# 强制执行。NET 代码样式。编辑器配置

> 原文：<https://betterprogramming.pub/enforce-net-code-style-with-editorconfig-d2f0d79091ac>

## 在编辑器中保持一致的编码风格

![](img/eb6f64abc25d7b90ffdb62ffc8c6f91c.png)

ahin Sezer diner 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

拥有一个干净的代码库是必不可少的，说实话，没有人想阅读无结构和混乱的代码。清理善后工作既乏味又耗时，而且一点也不好玩。

一些代码编辑器具有内置的清理功能，可以为您完成这项工作。当单独处理代码库时，这可能会工作得很好。然而，在团队中工作时，关键是要有一个通用的配置，不要用这个清理特性覆盖彼此的代码。

幸运的是，大多数 ide 都支持[。editorconfig](https://editorconfig.org/) 在 ide 和开发者之间创造了一致性。

# 添加。editorconfig 到您的。网络项目

Visual Studio 提供了一种将该文件添加到项目中的简便方法，并为您提供了更改配置的 UI 支持。或者，也可以不使用 Visual Studio 手动更改该文件。微软也提供了很好的模板。NET 和 C#开始。

您可以按照以下步骤添加文件:

1.  在 Visual Studio 的解决方案资源管理器中右击您的项目
2.  点击*【添加】*
3.  选择*“新项目”*
4.  搜索*“编辑器配置”*
5.  选择" *editorconfig 文件(。NET)"或* " *editorconfig 文件(C#)"*

该文件现在应该已经添加到您的项目中，并带有一些. NET 的默认设置。双击该文件将打开一个视图，您可以在其中浏览预定义的设置并按照您喜欢的方式配置它。完成初始配置后，您应该提交并推动它与您的团队共享。

**注意:**您可能从一开始就没有完美的配置，有时这取决于您的团队如何工作。当发生变化时。对我来说，尽可能隔离变更，并要求每个团队成员都进行审核是很有价值的。这应该让人们意识到这些变化及其影响。

# 应用代码样式和清理

## 在 Visual Studio 中运行它

应用您的。editorconfig 文件，你可以点击 Visual Studio 中的小扫帚按钮，也可以点击组合键 **Ctrl+K，Ctrl+E** 。点击 **Ctrl+K，Ctrl+D** 将会重新格式化你的代码。

## 通过命令行运行

从 CLI 开始清理需要一个可以通过 dotnet 命令行界面安装的工具。

```
dotnet tool install -g dotnet-format
```

安装完成后，您只需运行以下命令来清理您的代码。这也可以作为预提交挂钩运行。

```
dotnet format
```

# 在构建过程中捕获违规

清理代码已经很好了，但是强制执行代码风格的一个安全方法是，如果它不符合您的代码风格，就让您的构建失败。这可以通过将`[Microsoft.CodeAnalysis.CSharp.CodeStyle](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle/)` NuGet 包添加到您的项目中来实现。

```
dotnet add package Microsoft.CodeAnalysis.CSharp.CodeStyle
```

如果您在`.editorconfig`文件中配置将未使用的变量视为错误，构建将失败，并在构建输出中显示相应的行。通过这种方式，您可以绝对肯定所开发的代码遵循了您的代码风格。

# 进一步阅读和参考

[](https://editorconfig.org/) [## 编辑器配置

### EditorConfig 是一个文件格式和文本编辑器插件的集合，用于维护…

editorconfig.org](https://editorconfig.org/) [](https://docs.microsoft.com/en-us/dotnet/fundamentals/code-analysis/code-style-rule-options) [## 。NET 代码样式规则选项-。网

### 通过定义，可以在代码库中定义和维护一致的代码样式。中的. NET 代码样式规则选项…

docs.microsoft.com](https://docs.microsoft.com/en-us/dotnet/fundamentals/code-analysis/code-style-rule-options) [](https://docs.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options?view=vs-2022) [## EditorConfig 设置- Visual Studio (Windows)

### 您可以将 EditorConfig 文件添加到您的项目或代码库中，以便为每个人实施一致的编码风格…

docs.microsoft.com](https://docs.microsoft.com/en-us/visualstudio/ide/create-portable-custom-editor-options?view=vs-2022) [](https://www.hanselman.com/blog/editorconfig-code-formatting-from-the-command-line-with-net-cores-dotnet-format-global-tool) [## EditorConfig 代码格式化。NET Core 的 dotnet 格式全局工具

### “EditorConfig 有助于为从事同一项目的多个开发人员保持一致的编码风格…

www.hanselman.com](https://www.hanselman.com/blog/editorconfig-code-formatting-from-the-command-line-with-net-cores-dotnet-format-global-tool) [](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle/) [## 微软。代码分析

### NET 编译器平台(“Roslyn”)的 C#代码风格分析器。更多细节在 https://aka.ms/roslyn-packages 这个…

www.nuget.org](https://www.nuget.org/packages/Microsoft.CodeAnalysis.CSharp.CodeStyle/)