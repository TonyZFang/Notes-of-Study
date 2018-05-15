Unity Shader-后处理：简单的颜色调整（亮度，饱和度，对比度）

一.概念介绍

我们在做游戏的时候，虽然现在有了Unity等引擎，不用我们自己处理一些繁琐的东西，但是不管怎么样，最后显示在屏幕上的还是一些RGB的像素信息，了解这些基本的概念，肯定对我们做游戏有更大的帮助。

1.颜色模型的概念
既然是校正屏幕的颜色，我们有必要了解一下我们要校正的这几个属性的概念。这里就不得不提到我们常用的颜色定义方式，RGB颜色模型和HSV颜色模型。

1.1RGB颜色模型
RGB颜色模型也就是我们最常用的三原色，红绿蓝。RGB颜色模型在混色时属于加法混色，RGB中每种颜色数值越高，色彩越明亮。RBG为（0,0,0）时为黑色，RGB为（255,255,255）时为白色。计算机在处理颜色信息时一般都采用RGB颜色模型，可以很精确地表示某种颜色。

1.2HSV颜色模型

RGB颜色模型对于计算机来说很容易计算，但是并不适合人类理解，于是就有了HSV颜色模型，所谓HSV代表的是Hue（色相），Saturation（饱和度），Value（色调）也有一种说法是HSB模型，B代表的是Brightness（明度）。当然，还有一些说法，比如HSL模型，L代表的是Lightness（亮度）。HSV模型使用一个圆锥形坐标系，顶面对应的V（Value色调）为1，表示颜色较亮，底面的Value为0，表示颜色较暗；而H（Hue色相）是由绕着V轴的旋转角度给定，从红色开始逆时针方向计算，红色对应0度，绿色对应120度，蓝色对应240度；S（Saturation饱和度）由模型的半径来代表，由内向外Saturation逐渐增大，圆心处为0，边缘处为1。

2.亮度，饱和度，对比度，灰度的概念

2.1亮度
图像中RGB值的大小，RGB各个值越大，那么亮度越亮，越小，亮度越暗。比如我们要增加亮度，那么直接增加RGB值即可。

2.2饱和度
指的是颜色的纯度。一般用彩度除以明度，表征彩色偏离同亮度灰色的程度。简单来说，当颜色越偏向某个值，即越偏离灰度，饱和度越大；当颜色越偏向灰度，饱和度越小。
下面是百度百科关于饱和度的一段定义：
饱和度是指色彩的鲜艳程度，也称色彩的纯度。饱和度取决于该色中含色成分和消色成分（灰色）的比例。含色成分越大，饱和度越大；消色成分越大，饱和度越小。纯的颜色都是高度饱和的，如鲜红，鲜绿。混杂上白色，灰色或其他色调的颜色，是不饱和的颜色，如绛紫，粉红，黄褐等。完全不饱和的颜色根本没有色调，如黑白之间的各种灰色

2.3对比度
指的是一幅图像中明暗区域最亮的白和最暗的黑之间不同亮度层级的测量，差异范围越大代表对比越大，差异范围越小代表对比越小。一般来说对比度越大，图像越清晰醒目，色彩也越鲜明艳丽；而对比度小，则会让整个画面都灰蒙蒙的。

2.4灰度
灰度使用黑色调表示物体,即用黑色为基准色，不同的饱和度的黑色来显示图像。 每个灰度对象都具有从 0%（白色）到100%（黑色）的亮度值。

了解了一些基本的色彩概念，我们就可以开始进行处理了。首先我们看一下Unity后处理效果的原理。

二.Unity屏幕后处理原理

所谓屏幕后处理，简单来说就是渲染流水线的最后阶段，对由整个场景生成的一张图片进行处理，比如HDR，运动模糊等等效果，通过屏幕空间的后处理，可以整体改变整个游戏的风格或者效果。所以，要制作屏幕后处理，我们需要两样东西，一个是用于渲染后处理效果的shader，而另一个是我们需要调用这个渲染的脚本，好在Unity为我们提供了相关的功能

1.OnRenderImage函数
该函数在MonoBehaviour中提供，该函数在所有渲染完成后才进行调用，也就是我们上文所说的生成了一张场景图片，函数的原型如下：
void OnRenderImage(RenderTexture sourceTexture,RenderTexture destTexture);

