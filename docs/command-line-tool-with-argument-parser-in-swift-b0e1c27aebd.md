# 使用 Swift 中的参数解析器构建命令行工具

> 原文：<https://betterprogramming.pub/command-line-tool-with-argument-parser-in-swift-b0e1c27aebd>

## 与您的团队共享 Xcode 片段

![](img/fb5cd0d564ec3317474074c457514f57.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Argument Parser](https://github.com/apple/swift-argument-parser) 是苹果推出的新包。它使得编写命令行工具来自动化常见任务变得更加容易。参数解析器有三个属性包装器:参数、标志和选项。

# 争吵

它是运行脚本时在我们的参数变量中设置的一个值。例如，我们将运行与我们的参数变量相对应的`swift run SnippetSharing Snippets`片段。它也是我们的 snippets 文件夹名称。也可以是别的。

# 旗

标志主要用于添加额外的日志。例如，我们可以运行`swift run SnippetSharing Snippets --verbose`来获得额外的日志。

让我们创建一个命令行工具，与我们的团队成员共享 Xcode 片段。为此，我们在命令行工具文件夹中创建代码片段，命令行工具会将它移动到预期的文件夹中。在我们的例子中，我们将同时使用参数和标志属性包装器。

# 创建 Swift 包

让我们在项目文件夹中创建一个名为`SnippetSharing`的文件夹:

`mkdir SnippetSharing`

运行它以创建 Swift 包:

`swift package init --type executable`

之后输入`swift run`，打开初始化创建的`Package.swift`文件。

# 将参数分析器作为依赖项添加

在`Package.swift`中，我们需要在包中添加参数解析器。它的版本是 0.0.4，因为我们不需要参数解析器的任何异步操作。

完成这些配置后，参数解析器包应该出现在 Xcode 左下方的“包依赖项”中

## 编写命令行

让我们定义一个使用`ParsableCommand`的名为`SnippetSharing`的结构。

我们的结构有`folderName`，它对应于我们希望与团队成员共享的`Snippet`文件夹。`Manager`用于查找我们当前的目录路径。`snippetFolderPathXcode`是 Xcode 的默认文件夹路径，我们将复制所有代码片段以显示 Xcode 中的代码片段。Verbose 用于日志记录。

我们需要一个函数来运行我们的 shell 命令，如删除、创建和复制操作。

之后，我们可以分别编写命令函数。如果你不熟悉 Shell 脚本，你应该看一些关于它的例子。代码如下:

最后，我们可以更新我们的 run 方法，并在结构范围之外调用结构的 main 函数。

创建 Xcode 片段的文件夹有一个步骤。在包文件夹中，根据需要创建一个文件夹。我的文件夹名是`Snippets`。它同时包含了`IBOutlet`、`IBAction`的片段。两个关键词我们都用了很多时间。

## 尝试命令行工具

只需运行`swift run SnippetSharing Snippets`

正如我们所见，我们从一个基础上自动化了 Xcode 片段共享。所有团队成员都可以更新代码段文件夹并运行该命令。它也可以用于自定义 xctemplate 进行共享。[完整的项目在这里](https://github.com/TolgaTaner/SnippetSharing)。

*感谢阅读！*