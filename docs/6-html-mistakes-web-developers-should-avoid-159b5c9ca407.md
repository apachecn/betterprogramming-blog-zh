# Web 开发人员应该避免的 6 个 HTML 错误

> 原文：<https://betterprogramming.pub/6-html-mistakes-web-developers-should-avoid-159b5c9ca407>

## 因为 HTML 也需要你的关注

![](img/0968e5f350bffdd00d64510c637c8453.png)

由[拍摄的照片](https://unsplash.com/@jacksonsophat?utm_source=medium&utm_medium=referral)

在网络世界里，有一句谚语:

> " HTML 是身体，JavaScript 是网络应用的大脑."

你可能太专注于训练和开发你的应用程序的工作大脑，以至于忽略了身体的重要性。说实话，我也遵循了同样的方法。

当有人想成为一名 web 开发人员时，我们作为有经验的专业人士建议他们应该学习 JavaScript、TypeScript 等。几乎每次我们忽略或忘记告诉他们 HTML 是他们应该开始的东西。因为即使当我们开始开发一个 web 应用程序时，我们首先编写 HTML，然后我们转向 JavaScript、TypeScript 等等。

HTML 不是很难学习或理解的东西。但是，由于这种印象，我们对 HTML 掉以轻心，并在开发过程中犯错误。

在本文中，我们将讨论一些在编写应用程序结构时应该避免的错误。

# 1.不关闭标签

我们知道 HTML 都是关于标签的。HTML 中有两种类型的标签:一种是我们必须关闭的标签，另一种是我们不必关闭的标签。但是不想养成不关闭标签的习惯。

```
**Bad Practice:**<ol> <li>Steve Jobs <li>Bill Gates <li>Elon Musk
```

这个小错误会导致代码中断或无法正常运行，所以我建议在需要的地方为所有 HTML 元素设置结束标记。

```
**Good Practice:**<ol> <li>Steve Jobs</li> <li>Bill Gates</li> <li>Elon Musk</li></ol>
```

# 2.未写入 Alt 属性

web 用户面临的一个常见问题是无法在 web 应用程序上看到图像。原因可能是任何事情:缓慢的互联网，静态资产，如图像在指定的位置不可用，等等。

显示空白区域而不是实际图像绝对不是好的用户体验。用户应该知道在屏幕上的那个地方他们能期待什么。

如果图像不可用，我们可以使用`alt`属性为图像定义一个替换文本。

```
**Bad Practice:**<img src="dogecoin.png"> **Good Practice:**<img src="dogecoin.png" alt="A cryptocurrency coin, popularized by Elon Musk">
```

替代文本有助于提高可访问性、主题相关性，也有助于你在谷歌图片中的排名。

# 3.缺少文档类型

我们知道我们的 web 应用不仅仅是 HTML、CSS 或 JavaScript。它实际上是所有这些的组合。所以我们的浏览器必须相应地处理它们。

现在的问题是，浏览器如何决定它是不是 HTML 文档？答案是，用`doctype`。

`doctype` or 声明不是 HTML 标签。它实际上是发送给浏览器的关于它应该期望什么样的文档类型的信息。

```
**Bad Practice:**<html>
    <head>
        <title>Web Page Title</title>
    </head>
    <body>
        Web Page Content....
    </body>
</html>
```

每个 HTML 页面都必须有一个`doctype`。

```
**Good Practice:**<!doctype html>
<html>
    <head>
        <title>Web Page Title</title>
    </head>
    <body>
        Web Page Content....
    </body>
</html>
```

在 HTML 5 中，声明很简单:

```
<!doctype html>
```

在旧的文档(HTML 4)中，声明更加复杂，因为声明必须引用一个 [DTD(文档类型定义)](https://www.w3schools.com/xml/xml_dtd_intro.asp)。

```
<!DOCTYPE HTML PUBLIC “-//W3C//DTD HTML 4.01 Transitional//EN” “http://www.w3.org/TR/html4/loose.dtd">
```

> *提示:* `*<!DOCTYPE>*` *声明不区分大小写。所有这些声明都是有效的。*
> 
> *<！DOCTYPE html >*
> 
> *！DocType html >*
> 
> *！doctype html>*
> 
> *！doctype html>*

# 4.内嵌样式

老实说，我们对干净代码原则有多认真并不重要；不知何故，我们最终使用内联 CSS。但是我们应该永远避免这种做法。

内联样式可能有其用途，但它们不是维护和样式化 web 应用程序的最佳方式。内嵌样式不会将内容和设计分开。内联样式与嵌入式字体和现代开发人员抱怨的其他笨重的设计标签完全一样。它们违背了所有的最佳实践。

```
**Bad Practice:**<h2 style="color: #000">Dark Text</h2> **Good Practice:**<h2 class="text-dark">Dark Text</h2><style>
    .text-dark {
        color: "#000";
    }
</style>
```

# 5.使用多个换行符

如果你想在元素之间留一些空隙，你可能会使用换行标签
。但这太过时了。

换行符标签
不应该用来在元素之间制造间隙，而是将文本分割成单独的段落。

```
**Bad Practice:**This is a line
<br>
<br>
This is another line **Good Practice:**<p>This is a line</p>
<p>This is another line</p>
```

# 6.不要使用**和**

***和*分别用于加粗和斜体，但语义不正确。我们应该将这些标签分别替换为**和*。*******

*虽然**和*在 HTML 中仍然可用，并且如果你使用它们，你不会得到任何错误，但是不推荐使用它们。所以使用**和*来代替，因为它们在语义上更正确。*******

```
***Bad Practice:**<p>
    <b>Note: </b>Our website is under under construction currently.   <i>We will be back soon.</i>
</p> **Good Practice:**<p>
    <strong>Note: </strong>Our website is under under construction currently. <em>We will be back soon.</em>
</p>*
```

*如果使用 SonarQube 之类的代码质量检查工具，使用**或*标签就会报错。所以尽量避开他们。****

*这就是这篇文章的内容。我希望它对你有益。如果你认为我们在编写 HTML 时应该避免什么，请告诉我。*

*感谢阅读！*