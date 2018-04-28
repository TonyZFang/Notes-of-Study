Unity富文本详解

(如果使用时出现问题，首先核对标签里是否有多余的空格，标签属性和赋值之间不能有空格，否则标签无效。颜色数值赋值不要忘了符号#。)
 Rich Text用于GUI元素和文本网格可以结合多种字体类型和大小。 GUIStyle, GUIText 和 TextMesh类都有富文本设置，能指示unity寻找文本内含有的标记标签。Dubug.Log也可以用这些标记标签来增强代码报告错误的能力。这些标签不显示，而是显示应用到文本的风格变化。

标记格式
该标记系统的灵感来源于HTML但不打算要严格兼容标准的HTML。其基本思路是，一对匹配的标签里面可以包含一部分文字
We are <b>not</b> amused  
示例所示，标签只是“尖括号”和文本，< 和 >。标签内的文本表示的名字（在这个示例中就是b）注意：这个结尾部分的标签和开头那个名字一样但带有/符号。该标签不直接显示给用户但是诠释为它们扩起来的文字风格，即把not用b括起来，在例子中的b标签在单词“not”上应用粗体字。

已标记的文本块（包括标签括入的）被作为一个元素 如<b>not</b> 。

嵌套元素
通过嵌套一个元素到另一个元素里面来应用多种样式到一个文本块这个是可以的。
We are <b><i>definitely not</i></b> amused
注意结束标签的顺序，与起始标签顺序相反。这样做的原因是帮助你更清晰的考虑内部标签不必跨越整个文本的最外层元素。（标签不能交叉包含，只能一对标签包含另一对；而一对标签不能只包含另一对标签的一部分。）
We are <b>absolutely <i>definitely</i> not</b> amused    

标签参数
某些标签有一些简单的效果，开始标签可以有一些参数，例如，color标记需要知道应用哪个颜色。像这个在开始标签应用参数：
We are <color=green>green</color> with envy
注意结尾标签不包括参数素值。参数值可以由双引号包起来，但是这不是必需的。

支持的标签
下面的列出所有unity支持的标签样式。、

b
呈现粗体文本。
 We are <b>not</b> amused

i
呈现斜体文本。
 We are <i>usually</i> not amused

size 大小
根据给定的参数值，以像素为单位设定的文字大小。
 We are <size=50>largely</size> unaffected
虽然这个标签是Debug.Log看得到的，如果设置过大，你将会发现窗口栏和控制台的大小看起来很奇怪。

color 颜色
根据参数值设置文本颜色，颜色值可以用传统的HTML格式指定
#rrggbbaa
字母对应于对十六进位数字，表示红色，绿色，蓝色和alpha（透明度）值的颜色。例如，青色完全不透明：
<color=#00ffffff>...
另一种选择是使用的颜色名称(请参考Unity Documentation里支持的颜色名称)。这是比较容易理解且自然，颜色的范围是有限的，总是假定完全不透明。
<color=cyan>...

material 材质
这仅用于文本网格并渲染具有材料参数所指定的一段文字。该值是文本网格显示在检视面板材质数组的索引。
We are <materialmaterial=2>texturally</material> amused   

quad 嵌入
这仅用于文本网格和在文本内嵌图像。它的参数，material是用于图片，图片的高度以像素为单位，另外四个标参数表示一个用于显示图像的矩形区域。不像其他标签，quad不用环绕文字所以没有结束标签-斜杠符号放置在开始标签的结尾表明它是“自封闭的”。
<quad material=1 size=20 x=0.1 y=0.1 width=0.5 height=0.5 />
该行代码意思是材质的位置在渲染器的材质数组，同时设置图像高度为20像素。用于显示图像的矩形区域初始位置由X、Y、width和height的值提供，这些参数很适用于那些宽和高不成比例的图片。

Untiy Documentation:https://docs.unity3d.com/2018.1/Documentation/Manual/StyledText.html
原文链接：https://mp.weixin.qq.com/s/WyynaTLqzqmrxRqdG52efg
