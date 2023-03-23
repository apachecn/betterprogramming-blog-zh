# 22 个 CSS 技巧可以让你成为布局忍者

> 原文：<https://betterprogramming.pub/22-css-tricks-that-can-make-you-a-layout-ninja-452847fba639>

## 看看一些鲜为人知的 CSS 属性

![](img/b1dcbd157529259732d89589c3e059ba.png)

安迪·霍姆斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天我想告诉你几个 CSS 属性和值，它们在技术文献中很少被提及，但是在我看来，它们对提高 web 界面开发的速度和质量特别有意义。

讨论的许多特性都是实验性的。所有现代浏览器都支持其中的大部分。但是，如果您决定在生产中使用这些属性，请考虑访问[我可以使用](https://caniuse.com/)来确认它们是否受支持。

那么，你准备好开始一段奇妙的、几乎没有边界的 CSS 世界之旅了吗？我们开始吧！

# 网格+地点-项目

这种技术只需要两行代码就可以水平和垂直对齐项目。

`place-items`是`justify-items`和`align-items`的简写属性。

该属性可以同时应用于一个或多个(子)单元格。

# 弹性+利润

另一种水平和垂直对齐项目的现代方法是使用`display: flex`和`margin: auto`的组合。

公平地说，使用下面的代码片段也可以做到这一点:

# 柔性+间隙

既然我们在谈论`Flexbox`，值得一提的是，我们终于能够使用`gap`属性设置 flex 项目之间的间隙(我们确实需要这个):

# 内嵌-柔性

这个属性允许你创建具有`Flexbox`特性的内嵌元素。一个例子胜过千言万语:

# 列

这种技术允许您将文本拆分成列。`column-count`属性指定列数，`column-gap`设置列间距的大小，`column-rule`设置列间垂直线的样式。

`columns`是`column-count`和`column-width`的简写属性。

# 背景-重复

`background-repeat`属性设置用指定图像填充背景的顺序。“圆形”值在容器的整个宽度上均匀分布图像，而“空间”值在图像之间添加少量填充:

# 背景混合模式

`background-blend-mode`属性设置背景图像和颜色(或多个背景图像/颜色)相互混合的顺序。

可能的值:

*   `color`
*   `color-burn`
*   `color-dodge`
*   `darken`
*   `difference`
*   `exclusion`
*   `hard-light`
*   `hue`
*   `lighten`
*   `luminosity`
*   `multiply`
*   `overlay`
*   `saturation`
*   `screen`
*   `soft-light`

你用过 Photoshop 吗？那我想你明白这是怎么回事了。

假设我们有一个黑白图像，我们想用它作为背景。但同时，我们希望它是彩色的。如何才能实现这一点？

# 背景剪辑

`background-clip`属性定义了背景颜色或背景图像应该超出元素的填充多远。在我看来，`text`是这个属性最有趣的值:

# 过滤器

属性允许你对元素应用一些视觉效果。

可能的函数值:

*   `url()`
*   `blur()`
*   `brightness()`
*   `contrast()`
*   `drop-shadow()`
*   `grayscale()`
*   `hue-rotate()`
*   `invert()`
*   `opacity()`
*   `saturate()`
*   `sepia()`

为懒惰的人改变网站颜色主题(或方案):

[在 CSSgram](https://una.im/CSSgram/) 上，你会发现一个使用`filter`进行 Instagram 滤镜的例子。

# 投影

设置为`drop-shadow()`的`filter`属性与`box-shadow`属性不同，后者在应用效果方面类似，它允许您为图像本身(以 PNG 格式)添加阴影，而不是为图像所在的框添加阴影。

# 对象匹配

`object-fit`属性控制被替换元素的纵横比，例如`img`和`video`，如果它们有宽度或高度，以及缩放的过程。

例如，`scale-down`值允许您保持图像的纵横比，而不管框的大小如何:

`object-position`属性用于对齐框内任何选定的替换元素的内容。

# 光标

你知道吗，除了浏览器提供的光标图标(比如`cursor: pointer`)，我们还可以定义自己的图片和 SVG？

# 滚动行为

将`scroll-behavior`属性设置为`smooth`可以轻松实现页面部分之间的平滑滚动:

# 文本溢出

设置为`ellipsis`的`text-overflow`属性允许您在文本超出容器时将`…`添加到文本的末尾。

# 插入符号颜色

`caret-color`属性设置插入符号的颜色，插入符号是一个可见的标记(|)，指示下一个键入的字符将被插入的位置。

# @支持

`@supports`规则允许您在使用它们之前检查浏览器是否支持特定的一个或多个属性(或属性/值组合)。

# 风险值()

`var()`函数允许您使用自定义变量的值作为属性值。该函数的第二个可选参数是保留值。

# calc()

`calc()`函数用于指定使用尺寸、角度、时间或数字作为值的属性的计算值。这允许根据不同单位的加减来设置值。

通常，绝对定位的元素按如下方式水平和垂直对齐:

如果我们知道这样一个元素的尺寸，我们可以做以下事情:

# 属性()

使用`attr()`函数，您可以检索所选元素的属性值，并在样式中使用它。

用 CSS 创建工具提示:

# :目标

`:target`伪类允许您创建没有 JavaScript 也能工作的模态:

# *标记

我们曾经使用`list-style: none`移除列表标记，并使用`::before`或`::after`伪元素添加我们自己的标记。现在有一种更简单的方法可以做到这一点——我们可以使用`::marker`伪元素。

# *选择

`::selection`伪元素允许您设置文本选择的样式。

谢谢你的时间，祝你有美好的一天！