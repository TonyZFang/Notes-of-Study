关于为什么保存Transform等引用效率会更高

正常来说，大部分同学一般get transform都直接gameobject.transform使用。但往往，你会发现有些人会将transform引用保存起来，例如：

private Transform myTransform;
void Awake() {
    myTransform = transform;
}
然后使用myTransform替代this.transform。如果你不知道u3d内部实现获取方式你肯定会以为这人脑抽水了，有直接的不用，还自己保存起来。
this.transform并不是变量，而是一个get/set属性（property）。
using System;
using System.Runtime.CompilerServices;
using UnityEngineInternal;
namespace UnityEngine
{
    public class Component : Object
    {
        public extern Transform transform
        {
            [WrapperlessIcall]
            [MethodImpl(MethodImplOptions.InternalCall)]
            get;
        }
    }
}

调用this.transform实际上是一个调用intenal method的过程（这是用C/C++写的，不是MONO的）。值得注意的是这个调用方法略慢，因为你需要调用外部的CIL（aka interop），花费了额外的性能。

GetComponent是this.transform的10倍消耗时间。
this.transform是保存了引用myTransform的1.5倍的消耗时间。(因为新版优化了不少)

实际上：

如果你是偶尔调用一下transform的话，那就不要保留它的引用了，直接this.transform。

如果是Update中，每一帧都要改变的话，还是保留一下this.transform的引用吧。毕竟倘若一大堆东西的话，能快不少呢。

原文链接：http://blog.sina.com.cn/s/blog_5b6cb9500101fkal.html
