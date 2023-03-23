# 在 NextJS 应用程序中加载 Gists

> 原文：<https://betterprogramming.pub/loading-gists-in-a-nextjs-application-cb60e3f9d523>

## 关于如何从 NextJS 博客的 markdown 页面加载 GitHub Gists 的教程

![](img/e134dcec15f020709b827e2c776ca60b.png)

由[罗曼·辛克维奇](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/github?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 介绍

我所有的博客文章都可以在我的个人网站上找到。在引擎盖下，它们被保存为降价文件。

它让我可以轻松地写帖子，但是当我在其他平台上交叉发布时，我没有足够的时间来突出显示代码片段的语法和正确的颜色。

为了让它在每个平台上都更漂亮，我使用了 Github Gists。它在任何地方都表现得很好，但我不想在我的博客上有 Github 的代码块样式，但我更喜欢与我的网站设计相匹配的东西。为此，我必须修改我用来以我喜欢的方式加载和呈现来自 gists 的代码的 markdown 解析器。

在这篇文章中，我们将看到如何使用 markdown 文件创建一篇博客文章，如何创建和集成一个 remark 插件，用`react-syntax-highlighter`突出显示恢复的代码，并尽可能保持良好的性能。

# 初始情况:在 NextJS 页面中加载 Markdown

在进入加载 gists 的代码之前，我们首先需要让 blog 加载 markdown 文件。为此，它需要两个不同的项目:

*   包含博客文章的减价文件
*   包含 markdown 加载、解析和呈现的 NextJS 页面

让我们从降价文件开始

## 降价文件看起来像什么

markdown 文件分为两个不同的部分:元数据和内容本身。

元数据在这里给出作者、日期、使用的语言(我有法语和英语的帖子)、URL 路径、标签等信息。

这些信息通过开头的三个破折号`——-`和结尾的三个破折号从内容中分离出来。

以下是一篇博文的简单示例:

检查“查看原始数据”链接

文件非常简单。如果你不熟悉 markdown，你可以[从 Github](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) 阅读这篇文档开始:你将学习如何创建列表、链接、添加图片等等。

在我的 bog 中，我有更多的元数据字段来处理规范链接、图像、标签等等。我把它们从那篇文章中删除了，因为它是这个主题不必要的噪音。

## 加载降价文件

呈现 markdown 文件的第一步是加载它。我们使用基本 NodeJS 代码来:

*   查找文件夹中的所有文件
*   仅筛选 md/mdx 文件
*   加载文件

这可能有点过了，但是在我的博客上，我需要得到每一篇文章来找到与我当前文章最相似的文章，基于对它们的每一个公共标签的计算。无论如何，我将不得不加载每个帖子来找出答案。

代码如下:

如果你正在创建一个简单的博客，你可能更喜欢直接加载正确的文件而不是所有的文件。为此，您只需使用`fs.readFileSync`函数来获取您的文件。

现在帖子已经加载，我们需要在文件中搜索哪一个是适合我们当前页面的。

为此，我寻找与请求路径相同的帖子，但我首先需要解析我的 markdown 的元数据。我使用一个名为`grey-matter`的包来获取它们。

代码如下:

`loadGreyMatterPost`是一个函数，它以灰质格式返回一个表示我们降价的文件。我们仍然需要将它转换成 HTML 代码才能使用。如前所述，我还返回了所有的帖子，以便根据标签进行匹配。然而，这将不会在这篇文章中解释。

因为我使用的是 NextJS，所以我现在可以使用包`next-mdx-remote`将灰质格式转换成 MDX 格式。

代码如下:

## 将加载的数据添加到 NextJS 路由中

为了避免巨大而缓慢的负载，加载部分被添加到 NextJS 路由的`getStaticProps`方法中。它将只在编译时解析文件，这对于 markdown 来说是可以的，并且尽可能快地提供静态页面。

代码如下:

## 在页面中显示博客文章

现在我们有了所有需要的信息，我们可以将它们包含在下一个 JS 页面中。

它需要两个外部组件:

*   `MDXProvider`这是一个组件，可以处理每个子减价文件的默认信息
*   `MDXRemote`将 MDX 内容转换成 HTML React 组件

代码如下:

完成了！我们现在能够编写 markdown 博客文章，并在我们的 NextJS 应用程序中将它们呈现为 HTML！

# 创建备注插件

## AST 转换

备注插件是一个返回另一个函数函数。最后一个得到一个 AST——抽象语法树——代表 markdown 文档。

AST 可以获得许多字段，但这里有一个简单的 typescript 接口来说明它是如何构造的:

使用 markdown 时，代码的 AST 没有子元素或属性，而段落 AST 没有任何值。为了理解它是如何建立的，想象一下下面的降价:

检查“查看原始数据”链接

相关的 AST 将是:

考虑到这一点，我们可以考虑如何包含我们的要点。

## 从 Github 获取要点

我选择将要点包含在内嵌块代码中，前缀为`gist:`，然后是用户名和 ID。它看起来会像这样:

``gist:brunosabot/00000000000000000000000000000000`

当呈现这个内容时，生成的 AST 将由一个带有一个`inlineCode`子节点的`paragraph`节点组成。我们需要做的是替换从 Github 加载的代码中的段落。

由于我们需要来自 Github 的代码，我们也需要获取要点。要点可以由一个或几个文件组成:我们首先需要迭代文件列表，然后加载它们中的每一个。

代码如下:

第一个 fetch 查询获取包含 gist 元数据的 JSON，尤其是 gist 中的文件列表。

当列表恢复后，我们迭代它们以文本形式加载与每个文件相关的代码。

## 访问和更新 AST

gist 插件应该改变原始 AST 的内容。我们将访问文档节点，并根据需要更新它。

为了访问不同的节点，我们使用了`async-unist-util-visit`包。它将对每个匹配所请求类型的 AST 子节点应用一个方法。

如前所述，我们将在`paragraph`节点中寻找`inlineCode`，从`gist:`开始，然后解析它并加载要点。

但是让我们关注一下访问代码:

需要注意的一件重要事情是，AST 必须变异，不能返回。由于`visit`是一个承诺，但不等待回调来解决，我们需要使用一个 hack，将工作添加到承诺列表中，并在插件方法结束之前等待它们解决。

在代码片段中:

*   `node.children.some`方法在代码中寻找要点片段
*   `Object.assign`方法是在不丢失引用的情况下更新对象内容的方法
*   `delete`不是强制的，但是我喜欢保持一个只有必填字段的清晰对象。

在插件中，我们使用了一个`loadAndTransformGist`方法，当我们在代码中检测到一个要点片段时就会调用这个方法。

正如函数名中所写的，该方法将加载要点并进行适当的转换，以包含在 AST 中。

这是它的代码:

在这个代码片段中，第一部分是检查代码片段是否有效，这基本上是检查 AST 值是否为空。

我可以再做一些检查，包括验证 gist 值是否正确。我选择不这样做，因为我经常检查我的帖子的渲染:我可以很快看到 ID 是错误的，因为应用程序要么崩溃，要么不显示任何东西。

然后我提取要点 ID，用之前创建的`loadGist`方法加载它。

那么，我有两种可能性:

第一，要点里只有一个文件。我将用一个方块样本替换当前段落。`getGistAST`将给我代码 AST，我将为调用方法返回该代码。

第二，要点中有多个文件。我将对它们进行迭代，并将它们添加为段落节点的子节点。

`getGistAST`基本上是一个映射方法，代码如下:

翻译成英语，这个 AST 节点是一个`<Gist>`组件，具有:

*   代表文件名的文件属性
*   实际代码的代码属性
*   包含文件语言的阿郎属性，通过映射方法计算得出

这是映射方法:

一切都完成了，我们只需要更新我们的`transformGreyMatterToMDX`方法，添加插件:

# 使用自定义呈现组件

现在 markdown 能够读取要点片段，还有最后一步来进行实际的代码呈现:我们需要创建一个“要点”组件来进行适当的显示。

为此，我将使用 [React 语法荧光笔](https://www.npmjs.com/package/react-syntax-highlighter)包。

这是组件的样子:

我们首先有一个 div，如果文件名可以显示它的话。

然后，我们使用来自`react-syntax-highlighter`的`Prism`和以下属性:

*   `language`:代码片段所在的编程语言。如果不可用，我们设置`text`为默认的非高亮语言
*   `showLineNumbers`:侧面显示行号。如果是文字，最好不要有
*   `wrapLines`和`wrapLongLines`:代码为`text`换行时激活。我发现阅读高亮显示的代码并不容易。

要被识别，该要点组件需要包含在给予`MDXProvider`的组件中

代码如下:

另外，您可以看到我正在重写 img 组件以添加`loading="lazy"`属性。我会帮助我的博客有更好的表现。

# 尽可能保持好的表演

React Syntax Highlighter 的一个问题是，它将加载大量您可能永远不需要的语言。

希望有一种方法可以使用这个库的简化版本，但是它需要我们手动加载语言。

以下是加载系统的`<Gist>`组件代码:

# 结论

为 NextJS 博客创建一个 Gist 插件需要我们了解和操作 AST 格式，这可能不是小事。

我希望这篇文章能帮助你理解它是如何工作的，以及你如何将你的 gists 文件添加到你的博客中。

```
**Want to Connect?**If you want to learn more about JavaScript and React, feel free to follow me on [Twitter](https://twitter.com/brunosabot).
```