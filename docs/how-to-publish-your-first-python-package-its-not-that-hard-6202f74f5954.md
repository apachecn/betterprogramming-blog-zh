# 如何发布你的第一个 Python 包——这并不难

> 原文：<https://betterprogramming.pub/how-to-publish-your-first-python-package-its-not-that-hard-6202f74f5954>

## 上传到 PyPI 和 pip 安装您的成名之路

![](img/8fd492b1726e8185071cedb29b6b3b91.png)

照片由[岩田良治](https://unsplash.com/@ryoji__iwata?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/puzzle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我已经发布了我的第一个 Python 包，现在轮到你了。本指南将为您提供清晰的分步说明，帮助您规划、设置和执行第一个公共 Python 包的发布。跟随之后，你和世界上的其他人将能够pip 安装你自己发布的软件包。

快速的谷歌搜索会让发布一个 Python 包看起来像是一项令人生畏的任务。事实上，简单的第一个包并不难，但是过多的配置选项淹没了它。

# 达成共识

在我们转向代码和终端之前，我们必须做一些簿记工作。被抛来抛去的术语可能会令人困惑。什么是模块？一个包裹？图书馆？

我将这三个术语概念化的方式是，模块可以像单个 Python 文件一样基本。包就是那个模块，但却是为分发而准备的。最后，库将是模块的集合，大概是为发布而准备的。

接下来要澄清的是你需要做什么。确保你有一个编辑器(我用的是 Visual Studio)、一个终端应用(我用的是 iTerm 或者 Windows PowerShell)、一个 TestPyPI 上的账号 Python 包索引的沙箱。我们希望在那里练习，这样就不会污染生产索引，但是我也将包括生产发布的命令。

最后，我想概述一下我们将要采取的步骤。我们将设置目录结构，添加一些内容，然后创建用作安装脚本的 setup.py 文件。

接下来，我们将创建包并上传到(测试)PyPI。最后，我们将讨论继续开发的下一步以及学到的一些经验教训。

# 设置目录和内容

让我们从创建下面的文件夹结构开始，用您的包名替换`jhsu98`。

```
jhsu98/
  jhsu98/
    __init__.py
  LICENSE
  README.md
  setup.py
```

我们将要创建的模块将直接放入嵌套的`jhsu98`目录中的`__init__.py`中。把顶层目录想象成我们的项目，把嵌套目录想象成模块本身。

`LICENSE`文件将包含软件包发布的许可证。我不会在这里深入讨论，但是为您的包选择您想要的许可证并相应地复制/粘贴。

`README.md`文件将包含包的描述。用 Markdown 编写，这将是在项目的 PyPI 页面上可见的文档。

最后，`setup.py`文件将用于在包创建期间提供细节。它实际上是项目的元数据。好了，让我们开始充实`__init__.py`和`setup.py`

每次初始化模块实例时，都会执行`__init__.py`文件。我们要确保有两件事:一个版本和一个函数。

```
# __init__.py
__version__ = "0.0.1"def helloWorld():
   print("Hello World")
```

请随意在文件中使用您自己的版本号和您自己的函数。该版本的主要优点是，每次发布到 PyPI 时，您都需要增加。

现在是`setup.py`，互联网提供了上千种不同的变化。让我们从解释这个文件是什么和它做什么开始。

我相信你已经注意到了`.py`扩展名，所以对于初学者来说，它是一个普通的 Python 文件——不要让它的存在过于复杂。该文件的目的是提供一旦我们执行该文件以触发打包过程时将使用的包的详细信息。

在研究了许多`setup.py`教程和例子之后，以下是我的开始样板:

```
import pathlib
from setuptools import setupHERE = pathlib.Path(__file__).parent
README = (HERE / "README.md").read_text()
setup(
  name="jhsu98",
  version="0.0.1",
  description="",
  long_description=README,
  long_description_content_type="text/markdown",
  author="",
  author_email="",
  license="MIT",
  packages=["jhsu98"],
  zip_safe=False
)
```

需要关注的几个亮点:确保版本号与`__init__.py`匹配，我冒昧地假设了一个免费使用和分发的 MIT 许可证，`packages=["jhsu98"]`的值必须与你的模块名匹配，`name="jhsu98"`是项目的名称，技术上可以与你的模块名不同。

我将剩下的部分留给您来填写，但是在此之后，我们就完成了模块的创建并准备打包它了。

# 创建分发包

是时候使用您的`setup.py`文件来创建发行包了。打开终端，导航到父项目文件夹。然后使用以下命令运行`setup.py`:

```
python setup.py sdist bdist_wheel
```

您将看到创建了三个目录`build`、`dist`和`jhsu98.egg-info`，除了您的模块名称。我们最关心的是`dist`目录，其中有一个以您的包的名称和版本命名的 tarball ( `tar.gz`文件)。这是将要发布到(测试)PyPI 的文件。

有许多不同的策略来创建依赖于项目内容和发行方法的发行版，但是那是另外一天的 2 级主题。重要的是在下一节中将要发布到(测试)PyPI 的单文件 tarball。

# 发布您的包

这一刻终于到来了。是时候发布你的包了。如前所述，Python 包索引托管了一个用于测试的索引，这就是我们将要开始的地方。首先，在[https://test.pypi.org/](https://test.pypi.org/)上创建一个账户

创建并验证您的帐户后，让我们回到终端，安装用于上传 tarball 的 [twine](https://pypi.org/project/twine/) 。

```
pip install twine
```

接下来，我们将使用 twine 模块上传到测试环境。

```
python3 -m twine upload --repository-url https://test.pypi.org/legacy/dist/*
```

为了简单起见，我用星号(*)来上传全部；但是，接下来您可以更加明确地指定要上传的确切文件。

输入用户名和密码后，您应该会收到一条成功消息:祝贺您，您刚刚发布了一个 Python 包！

您可以在任何 Python 环境中查看您的[项目的主页](https://test.pypi.org/manage/projects/)和`pip install`。让我们试着从`pip`安装我们的模块。我建议使用虚拟环境，但这取决于你。

因为我们发布了测试索引，所以我们需要指定安装源。使用以下命令，替换您自己的软件包名称:

```
pip install -i https://test.pypi.org/simple/jhsu98
```

假设安装进展顺利，是时候创建一个新的`.py`文件了，您将在其中导入您安装的模块并使用它:

```
import jhsu98jhsu98.helloWorld()
```

如果你准备好真正的交易，这里有一些说明。[创建一个账户](https://pypi.org/)并使用以下命令上传和安装。

`python3 -m twine upload dist/*`

`pip install jhsu98`

# 持续发展和经验教训

很自然地，你已经兴奋地准备好发布你的 Python 模块了，所以这里有一些小技巧来节省你在 Google 上的时间，并且避免我碰到的绊脚石。

*   想想文件结构。您可能不希望整个模块或库都放在`__init__.py`中，所以计划好您的文件并相应地导入。
*   制定版本计划。我喜欢在我的项目中使用`major.minor.micro`结构。简而言之，我认为向后破坏的变更是主要版本，新特性或一组修复是次要版本，表面的变更或单一的错误修复是微版本。不要忘记更新`setup.py`和`__init__.py`的版本
*   跟踪依赖关系。随着项目的增长，您可能需要导入其他已安装的模块。您将希望在`setup.py`中通过添加一个名为`install_requires`的参数来跟踪这些，该参数是一个包名数组。这将确保在安装过程中包含这些依赖项。

你有什么建议或经验教训吗？分享您发布到以下 Python 包索引的经验。