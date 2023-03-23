# 在介质上写 iOS 文章变得简单

> 原文：<https://betterprogramming.pub/writing-ios-articles-made-easy-1afec99aa318>

## 加快你写作速度的三个工具

![](img/16f8ff4ecb006f787a34913a4e6711e0.png)

图片来自 [ptra](https://pixabay.com/users/ptra-359668/) 来自 [Pixabay](https://pixabay.com)

撰写关于 iOS 和 Swift 相关主题的文章和教程非常有趣，Medium 使其变得非常容易。但是，如果没有合适的工具，有些任务需要重复的手工操作。

在本文中，我将向您展示三个命令行工具，它们将帮助您尽可能多地实现自动化，从而使您变得更快、更有效率！

# 1.使用 nef 从命令行创建操场

大多数时候，写一篇文章是从你想要测试、探索和描述的代码开始的。没有比使用游乐场更好的方法了！

但是创建一个运动场并添加所需的第三方框架需要多个手动步骤。nef 是我想向您展示的第一个命令行工具。它代表你创造了游戏场地，所以你不需要这样做。

> “借助对第三方库的支持，简化 Swift playgrounds 的创建。”— nef 作者

要创建一个新的游戏场，请转到您想要放置它的目录并执行`nef playground --name <NAME> --podfile <FILEPATH>`。

只需将`<NAME>`替换为游戏场的名称，将`<FILEPATH>`替换为您想要使用的 Podfile 的路径。nef 将创建操场，将其嵌入一个新的工作空间，并添加吊舱。

通过使用我在本文的[中提到的一个小 Python 脚本，您可以做得更好。它生成一个临时 Podfile 并将其传递给 nef，因此您甚至不必自己创建一个 Podfile。](https://medium.com/@HeyDaveTheDev/making-swift-playgrounds-more-playful-6fd6d9f5ba54)

现在，你只需要调用`python play.py <NAME> --list <POD1> <POD2> <POD3> ...` 就可以了，创建的工作空间会在一切设置完成后打开。

# 2.使用 gist 从命令行创建 gist

几乎所有关于编程的文本都使用源代码来演示例子或帮助理解正在发生的事情。

将代码嵌入文本的最佳方式之一是 GitHub gists。只要粘贴一个你想展示的要点的链接，按回车，要点就会展开。

但是有时，您需要许多 gists——我的一篇文章有 19 个——并且需要创建每个 gist，用代码、名称和描述填充它们，并复制到您的文章中。这可能会很烦人。

gist 是另一个命令行工具，它使您能够从终端上传和更新您的 gist，从而解决了这个问题。

> “潜在的最佳命令行吉斯特。”— Defunkt

设置完成后，上传文件到 GitHub gists 就像使用`gist <FILEPATH> --filename <FILENAME> --description <DESCRIPTION>`一样简单。

这将为您完成所有工作，并返回新创建的要点的 ID。这个 ID 可以用于以后更新它。为此，只需输入`gist -u <GIST-ID> <FILEPATH> --filename <FILENAME>`。

我喜欢在一个游乐场的资源目录里添加两个文件，`upload_gists.sh`和`update_gists.sh`。每当我在操场上修改任何一行代码时，我只需运行我的`update_gists.sh`脚本，所有的 gists 都会随着最近的修改而更新。

当你这样做时，你可以在上传或更新时使用`../Pages/<PAGENAME>.xcplaygroundpage/Contents.swift`作为你的文件路径，只需用你页面的实际名称替换`<PAGENAME>`即可。

# 使用 hub 从命令行创建存储库

有时候，在 gists 中展示一些片段是不够的，你需要给你的读者一个到整个项目知识库的链接。您可以使用工具 [hub](https://hub.github.com) ，而不是手动访问 GitHub，创建存储库，并将您的代码推送到那里。

> “hub 是命令行 git 的扩展，它可以帮助您在不离开终端的情况下完成日常 GitHub 任务。”— GitHub

您可以从您的终端克隆、创建和派生存储库、创建问题、提取请求等等。如果您已经建立了自己的游戏场，并且想要为它创建一个新的存储库，那么您所要做的就是`hub create`，这样就完成了！

nef、gist 和 hub 一起将重复和烦人的任务从您身上带走，从而使您更快、更有效率。

我写的几乎每篇文章都用这三个工具！它们节省了我很多时间，我希望你会发现它们和我一样有用。

# 资源

 [## 船形容器

### 简称娜芙蒂蒂，母亲安赫塞娜蒙，是一个命令行工具，以方便创建文件的形式…

nef.bow-swift.io](https://nef.bow-swift.io/docs/) [](https://medium.com/@HeyDaveTheDev/making-swift-playgrounds-more-playful-6fd6d9f5ba54) [## 让 Swift 游乐场更好玩

### Swift playgrounds 让尝试新框架变得非常简单，但是到了…

medium.com](https://medium.com/@HeyDaveTheDev/making-swift-playgrounds-more-playful-6fd6d9f5ba54) [](https://github.com/defunkt/gist) [## defunkt/gist

### gist gem 提供了一个 gist 命令，您可以使用它从您的终端上传内容到 https://gist.github.com/.

github.com](https://github.com/defunkt/gist)  [## hub git 的命令行包装器

### hub 是命令行 git 的一个扩展，可以帮助你在不离开终端的情况下完成日常 GitHub 任务。阅读…

hub.github.com](https://hub.github.com)