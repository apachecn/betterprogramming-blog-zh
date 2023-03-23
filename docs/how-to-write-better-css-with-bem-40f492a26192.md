# 如何使用块、元素和修饰符(BEM)编写更好的 CSS

> 原文：<https://betterprogramming.pub/how-to-write-better-css-with-bem-40f492a26192>

## 编写更干净、更清晰的样式表

![](img/261214ea40e6c06ed8b8814e6493aad3.png)

图片由[乔·汉科](https://commons.wikimedia.org/w/index.php?title=User:Joehanko&action=edit&redlink=1)在[维基共享资源](https://commons.wikimedia.org/wiki/File:Cascading_Style_Sheets.jpg)上提供

这篇文章和相应的视频是专门为那些想优化 CSS 的开发者准备的。对于你的队友和你自己来说，拥有可读的 CSS 是非常重要的。这将有助于你和你的团队在未来开发网站或应用程序。

在我看来，CSS 命名约定 Block-Element-Modifier([BEM](http://getbem.com/introduction/))是比以前更好地编写 CSS 类和选择器的完美方式。

如果你想要一个直观的例子来说明我如何将我已经开发的 CSS 转换成可读性更好的 BEM，请查看下面的视频。

如果你更喜欢阅读，请遵循下面的文字。

# **视频备注**

## **示例网站**

对于这个例子，我使用了一个 Bootstrap 主题——你可以[免费下载业务主题](https://www.quackit.com/html/templates/simple_website_templates.cfm)。

## **崇高的文字**

在这个编辑器中，我使用了 Sublime 文本编辑器。[可以免费下载](https://www.sublimetext.com)。如果你想成为一名真正的专业人士，请查看我们的朋友韦斯·博斯的[课程/书籍。](http://bit.ly/WESBOS-SUBLIME)

## **详细的边界元信息**

如果你想阅读 BEM 文档，请登录 [BEM 网站](http://getbem.com/introduction/)查看。

在我的视频中，我展示了 CSS 魔法网站上一篇关于 BEM 的[详细博文。](https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

让我们来看看代码。

# **1。HTML 和 CSS:我们如何学习它们**

当您学习编写 HTML 或 CSS 时，它可能看起来像这样:

## **HTML**

```
<nav>
   <ul>
    <li><a>Home</a></li>
    <li><a>About</a></li>
    <li><a>Blog</a></li>
  </ul>
</nav>
```

## **CSS**

```
nav {
  height: 60px;
  max-width: 1024px;
}

nav ul {
  margin: 0;
  padding: 0;
  text-align: center;
}

nav ul li {
  display: inline;
}

nav ul li a {
  display: inline-block;
  padding: .5em 1em;
}
```

## **分析 HTML 和 CSS**

如果我们分析 HTML 和 CSS，我们会看到以下内容:

*   仅由元素构成
*   没有类名
*   多级选择器

## **像这样写 HTML 和 CSS 你会发现的问题**

当我开始前端开发时，这是我被告知的编写方式。

也许你看不到问题，但在这篇文章的结尾，你肯定会知道。

你会发现的问题是:

*   如果你在别的地方使用同样的 HTML，事情会变得混乱，因为它会在任何地方反映出来。所以不具备可扩展性。
*   要覆盖样式，您必须编写非常具体的多级选择器，或者更糟的是，使用`!important`来覆盖
*   这将导致大量的大 CSS 文件的错误
*   CSS 选择器并没有解释在页面上哪里可以找到 HTML 元素。所以不是不言自明的。

# **2。稍微优化一下你的 HTML 和 CSS**

当我有了几年的前端开发经验后，我的 HTML 和 CSS 开始变成这样:

## **HTML**

```
<nav class=“navigation”>
   <ul>
    <li><a>Home</a></li>
    <li><a>About</a></li>
    <li><a>Blog</a></li>
  </ul>
</nav>
```

## **CSS**

```
.navigation {
  height: 60px;
  max-width: 1024px;
}

.navigation ul {
  margin: 0;
  padding: 0;
  text-align: center;
}

.navigation li {
  display: inline;
}

.navigation a {
  display: inline-block;
  padding: .5em 1em;
}
```

## **分析 HTML 和 CSS**

如果您分析更优化的 HTML 和 CSS，您会看到以下内容。

*   CSS 选择器更短
*   CSS 选择器的特异性较低
*   CSS 选择器包含更多的上下文——但不是 BEM 能给出的上下文

当我开始开发这个的时候，我觉得它已经被优化了。但是我花了几年时间才发现我需要更多的优化，因为仍然有一些问题。

*   如果我需要覆盖样式，我仍然需要通过使选择器更具体来添加一些层
*   有时当使用 CSS 库/框架时，我仍然需要使用`!important`
*   选择器并不是不言自明的

# **3。使用 BEM 编写更好的 HTML 和 CSS**

当我发现浏览器阅读选择器的方式与人类不同时，就像是“什么？嗯？”这对我来说太新鲜了。我发现我的 CSS 选择器没有我想象的那么好。

因此，当我在寻找采取行动并使 CSS 选择器变得更好的方法时，我发现了 BEM。在视频中，你可以看到我是如何在一个示例网站的 CSS 和 HTML 上应用 BEM 的。

在这个例子中，你可以看到根据 BEM 的 HTML 和 CSS 将会是什么样子。

## **HTML**

```
<nav class=“main-nav”>
  <ul class=“main-nav__list”>
    <li class=“main-nav__list-item”><a class=“main-nav__link-item”>Home</a></li>
    <li class=“main-nav__list-item”><a class=“main-nav__link-item”>About</a></li>
    <li class=“main-nav__list-item”><a class=“main-nav__link-item main-nav__link-item—active”>Blog</a></li>
  </ul>
</nav>
```

## **CSS**

```
.main-nav {
  height: 60px;
  max-width: 1024px;
}

.main-nav__list {
  margin: 0;
  padding: 0;
  text-align: center;
}

.main-nav__list-item {
  display: inline;
}

.main-nav__link-item {
  display: inline-block;
  padding: .5em 1em;
}

.main-nav__link-item—active {
  font-weight: bold;
  color: red;
  background: grey;
}
```

## **分析 HTML 和 CSS**

如果你分析 HTML 和 CSS，你会发现每个元素上都有类名——自我描述的类名。你不需要看网站来了解这个组件在页面上的位置。

它为自己声明它是主导航。所有的列表项和链接也会直接给你和你的团队上下文。

让我们检查一下我们是否修复了之前看到的问题。

*   现在你可以复制 HTML，它会保持不变。如果您想重用 HTML，将`.main-nav`改为`footer-nav`，您将看不到任何从主导航继承的样式。所以这是完全可扩展的。
*   如果您想覆盖一个或多个元素的样式，您只需添加另一个类名—就这么简单。
*   特别是当你使用 CSS 预处理器的时候，你的开发结构会有一个很好的架构。
*   CSS 选择器和类名是自描述的。所以现在什么都好找了。它提供了比以前更多的背景。