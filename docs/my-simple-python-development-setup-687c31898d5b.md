# 我的简单 Python 开发设置

> 原文：<https://betterprogramming.pub/my-simple-python-development-setup-687c31898d5b>

## 保持简单并隔离您的环境

![](img/85143d23647616812ca00b1e85e19b50.png)

照片由 [Max Nelson](https://unsplash.com/@maxcodes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我喜欢 Python。我的第一份真正的开发工作是开发 Django web 应用程序，我一直喜欢这种语言的表现力，以及它在我感兴趣的几乎所有软件开发领域都是一个不错的工具。

这是对我用来开发 Python 应用程序的环境以及如何设置它的简要说明。

# 我的设置要求是什么？

*   保持操作系统默认的 Python 安装原封不动。
*   管理多个独立的 Python 版本。
*   管理虚拟环境以隔离项目需求。

# 保持您的操作系统默认 Python 安装不变

我喜欢让我的操作系统尽可能的干净，这样一切运行起来更顺畅。当开发时，尤其是测试和学习新事物时，我们都会犯错误和破坏东西。

隔离您的开发环境，因此当您不可避免地打破某些东西时，损害也是隔离的。

您的操作系统的 Python 版本属于您的 OS。它通常不是你想要的 Python 版本，在 macOS 的情况下是 Python 2.7，通过全局安装包，当你需要同一个包的多个版本时，你迟早会遇到问题。

让操作系统的 Python 独来独往的另一个原因是，操作系统的某些进程可能会使用那个 Python 版本。如果您更改它，可能会损坏您的操作系统。

# 管理 Python 的多个独立版本

为了管理多个 Python 版本，我使用了`[pyenv](https://github.com/pyenv/pyenv)`。尽管我总是尝试使用最新的 Python 版本，但有时你需要使用旧的代码库。`pyenv`帮你轻松切换 Python 版本。

当我使用 macOS 时，我倾向于使用[自制软件](https://docs.brew.sh/Homebrew-and-Python)安装几乎所有我需要的开发工具。

```
brew install pyenv
```

通常，`pyenv`会在安装后添加到你的路径中。要安装任何 Python 版本，请执行以下操作:

```
# Show the list of available python versions# If you don’t see the python version you may try updating pyenv to its latest versionpyenv install — list# Install the version you wantpyenv install -v 3.7.2# List your installed python versions (The * will indicate your current version)pyenv versions# Change your global python versionpyenv global 3.6.8# Sets a location specific python version (creates a .python-version file)pyenv local 2.7.15# Uninstall any versionpyenv uninstall 2.7.15
```

`pyenv`的一个优势是它从源代码构建你安装的每个 Python 版本，它们都位于你的`pyenv`根目录下:

```
~/.pyenv/versions/
```

# **管理虚拟环境**

在 Python 中开发时，保持项目依赖性隔离和良好管理的方法是虚拟环境。

有很多不同的工具可以做到这一点，如果你想更深入地比较最常见的工具，看看这个伟大的堆栈溢出答案。

[](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe) [## venv，pyvenv，pyenv，virtualenv，virtualenvwrapper，pipenv 等有什么区别？

### virtualenv 是一个非常流行的工具，它可以为 Python 库创建隔离的 Python 环境。如果你不是…

stackoverflow.com](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe) 

我们将要讨论的两种工具的工作原理相似。他们为您的 Python 安装和依赖项创建一个文件夹，并修改`PATH`环境变量以指向所需的安装。

当我尝试在新项目中使用最新的 Python 3.x 版本时，我使用`[venv](https://docs.python.org/3/library/venv.html)`来管理虚拟环境。从版本 3.3 开始，它就包含在 Python 的标准库中，这意味着您不必安装任何额外的工具。

假设您想使用 Python 3 启动一个新项目，您为您的项目创建一个新文件夹并运行:

```
# Create our virtual environment named “env”python3 -m venv env# A python installation in a new env folder will be created.# Activate your virtual envsource env/bin/activate# You’ll be working in your virtual environment…. Happy hacking!# To leave your virtual environmentdeactivate
```

如果你需要支持 Python 2 或低于 3.3 的版本，那么`[virtualenv](https://virtualenv.pypa.io/)`就是你要走的路。实际上，`venv`的实现主要基于`virtualenv`，所以它们的工作方式非常相似。

一个主要的区别是，与`venv`不同，`virtualenv`不是 Python 标准库的一部分。您需要使用`[pip](https://pypi.org/project/pip/)`来安装它:

```
pip install virtualenv
```

使用`virtualenv`创建虚拟环境的方式与我们之前看到的使用`venv`的方式非常相似。导航到您的项目文件夹并运行:

```
# Create our virtual environment named “env”virtualenv env# A python installation in a new "env" folder will be created.# Activate your virtual environmentsource env/bin/activate# You’ll be working in your virtual environment…. Again, happy hacking!# To leave your virtual environmentdeactivate
```

# **重述**

*   不要搞砸你的 OS Python 安装，你会后悔的。
*   用`pyenv`管理多个 Python 版本。
*   避免全局安装特定于项目的包，解决方案是使用虚拟环境。
*   要管理 Python 版本> = 3.3 的虚拟环境，请使用标准库中的`venv`。
*   要管理旧 Python 版本的虚拟环境，请使用`virtualenv`，它需要使用`pip`安装。