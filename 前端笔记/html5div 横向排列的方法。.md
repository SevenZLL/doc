# html5:div 横向排列的方法。

div 横向排列的方法。

以下面这组 div 为例，wrap 的高度由内容撑开

<div id="wrap">
    <div id="div1">div1</div>
    <div id="div2">div2</div>
    <div id="div3">div3</div>
</div>
平时是这样的，上下排列~



float 浮动
#div1{
    float: left;
}
#div2 {
    float: right;
}
#div3 {
    float: right;
}


然后这样了



成功横向排列了，但是有几个问题~

float 的特点：

多个 div 右浮动时，顺序会颠倒，请注意看 div2 和 div3，可以通过将它们再用一个 div 包起来，然后对它们设置左浮动，对父 div 设置右浮动来解决。

脱离文档流，若父元素高度由内容撑开，那么就撑不开，从图中可以看到 wrap 没了，因为高度变为了 0，可通过清浮动来解决。

文字会环绕在浮动元素周围，图中未表现出来。

不能换行，图中未表现出来。
inline-block 行块标签
#div1, #div2, #div3{
    display: inline-block;
}


横排成功~但同样有些问题：

inline-block 特点：

元素间会有空白。这个空白其实是空白符，因为 inline-block 会使元素在行内排列，也就是跟文字在一起排列，而我们源代码中 div 和 div 之间的空格、Tab、换行符在浏览器里会被合并成一个空白符，所以就会出现缝隙，常见的解决方案有：

通过给父元素设置 font-size: 0; ，使空白符不可见。但会导致子元素中继承的字体大小也为 0，解决方案： 
可以明确子元素内字体大小的，为其单独设置文字大小。
可以使用 rem 作为字体大小单位来继承 HTML 根元素的字体大小属性。
在源代码里把前一个 div 的结束标签和后一个 div 的开始标签贴在一起。可读性极差，丑拒。

<div id="wrap">
    <div id="div1">div1
    </div><div id="div2">div2
    </div><div id="div3">div3</div>
</div>
不用 inline-block，嘿嘿~
可以换行，如下图


flex 弹性盒模型
最爱的解决方案，给父元素设置 display: flex; 即可。

#wrap{
    display: flex;
}
效果图：



还可以通过 justify-content 属性调整子元素的水平对齐方式：

#wrap{
    display: flex;
    justify-content: flex-start;
}
flex-start：

默认，图同上。

flex-end：



center：



space-around：



space-between：



不过当父元素宽度不够时， flex 默认是不会换行的，而是会等比例压缩，缩放比例 flex-shrink 属性或复合属性 flex 相关。



通过 flex-wrap 属性可以使其换行，该值有三个取值 nowrap、 wrap、 wrap-reverse，默认为 nowrap。

#wrap{
    display: flex;
    flex-wrap: nowrap;
}
nowrap

默认，图略。

wrap



wrap-reverse



flex 使用不再深入探讨，推荐阮一峰写的教程 Flex 布局教程：语法篇
————————————————
版权声明：本文为CSDN博主「hunhun1122」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hunhun1122/article/details/78591103