RenderTexture表示的是渲染纹理，我们渲染物体并不是仅仅渲染在屏幕空间，也可以将物体渲染到特定纹理上，也就是RenderTexture。sourceTexture就是我们渲染的场景图片，而destTexture是目标渲染纹理。我们可以在这个函数中进行相关的后处理效果，使用带有后处理效果shader的材质将场景内容重新渲染。

2.Graphics.Blit函数
该函数是Graphics的函数，用于将源纹理拷贝到目标纹理，函数原型如下：
public static void Blit(Texture source,RenderTexture dest);
public static void Blit(Texture source,RenderTexture dest, Material mat, int pass = -1);
public static void Blit(Texture source,Material mat, int pass = -1);

source是源纹理，dest是目标纹理，当dest为null时，直接将源纹理拷贝到屏幕；mat是拷贝时使用的材质，也就是我们后处理时使用的材质，Unity会使用该材质将源纹理进行处理拷贝给目标纹理，pass是使用的材质shader所使用的pass，我们知道，一个shader中可能有多个pass，使用哪个pass进行处理就可以从该参数传入，当然，默认为-1时表示所有的pass都会执行。

在了解了Untiy的后处理流程后，我们就可以着手写我们的亮度对比度饱和度调整后处理了。

三.后处理效果代码

后处理效果需要两部分，分别是脚本部分和shader部分，我们分别来看一下。

1.脚本部分
后处理脚本主要做的是两件事，第一件是获取需要的shader，生成材质，第二件是通过OnRenderImage使用材质处理屏幕效果。第一步具有一些普遍性，不管是什么后处理效果，都要有这一步相同的操作，所以我们将该步骤抽离出来，创建一个后处理效果的基类PostEffectBase，代码如下：

using UnityEngine;
using System.Collections;

//非运行时也触发效果
[ExecuteInEditMode]
//屏幕后处理特效一般都需要绑定在摄像机上
[RequireComponent(typeof(Camera))]
//提供一个后处理的基类，主要功能在于直接通过Inspector面板拖入shader，生成shader对应的材质
public class PostEffectBase : MonoBehaviour {

    //Inspector面板上直接拖入
    public Shader shader = null;
    private Material _material = null;
    public Material _Material
    {
        get
        {
            if (_material == null)
                _material = GenerateMaterial(shader);
            return _material;
        }
    }

    //根据shader创建用于屏幕特效的材质
    protected Material GenerateMaterial(Shader shader)
    {
        if (shader == null)
            return null;
        //需要判断shader是否支持
        if (shader.isSupported == false)
            return null;
        Material material = new Material(shader);
        material.hideFlags = HideFlags.DontSave;
        if (material)
            return material;
        return null;
    }
}

然后，我们以后所有的后处理效果脚本都可以继承该类PostEffectBase，就都自动具有了通过shader生成后处理材质的功能。
接下来就是我们这一篇的亮度，饱和度，对比度调整的脚本，脚本很简单，主要的功能就在于设置几个参数，覆写OnRenderImage函数后将参数实时传入shader，然后通过Blit函数完成后处理效果，代码如下：

using UnityEngine;
using System.Collections;

//继承自PostEffectBase
public class ColorAdjustEffect : PostEffectBase {

    //通过Range控制可以输入的参数的范围
    [Range(0.0f, 3.0f)]
    public float brightness = 1.0f;//亮度
    [Range(0.0f, 3.0f)]
    public float contrast = 1.0f;  //对比度
    [Range(0.0f, 3.0f)]
    public float saturation = 1.0f;//饱和度

    //覆写OnRenderImage函数
    void OnRenderImage(RenderTexture src, RenderTexture dest)
    {
        //仅仅当有材质的时候才进行后处理，如果_Material为空，不进行后处理
        if (_Material)
        {
            //通过Material.SetXXX（"name",value）可以设置shader中的参数值
            _Material.SetFloat("_Brightness", brightness);
            _Material.SetFloat("_Saturation", saturation);
            _Material.SetFloat("_Contrast", contrast);
            //使用Material处理Texture，dest不一定是屏幕，后处理效果可以叠加的！
            Graphics.Blit(src, dest, _Material);
        }
        else
        {
            //直接绘制
            Graphics.Blit(src, dest);
        }
    }
}
这样，我们的后处理脚本就完成了。涉及到以下几个知识点：
1.可以通过[Range(min，max)]来控制Inspector面板中的值，限定其范围，并提供滑动条控制。
2.OnRenderImage函数每帧渲染完全部内容后执行，我们在每一帧设置Material的各项参数，通过Material.SetXXX("name",value)可以向shader中传递各种参数。
3.各种后处理效果可以叠加，这里的dest并不一定就是屏幕。不过后处理是很耗费性能的，一方面是pixel shader全屏幕overdraw，另一方面，一个rendertexture的内存占用很大，尤其是大分辨率手机上，多个后处理效果可能造成内存耗尽，程序崩溃。

