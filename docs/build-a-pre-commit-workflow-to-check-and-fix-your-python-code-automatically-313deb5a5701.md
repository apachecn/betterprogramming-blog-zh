# 构建预提交工作流以自动检查和修复 Python 代码

> 原文：<https://betterprogramming.pub/build-a-pre-commit-workflow-to-check-and-fix-your-python-code-automatically-313deb5a5701>

## 提高代码质量并加速代码评审过程

![](img/8d8a642b969da77810794b9d5edc11d2.png)

Vishal Jadhav 的照片来自 Unsplash 。

Git 可以在某些动作发生时自动运行定制脚本，也就是所谓的钩子。

例如， [*预提交*、*提交-消息*和*预推送*挂钩](https://lynn-kwong.medium.com/use-pre-commit-commit-msg-and-pre-push-git-hooks-to-fix-your-python-code-asap-77d80d3ce412)分别在提交时、保存提交消息时和提交被推送到远程储存库之前被触发。

在这些 Git 挂钩中，最重要的是*预提交*，它可以执行静态代码检查，并自动修复琐碎耗时的问题。由于有了*预提交*，代码审查人员可以节省大量的代码审查时间，并更有效地关注代码的重要逻辑变化。

如本文中的[所示，我们可以从头开始编写一个*预提交*脚本，这只是一个常规的可执行脚本。然而，当同一个钩子在几个仓库中使用时，会有一个问题。我们需要在不同的地方复制和维护相同的代码，这不是很方便。](https://lynn-kwong.medium.com/use-pre-commit-commit-msg-and-pre-push-git-hooks-to-fix-your-python-code-asap-77d80d3ce412)

幸运的是，大多数最好的行业标准 linters，如[](https://lynn-kwong.medium.com/use-black-mypy-and-pylint-to-make-your-python-code-more-professional-b594512f4362)*、 [*isort*](https://lynn-kwong.medium.com/use-isort-to-sort-your-python-module-imports-automatically-40918f3e2a8b) 和 [*pylint*](https://lynn-kwong.medium.com/use-black-mypy-and-pylint-to-make-your-python-code-more-professional-b594512f4362) 都提供了一些可以直接使用的 Git 挂钩，并带有可定制的参数。这些 Git 钩子可以用一个叫做 [*预提交*](https://pre-commit.com/) 的特殊 Python 库安装。是的，它和*预提交* Git 挂钩同名！它是一个管理和维护多语言*预提交*钩子的框架。*

*在这篇文章中，我们将介绍最常用的*预提交*钩子，可以用*预提交*包管理器安装。我们将要构建的工作流可以对 Python 代码执行最常见的静态代码检查。它可以显著提高您的代码质量，并使您的团队更容易审查代码。*

*由于`pre-commit`包管理器是一个 Python 库，我们可以用`pip`来安装它。根据您自己的使用情况，您可以将它安装在您系统的库中或虚拟环境中。*

```
*$ **pip install pre-commit***
```

*现在`pre-commit`命令应该可用了，我们可以开始为它创建配置文件了。*

```
*$ **pre-commit --version** pre-commit 2.18.1*
```

**预提交*包管理器的配置保存在一个名为`.pre-commit-config.yaml`的文件中。你可以创建它，并在 vim 或你喜欢的编辑器如 [VS Code](https://lynn-kwong.medium.com/some-tips-to-make-your-vs-code-more-efficient-db77ec7071f8) 中打开它。*

# **预提交挂钩**

*让我们首先在*预提交钩子*库*中添加一些最常用的钩子。**

*`repos`是`.pre-commit-config.yaml`的顶级键，定义了一个存储库映射列表。每个`repo`都是一个 GitHub 库，其中定义了一些 Git 挂钩。如果您打开相应的存储库，您会发现一个名为`[.pre-commit-hooks.yaml](https://github.com/pre-commit/pre-commit-hooks/blob/main/.pre-commit-hooks.yaml)`的文件，它定义了可用的钩子。钩子由 id 标识，还包括其他元数据，如名称、描述、类型、参数等。*

*在这个例子中，展示了来自*预提交钩子*库的一些最常用的钩子。它们的 id 对于它们的功能来说是不言自明的。如果你想了解更多关于每个钩子的信息，你可以在资源库的`[.pre-commit-hooks.yaml](https://github.com/pre-commit/pre-commit-hooks/blob/main/.pre-commit-hooks.yaml)`中查看它们。*

*特别是，钩子的参数可以用`args`键指定。在本例中，规定了不允许直接提交到*开发*和*主*分支。*

# *自动折叠*

*从 Python 代码中移除未使用的导入和变量。这看起来微不足道，但实际上非常方便，因为您可能会不小心在代码中留下一些未使用的变量和导入，随着时间的推移，这可能会变得混乱。*

*理由很简单。它指定了未使用的变量和导入将被就地移除。你可以在 i [ts GitHub 页面](https://github.com/PyCQA/autoflake#advanced-usage)上找到更多关于 *autoflake* 的论据。*

# *薄片 8*

*[Flake8](https://github.com/PyCQA/flake8) 是围绕 *PyFlakes* 、 *pycodestyle* 、 *Ned Batchelder 的 McCabe 脚本*的包装器。这是一个很好的工具，可以根据编码风格和常见的编程错误来检查你的 Python 代码。你可以把它作为一个扩展安装在 VS 代码中，并按照[这篇文章](https://lynn-kwong.medium.com/use-black-mypy-and-pylint-to-make-your-python-code-more-professional-b594512f4362)中的演示进行配置。然而，有了针对 flake8 的*预提交*钩子，您的代码可以在提交之前被自动检查。*

*根据 [PEP8](https://peps.python.org/pep-0008/#maximum-line-length) ，最大线路长度和最大单据长度分别为 79 和 72。此外，错误 **E203** 和 **W503** 被忽略，这意味着“*前有空格”:“*”和“*换行符分别出现在二元运算符*之前。当您使用黑色进行格式化时，您将会遇到这些错误，这将很快引入。*

# **黑色**

*`[**black**](https://pypi.org/project/black/)`是一个很棒的工具，可以用来自动格式化你的 Python 代码。Python 代码会根据 [PEP8](https://www.python.org/dev/peps/pep-0008/) 自动格式化，为您节省大量时间。*

*如果你有更复杂的黑色设置，你可以在[*py project . toml*](https://github.com/psf/black/blob/main/pyproject.toml)中添加，如[此链接](https://github.com/psf/black/blob/main/pyproject.toml)所示。*

# *伊索特*

*是一个 Python 库，可以按字母顺序对导入进行排序，并自动按类型将它们分成不同的部分。`isort`可以把你乱七八糟的进口整理好，按 PEP8 排序。此外，你的团队会有一致的风格，每个人都会很开心。如果需要*，请查看[这篇文章](https://lynn-kwong.medium.com/use-isort-to-sort-your-python-module-imports-automatically-40918f3e2a8b)以获得关于`isort` 的更详细介绍。**

*线长度被指定为 79，以使其与上述其他棉绒一致。此外，重要的是，您必须指定`src`选项，并将路径添加到您的源代码中。*

*否则，您的本地库将无法正确订购。还有，需要注意的是`.isort.cfg`和`pyproject.toml`中的选项是`src_paths`。如果您需要对*或*进行更多控制，请检查[此链接](https://pycqa.github.io/isort/docs/configuration/config_files.html)。*

# *皮林特*

*[***Pylint***](https://pypi.org/project/pylint/)是一款自动化的 Python 代码分析工具，可以检测编程错误，并根据 [PEP8](https://www.python.org/dev/peps/pep-0008/) 提供代码修改建议。这是一个很好的工具，可以显著提高 Python 代码的质量。*

*默认情况下,`Pylint`非常严格，这在大多数情况下是好的，但有时可能会矫枉过正，特别是对于遗留代码。通常你不想对你的遗留代码引入太多的重构。在这种情况下，您可以禁用会导致重大重构的警告。*

*例如，许多函数或方法需要一些公共参数，即使它们没有被使用，特别是对于一些 API 框架，如 [Falcon](https://lynn-kwong.medium.com/build-apis-with-falcon-in-python-all-essentials-you-need-9e2c2a5e1759) 。我们可以指定选项`--disable=unused-argument`来禁用这种类型的警告。您可以在`pylint`的输出中找到错误代码。如需详细列表，请查看[此链接](https://vald-phoenix.github.io/pylint-errors/)。*

# *mypy*

*[***Mypy***](http://mypy-lang.org/)是一个为 Python 代码执行静态类型检查的 linter。如果你是 Python 类型注释的新手，请查看这篇文章。需要注意的是，`mypy`只检查显式添加了类型注释的代码，不会检查没有添加类型的代码。*

*默认情况下，`mypy`不支持第三方库的类型，你需要将它们添加到`additional_dependencies`。此外，一个常见的参数是`--ignore-missing-imports`，当一些库没有可用的类型存根时，这很有用。*

# *pydocstyle*

*`pydocstyle`是一个根据 [PEP 257](https://peps.python.org/pep-0257/) 检查 Python 文档字符串的 linter。这是一个很好的工具，可以让你的 docstrings 看起来更好，并且在你的团队中有一些标准。例如，它要求 docstring 的第一行是摘要行，并且必须以命令动词开始，以点结束。*

*默认设置在大多数情况下应该足够了。*

*`.pre-commit-config.yaml`中添加挂钩后，需要安装才能生效。您可以运行以下命令来安装*预提交*挂钩:*

```
*$ **pre-commit install***
```

*当命令运行时，名为`pre-commit`的脚本将在文件夹`.git/hooks`中创建。每当您进行新的提交时，它将自动运行。*

*在这篇文章中，我们介绍了一组预提交钩子，包括`black`、`isort`、`mypy`、`pylint`等，它们可以自动检查并修复您的 Python 代码，使其无错误且更加专业。您可以直接使用挂钩，也可以对其进行微调，使其符合您团队的要求。*

*相关文章:*

*   *[使用预提交、提交消息和预推送 git 挂钩尽快修复您的 Python 代码](https://lynn-kwong.medium.com/use-pre-commit-commit-msg-and-pre-push-git-hooks-to-fix-your-python-code-asap-77d80d3ce412?source=your_stories_page----------------------------------------)*