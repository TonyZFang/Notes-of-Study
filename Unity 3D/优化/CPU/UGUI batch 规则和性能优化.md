UGUI batch 规则和性能优化

1.名词
　　1）Batch 单词 google 翻译的解释是：批量、批次。
　　2）Stats中的Batches参数：意思是batching后产生的批次数。（其实每一个批次就会调用一次DrawCall）
　　3）Unity官方文档中的batching是指batch操作和batching技术的称呼。（这里的batching是指“将符合条件的mesh通过CPU变换到统一的世界坐标中”这个操作，也就是合并mesh为一个批次的意思）
2.UGUI 中所有显示物体的网格都是自己填充的，例如Image，UGUI会自动生成四个顶点，和代表两个三角形的顶点索引。所有显示物体如果不设置它的material都会使用同一个默认材质（shader：“UI/Default”,color:RGBA(1,1,1,1),pass count:1,render queue:3000）
3.所以，根据官方文档DrawCallBatching可知，UGUI非常符合DynamicBatching。但是有一点是不符合的那就是UI的图片，所以我们可以把图片打成一张大图集来达到DynamicBatching的效果。这样就能减少batching后的batches个数，也就减少了DrawCall次数。
4.因为UI部分涉及的图片比较多，如果打一个图集在提交到GPU的时候会占用很大的内存，所以一般UI图集都会根据一定的规则进行，texture atlasing。
5.所以一般我们看到的UGUI优化方案，都是基于这些点来说的。这些知识点明白了，才知道为什么要做texture atlasing，为什么要分层，什么时候要合并等等。

UGUI排序规则

1.相邻的层级如果它们的texture在一个atlas中，就会被batching到一起。
2.在同一个层级区间并且没有交叉区域的物体（遮挡会按组件的transform的矩形区域计算），如果texture在一个atlas中，就会被batching到一起。

我们要做的事

1.对于atlas总来的来说就是让同一时刻，UI中平均包含的atlas尽可能少。
2.经常用的图片要打成一个atlas,其他atlas按功能划分。比如，背包界面，除了公用图集中出现的元素以外，所有出现的图片都打成一个atlas。
3.可重用的组件尽量在前期就都考虑到，因为到后期可能会产生混乱，同一个按钮在A界面出现也在B界面出现。（当然除了为了减少draw call这么做以外）。
4.如果在某种情况下对顶点数限制不高，可以考虑将图片做成对称的，然后用原图的一半拼出想要的效果。
5.对于drawcall来说，我们UI中有N层，N越大越靠前，会遮住后面的组件。我们从0到N数，然后看每个组件的材质，我们要尽量让这个材质变化的次数减少。
6.相同图集的组件:在不影响最终效果的情况下尽量使得相同图集的组件相邻。
7.相同字体的文本控件在不影响最终效果的情况下劲量使得相同字体的组件相邻。
8.对于不同的图集尽量不要去穿插，字体也是一样（纵向交叉，Herichy中的顺序）。
10.如果可能的话可以把层叠交叉的文字做到图片中。
11.如果不用mask可以实现的功能尽量不用。Mask组件用到了透明，所以他会被绘制两次。所以，带mask的组件不适用dynamic batching的规则。

Untiy Documentation:https://docs.unity3d.com/2018.1/Documentation/Manual/DrawCallBatching.html
原文链接:https://www.cnblogs.com/fly-100/p/5488757.html