2.shader部分


终于步入正题了，下面看一下后处理效果的shader。由于后处理效果是对于一个场景的渲染图进行处理，所以vertex shader基本没有什么好说的，大部分后处理都是基于pixel shader。先整理一下思路：
最简单的是亮度，我们可以直接在采样texture后乘以一个系数，达到放大或者缩小rgb值的目的，这样就可以调整亮度了。
其次是饱和度，饱和度是离灰度偏离越大，饱和度越大，我们首先可以计算一下同等亮度条件下饱和度最低的值，根据公式：gray = 0.2125 * r + 0.7154 * g + 0.0721 * b即可求出该值（公式应该是一个经验公式），然后我们使用该值和原始图像之间用一个系数进行差值，即可达到调整饱和度的目的。
最后是对比度，对比度表示颜色差异越大对比度越强，当颜色为纯灰色，也就是（0.5,0.5,0.5）时，对比度最小，我们通过在对比度最小的图像和原始图像通过系数差值，达到调整对比度的目的。代码如下：

//shader的目录
Shader "Custom/ColorAdjustEffect"
{
    //属性块，shader用到的属性，可以直接在Inspector面板调整
    Properties
    {
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        _Brightness("Brightness", Float) = 1
        _Saturation("Saturation", Float) = 1
        _Contrast("Contrast", Float) = 1
    }

    //每个shader都有Subshaer，各个subshaer之间是平行关系，只可能运行一个subshader，主要针对不同硬件
    SubShader
    {
        //真正干活的就是Pass了，一个shader中可能有不同的pass，可以执行多个pass
        Pass
            {
                //设置一些渲染状态，此处先不详细解释
                ZTest Always Cull Off ZWrite Off

                CGPROGRAM
                //在Properties中的内容只是给Inspector面板使用，真正声明在此处，注意与上面一致性
                sampler2D _MainTex;
                half _Brightness;
                half _Saturation;
                half _Contrast;

                //vert和frag函数
                #pragma vertex vert
                #pragma fragment frag
                #include "Lighting.cginc"

                //从vertex shader传入pixel shader的参数
                struct v2f
                {
                    float4 pos : SV_POSITION; //顶点位置
                    half2  uv : TEXCOORD0;    //UV坐标
                };

                //vertex shader
                //appdata_img：带有位置和一个纹理坐标的顶点着色器输入
                v2f vert(appdata_img v)
                {
                    v2f o;
                    //从自身空间转向投影空间
                    o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                    //uv坐标赋值给output
                    o.uv = v.texcoord;
                    return o;
                }

                //fragment shader
                fixed4 frag(v2f i) : SV_Target
                {
                    //从_MainTex中根据uv坐标进行采样
                    fixed4 renderTex = tex2D(_MainTex, i.uv);
                    //brigtness亮度直接乘以一个系数，也就是RGB整体缩放，调整亮度
                    fixed3 finalColor = renderTex * _Brightness;
                    //saturation饱和度：首先根据公式计算同等亮度情况下饱和度最低的值：
                    fixed gray = 0.2125 * renderTex.r + 0.7154 * renderTex.g + 0.0721 * renderTex.b;
                    fixed3 grayColor = fixed3(gray, gray, gray);
                    //根据Saturation在饱和度最低的图像和原图之间差值
                    finalColor = lerp(grayColor, finalColor, _Saturation);
                    //contrast对比度：首先计算对比度最低的值
                    fixed3 avgColor = fixed3(0.5, 0.5, 0.5);
                    //根据Contrast在对比度最低的图像和原图之间差值
                    finalColor = lerp(avgColor, finalColor, _Contrast);
                    //返回结果，alpha通道不变
                    return fixed4(finalColor, renderTex.a);
                }
                    ENDCG
        }
    }
    //防止shader失效的保障措施
    FallBack Off
}

四.效果展示

完成shader和后处理脚本后，我们可以创建一个场景，在场景的MainCamera下挂在上该脚本，然后把ColorAdjustEffect的shader赋给脚本的shader槽，










原文链接：https://blog.csdn.net/puppet_master/article/details/52423905
