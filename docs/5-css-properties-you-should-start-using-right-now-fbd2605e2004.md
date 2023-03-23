# 你应该马上开始使用的 5 个 CSS 属性

> 原文：<https://betterprogramming.pub/5-css-properties-you-should-start-using-right-now-fbd2605e2004>

## 很好地了解 CSS 就接近于魔术。学会使用它的力量

![](img/37a40a49958e919dd16e72ef5fc1a137.png)

罗伯特·卡茨基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我每天都在使用 CSS，并且相信我对它相当在行。尽管如此，它的力量偶尔会让我感到惊讶。以下是我最近发现的改变我写 CSS 方式的 5 个 CSS 属性。希望对你也有帮助！

# 1.强调色

使用表单是前端工作的一个重要部分。问题是表格很复杂。你尝试过自己设计复选框或单选按钮的样式吗？它很快变得一团糟，因为你必须为不同的浏览器和状态应用不同的风格。因此，您通常最终会使用其他人制作的定制组件，这通常是以可访问性为代价的。

如果您可以使用原生 HTML 输入，但改变它们的颜色，使其在不同浏览器之间更加生动和一致，会怎么样？你可以的！

为此，使用`accent-color`属性——它允许您更改单选按钮、复选框、进度条和范围输入的颜色。

现在你可以只用一行 CSS 就做出漂亮的输入，是不是很棒？

## 浏览器支持

目前，Firefox、Chrome、Edge 和 Opera 都支持`accent-color`。Safari 只在技术预览版支持。

# 2.块大小和内联大小

设置元素的宽度和高度通常很简单。但是你有没有注意到如果你需要让一些文本垂直而不是水平放置会发生什么？您可以使用`writing-mode`轻松设置，但是宽度和高度突然与文本流不匹配。

幸运的是，您可以使用`block-size`和`inline-size`而不是元素的高度和宽度来修复它。

对于标准的、水平取向的元素，`block-size`与元素的高度相关，而`inline-size`与元素的宽度相关。

对于垂直方向的元素，`block-size`与元素的宽度相关，而`inline-size`与元素的高度相关。

我知道这可能很令人困惑，所以看看下面的钢笔，看看他们两个在行动。

## 浏览器支持

目前所有现代浏览器都支持`block-size`和`inline-size`。IE11 不支持它们。

# 3.插入物

速记属性很棒。写起来要简单明了得多:

```
.box {
  padding: 10px;
}
```

比:

```
.box {
  padding-top: 10px;
  padding-right: 10px;
  padding-bottom: 10px;
  padding-left: 10px;
}
```

但是您知道定位元素也有一个简写属性吗？该属性名为`inset`，它允许您在一个声明中设置`top`、`right`、`bottom`和`left`，如下所示:

```
.box {
  inset: 10px;
}
```

挺有用的吧？

## 浏览器支持

现在`inset`被所有现代浏览器支持。IE11 不支持。

# 4.列表样式

您可能不知道，使用`list-style`对列表进行样式化是相对容易的。这是一个允许你设置三个不同属性的简写:`list-style-type`、`list-style-image`和`list-style-position`。

最强大的属性是`list-style-type`。它设置列表元素标记类型。这听起来可能不那么令人印象深刻，但 CSS 提供了几十种预定义的标记，从方块、罗马字母到最奇特的语言。您甚至可以使用`@counter-style` CSS at-rule 定义您的自定义标记！

`list-style-image`属性允许您将图像用作标记，而`list-style-position`设置标记相对于列表项的位置。

要查看您可以使用列表样式做的一些很棒的事情，请查看这支笔。

## 浏览器支持

所有的浏览器都支持列表样式属性，但是对于`list-style-type`的一些值有一些限制。查看 [MDN 页面](https://developer.mozilla.org/en-US/docs/Web/CSS/list-style-type)了解详情。

# 5.对象匹配

使用图像作为元素背景的好处在于，您可以使用`background-size`属性来设置容器中图像的大小。但是您知道您可以使用 CSS 来设置如何调整`<img>`或`<video>`元素的大小以适合它们的容器吗？

`object-fit`属性的工作方式与`background-size`相似。可以设置为五个值:`contain`、`cover`、`fill`、`none`、`scale-down`。

检查这支笔，看看`object-fit`的作用。

## 浏览器支持

现在所有现代浏览器都支持`block-size`和`inline-size`。IE11 不支持它们。

今天就到这里吧！你最喜欢的，不太为人所知的 CSS 属性有哪些？