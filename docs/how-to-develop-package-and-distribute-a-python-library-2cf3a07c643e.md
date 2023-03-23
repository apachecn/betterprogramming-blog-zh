# 如何开发、打包和分发 Python 库

> 原文：<https://betterprogramming.pub/how-to-develop-package-and-distribute-a-python-library-2cf3a07c643e>

## PYTHON 打包

## 利用虚拟环境工具探索新的包装标准

![](img/de29f663a99e3ef235e4bc27024bfeb1.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

当我和我的团队开始在 Atri 实验室开发和发布新的 Python 库时，我们遇到了两个障碍:

1.  我们所知道的包装标准已经被废弃了。
2.  我们找不到任何好的资源来在虚拟环境中使用这些新的打包标准。

因为我们是在虚拟环境中开发 Python 库的(具体来说，就是 Pipenv)，所以我们必须想出一种方法来将这些新的打包标准与虚拟环境工具结合使用。因此，我们决定通过阅读 python.org 和 Pipenv 的官方文档来构建我们自己的方法。

但首先，让我们简单看一下最近的两个包装标准。

# 包装标准是如何演变的

## 使用`pip freeze command`生成`requirements`

早期的标准是使用`pip freeze`命令生成`requirements.txt`文件。在打包和分发库时，这个文件将在`setup.py`文件中被读取。

这产生了两个关键问题:

1.  `pip freeze`命令会将你系统中安装的所有库复制到`requirements.txt`文件中，而你只需要这个项目中使用的库。更详尽意味着更好的包装，对吗？抱歉，答案是否定的。您希望您的库具有最低要求，以便在安装过程中不会产生冲突。
2.  您可能会说您将手动编辑这个文件并删除您知道您不使用的库。会有用的，对吧？抱歉，但答案又是不。这是因为您不知道这些库的依赖关系。通过决定手动编辑`requirements.txt`文件，您很可能会删除这些依赖项。

## 在虚拟环境中生成`requirements`

虚拟环境允许我们远离`pip freeze`命令。例如，在 Pipenv 中，您可以使用`pipenv lock -r > requirements.txt`命令来生成`requirements.txt`文件。

这有什么帮助？我们不是刚刚交换了一个命令吗？

没错，但它解决了早期标准的一个关键问题。现在，我们的需求文件只包含我们在这个项目中使用的库，而不是整个系统。

但是这个标准仍然有一个问题:在建立 CI/CD 管道时，总是有可能出现错误配置。因此，像以过时的`requirements.txt`文件结束这样的错误是经常发生的，而且很有可能发生。

如果你有兴趣了解更多关于哪些已经被弃用，有哪些新的替代方案，你可以查看这个[博客](https://blog.ganssle.io/articles/2021/10/setup-py-deprecated.html#id2)。

# 新的打包标准和虚拟环境工具

新的打包标准与常用的虚拟环境工具不直接兼容。比如，根据新标准，我们应该使用`python -m build`包，而不是运行`python setup.py sdist`。

运行`python -m build`创建一个虚拟环境，安装依赖项等。但是，如果您使用 Pipenv 之类的虚拟环境工具，它将无法从 Pipfile 安装依赖项。

因为我们是在虚拟环境中开发 Python 库的(具体来说，就是 Pipenv)，所以我们必须想出一种方法来将这些新的打包标准与虚拟环境工具结合使用。

# 发展

开发 Python 库时使用 Pipenv 之类的虚拟环境。可以在[这里](https://realpython.com/pipenv-guide/)找到关于 Pipenv 的很好的指南。

# 在虚拟环境中打包 Python 库

一个好的起点是先通读 Python.org 关于打包一个简单 Python 项目的教程。我们将调整这些步骤，以便在虚拟环境工具中使用它们。

## 步骤 1:创建包文件

## 步骤 2:创建 pyproject.toml

遵循此处中[所述的步骤 1-2。](https://packaging.python.org/en/latest/tutorials/packaging-projects/#creating-the-package-files)

## 步骤 3:配置元数据

我们现在可以通过读取 Pipfile 以编程的方式获取需求，而不是从 Pipfile 生成一个`requirements.txt`。

下面是我们如何做到这一点的一个例子——摘自 [Streamlit 的 Github 库](https://github.com/streamlit/streamlit/blob/develop/lib/setup.py)中的`setup.py`文件。

请注意以编程方式生成需求的最后一行

我们可以在调用`setup.py`文件中的`setuptools.setup`函数时使用这些需求。

如何使用这些需求

## 步骤 4:生成分发归档文件

注意，运行 PyPi 推荐的`python -m build`包会创建一个新的虚拟环境。然而，我们希望`build`包使用由`pipenv`创建的虚拟环境，而不是创建自己的环境。

如果我们简单地给命令 `*python -m build -n*` *添加一个标志，告诉它不要创建自己的虚拟环境，会怎么样？*

可惜这还是失败了。

![](img/0efd063d76f3eefbc89687f4cb96d289.png)

来自[https://blog . ganss le . io/articles/2021/10/setup-py-deprecated . html # id2](https://blog.ganssle.io/articles/2021/10/setup-py-deprecated.html#id2)

注意，早先的`setup.py`曾经有两个:`sdist`和`wheel`。

如果我们给命令`python -m build -n`添加一个标志，我们在前面的步骤中编写的生成需求的代码会在`sdist`步骤中动态通过(这个步骤生成了. tar.gz 文件),但是在创建一个轮子时会失败。您将得到一个`NonExistentKey`错误。

这个问题可能会在`build`的未来版本中得到解决。在此之前，您需要分别运行`sdist`和`wheel`步骤。

1.  对于`sdist`步骤，运行`python -m build -n -s`
2.  对于`wheel` 步骤，运行`python -m build -n -w`

## 步骤 5:上传发行版归档文件

1.  在 [PyPi](https://pypi.org/) 和 [TestPyPi](https://test.pypi.org/) 中创建账户。
2.  在您的`$HOME`目录中创建`.pypirc`文件。内容应该如下:

内容在。pypirc 文件

3.像`pipenv install twine --dev`一样安装捆绳。

4.将您的包上传到 PyPiTest。从虚拟环境内部，运行:

```
python -m twine upload --repository testpypi dist/*
```

5.从 PyPiTest 安装您的软件包:

```
pipenv install -i [https://test.pypi.org/simple/](https://test.pypi.org/simple/) <your-package-name>
```

# 结束语

在这篇文章中，我们学习了以下关于 Python 打包的内容:

1.  包装标准是如何发展的？
2.  如何改编 PyPi 推荐的指南在虚拟环境中打包一个 Python 库？

如果您有兴趣阅读更多关于我们为 Python 开发人员创建新的 web 开发框架和建立企业技术创业的旅程，请继续关注。

*感谢 Atri Labs 的* [https://blog . ganss le . io/articles/2021/10/setup-py-deprecated . html # id2](https://medium.com/u/bb565db0c961#id2)

*   [https://realpython.com/pipenv-guide/](https://realpython.com/pipenv-guide/)*   [https://packaging . python . org/en/latest/tutorials/packaging-projects/# creating-the-package-files](https://packaging.python.org/en/latest/tutorials/packaging-projects/#creating-the-package-files)*   [https://github . com/streamlit/streamlit/blob/develop/lib/setup . py](https://github.com/streamlit/streamlit/blob/develop/lib/setup.py)*   [https://pypi.org/](https://pypi.org/)*   [https://test.pypi.org/](https://test.pypi.org/)