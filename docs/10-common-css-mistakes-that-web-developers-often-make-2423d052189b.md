# Web 开发人员经常犯的 10 个常见 CSS 错误

> 原文：<https://betterprogramming.pub/10-common-css-mistakes-that-web-developers-often-make-2423d052189b>

## 目标不仅仅是编写有效的 CSS。编写高效且易于维护的 CSS 代码

![](img/a3234383ae0b206c5f18d4a544139eed.png)

图片提供:[马库斯·斯皮斯克](https://unsplash.com/@markusspiske)

层叠样式表(CSS)是维系网页的最重要的支柱之一。它可以让你在一个简单的 HTML 布局中加入风格和设计感。从字体系列和颜色到对齐和显示，它无所不包。

最重要的是，它无疑是最简单、最直观的语言之一。毕竟，有什么比通过一个简单的几乎用纯英语编写的属性列表来改变元素的特征更容易的呢？

尽管表面上看起来并不复杂，但 CSS 是一个复杂的系统，尤其是在大规模、高性能的层面上使用时。考虑到选择一个元素的众多方法，更不用说可以应用于它的属性的数量，以及当多个浏览器和设备进入画面时呈现方式的变化——很容易被 CSS 绊倒。

以下是大多数 web 开发人员会犯的一些常见错误，以及如何识别和避免这些错误可以帮助您编写更好、更高效的 CSS！

# 1.使用颜色名称而不是十六进制

当你说**的时候*的颜色:蓝色；你实际上是在告诉计算机显示它认为蓝色的任何色调。通过这样做，您可以让浏览器控制您的网页应该如何显示，作为一名开发人员，这是您永远不应该做的事情。通过模糊地将颜色命名为蓝色，它很容易与您想象中的蓝色不同，更糟糕的是，它还会因浏览器而异。***

使用十六进制值 ***例如:color:# 4169 E1；*** 因此成为所有开发者都应该采用的东西。它确保了特异性，受到所有浏览器的支持，并把控制权还给你，让你决定你的网页到底要如何显示。

*注意:查找和使用十六进制值的一个有效方法是首先输入与所需色调最接近的颜色名称，然后使用颜色滴管检查元素以找到十六进制值。*

# 2.硬编码 px 而不是相对单位

虽然有时必须使用绝对的 *px* 值，但您应该始终使用相对度量，例如 [***em***](https://en.wikipedia.org/wiki/Em_(typography)) 、**、 *%*** (百分比)、 ***rem*** (root-Em)，以及其他任何可能的度量。

这确保了网站根据用户选择的缩放级别和屏幕/浏览器大小按比例缩放。

所以，与其绝对地宣称尺寸，

```
p {
    font-size: **16px;**
    line-height: **20px;**
    margin-bottom: **8px;**
}
```

请改为这样做:

```
 p {
    font-size: **1rem;**
    line-height: **1.25em;**
    margin-bottom: **0.5rem;**
}
```

# 3.不使用字体后退

不管某个字体让你的页面看起来有多漂亮，或者有多吸引眼球，你都必须考虑到不是所有的字体类型都被所有的计算机支持。如果你使用了一些浏览器不支持的字体，这意味着你的网页可能没有你为所有用户设计的那样漂亮和醒目。

所以，在你使用了你最喜欢的字体之后，比如说***，****一定要列出后备字体，以防浏览器不支持。*

*与其写这个，*

```
*#selector {
  font-family: **Helvetica;**
}*
```

*通过字体回退扩展代码，例如:*

```
*#selector {
  font-family: **Helvetica, Arial, sans-serif;**
}*
```

*现在，即使浏览器不支持***【Helvetica】***，它也会退回到浏览器默认之前的第二首选选项 ***Arial*** 。*

# *4.不使用 CSS 快捷键*

*看看下面的 CSS:*

```
***font-family:** Helvetica;
**font-size:** 14px;
**font-weight:** bold;
**line-height:** 1.5;*
```

*使用字体的 CSS 简写，这可以很容易地压缩成一行:*

```
***font: bold 14px/1.5 Helvetica;***
```

*类似地，背景图像的属性列表如下:*

```
***background-image:** url(background.png);
**background-repeat:** repeat-y;
**background-position:** center top;*
```

*可以写成:*

```
***background: url(background.png) repeat-y center top;***
```

*我们为什么要这么做？原因很简单。它不仅减小了 CSS 文件的大小，还使样式表更容易阅读，更有条理。*

*这里有一个 CSS 短工 [**列表**](https://www.kobzarev.com/wp-content/uploads/cheatsheets/css/css-cheat-sheet.pdf) 帮助你写出更干净的代码。这一切都是为了让你的编码习惯根深蒂固，但是一旦你这样做了，就没有回头路了！*

# *5.过度限定选择器*

*就像所有其他过度的事情一样，过于具体是一件坏事。而且更多的时候，甚至没有必要。看看下面的 CSS:*

```
*header **#nav** **ul** li **a** {...}*
```

*首先，报头规范是绝对不必要的，因为具有最高特异性的ID 已经被使用*(ID 是唯一的并且只与一个元素相关联)。此外，无序列表(ul)中总是包含列表项(li)。因此，提及这一点变得毫无意义。同一个选择器现在可以写成:**

```
****#nav ul a {...}****
```

**对于 CSS，只有两个层次的特异性——特异性和不够特异性。包含所有这些额外的元素可能看起来“安全”,但实际上是不必要的，只会增加样式表的长度。**

**一般来说，你的选择器应该尽可能的短。要尽可能的具体，以保证它能正常工作。**

# **6.使用 ID 而不是类**

**反对使用 ID 的最有说服力的理由是，它比类具有更高的特异性，这就是为什么很难覆盖和扩展您的样式。一个类本身不能覆盖属于一个 ID 的样式。要“击败”ID，您需要更多的 ID 或者使用 ***！重要的*** ，它可以在你的样式表中开始**特异性战争**。**

**类选择器也可以用于同一个页面上的几个 HTML 元素，不像 id 是每个元素唯一的。能够重用样式是 CSS 的优势之一。**

**为了保持一致的约定，只使用类属性来定义样式和 id，同时集成与 Javascript 的交互性。**

# **7.不使用 CSS 重置**

**如果您曾经显示过没有 CSS 样式的 HTML 页面，您会知道 web 浏览器本身使用一些默认值作为后备来“样式化”页面。文本具有特定的字体大小和样式，边距和填充被设置为特定的值。**

**虽然这对不使用 CSS 的人来说是一件好事，但在将自己的样式放入页面时，首先重置这些值是很重要的。这些值因浏览器而异，因此 CSS 重置是确保所有样式统一有效的唯一方法。**

**这需要将所有 HTML 元素的所有样式重置为可预测的基线值。一旦你做到了这一点，你就可以将页面上的所有元素都设计成相同的样式。一张白纸。**

**一种更简单但不完整的方法是使用通用选择器重置边距和填充:**

```
***** {margin:0; padding:0;}****
```

**然而，为了完全重置，您可以使用 [**Eric Meyer reset**](http://meyerweb.com/eric/thoughts/2007/05/01/reset-reloaded/) (根据您的选择进行修改)，来重置列表项、链接和表格等元素的边框、下划线和颜色，以便您不会在 web 浏览器之间遇到意外的不一致。**

# **8.重复代码(冗余选择器和属性)**

**一般来说，在编码的时候重复自己并不是一个好的习惯。CSS 也不例外。看看下面的代码:**

```
****#selector-1** {
  font-style: italic;
  color: #e7e7e7;
  margin: 5px;
  padding: 20px
}
**.selector-2** {
  font-style: italic;
  color: #e7e7e7;
  margin: 5px;
  padding: 20px
}**
```

**更好的写法是将它们组合起来，用逗号(，)分隔选择器:**

```
****#selector-1, .selector-2** {
  font-style: italic;
  color: #e7e7e7;
  margin: 5px;
  padding: 20px
}**
```

**这不仅效率更高，还减少了维护时间和页面加载速度。**

# **9.没有把设计和布局分开**

**CSS 的工作是提供样式，HTML 的工作是提供结构。一般来说，HTML 应该以捕捉页面信息层次的方式编写，忽略任何设计问题。之后，可以添加 CSS 来使事情“看起来更好”**

**然而，虽然 HTML 提供了结构，但它不能总是将元素定位在您希望它出现的页面的确切位置，这是我们使用 CSS 来搭建页面布局的地方。一旦一个元素被放到页面上的正确位置，就很容易对其进行样式化，而不用担心显示和位置。这就是为什么**布局 CSS** 要和**设计 CSS** 分开的原因。**

**不是将布局和设计属性放在一起，**

```
****.article** {
  display: inline-block;
  width: 50%;
  margin-bottom: 1em;
  font-family: sans-serif;
  border-radius: 1rem;
  box-shadow: 12px 12px 2px 1px rgba(0, 0, 0, .2);
}
**.sidebar** {
  width: 25%;
  margin-left: 5px;
}*<div class="article"></div>
<div class="article sidebar"></div>***
```

**分离元素的设计和布局:**

```
****/* layout */**
.article, .sidebar {
  display: inline-block;
}
.article {
  width: 50%;
  margin-bottom: 1em;
}
.sidebar {
  width: 25%;
  margin-left: 5px;
}
 **/* display */**
.card {
  font-family: sans-serif;
  border-radius: 1rem;
  box-shadow: 12px 12px 2px 1px rgba(0, 0, 0, .2);
}*<div class="article card"></div>
<div class="sidebar card"></div>***
```

**这确保了**关注点的分离，**这是一个常见的软件工程原则，有助于保持我们的代码可维护和易于理解。**

# **10.编写无组织的 CSS**

**不要按照你的想法来编写你的风格，帮你自己一个忙，把你的代码组织得整整齐齐。这将确保下次您对文件进行更改时，您仍然能够浏览它。**

*   **给你的 CSS 加注释:一个很好的技巧是在你的样式表中的逻辑部分之间添加一个注释块，帮助你在浏览时快速定位不同的部分，或者甚至给你一些东西来搜索直接进入 CSS 的那个部分。你不需要注释 CSS 中的每一件事，因为大部分内容都是不言自明的。你应该评论的是你做出某个特定决定的原因。**
*   ****在样式表中创建逻辑部分:**最好先在样式表中创建所有的通用样式。这意味着所有的风格将普遍适用，除非你做一些特殊的元素。您通常会为**主体、p、h1、h2、h3、链接和表格设置规则。**在这之后，你可以有几个实用类，或者属性，你知道你会想要应用到许多不同的元素。最后，包括针对特定事物的 CSS，按照上下文、页面，甚至是使用它们的组件来分类。**

**另请参阅:**

**[](/5-mistakes-youre-making-as-a-beginner-in-programming-and-how-to-avoid-them-ef6bbfa919a0) [## 初学者在学习编程时应该避免的 5 个错误

### 犯错是旅程的一部分。重要的是尽快识别并纠正它们

better 编程. pub](/5-mistakes-youre-making-as-a-beginner-in-programming-and-how-to-avoid-them-ef6bbfa919a0) [](/11-must-have-chrome-extensions-web-developers-and-designers-3482a1ca6349) [## 网络开发人员和设计人员必备的 11 个 Chrome 扩展

### 有用的扩展，使您的任务更容易

better 编程. pub](/11-must-have-chrome-extensions-web-developers-and-designers-3482a1ca6349)**