# 如何用插件轻松扩展 Pylint

> 原文：<https://betterprogramming.pub/how-to-easily-extend-pylint-with-plugins-d8ead26a68ac>

## 为您的特定业务目标定制静态 Python 代码分析

![](img/6e1292709028e99f63def4bfeeb87bc3.png)

图片由[保利乌斯·安德里库斯](https://unsplash.com/@paulando)在 [unsplash](https://unsplash.com/) 拍摄

静态代码分析和林挺是软件开发成功的基础。python 最常用的静态代码分析器之一是`pylint`

在本文中，我将向您展示使用 Pylint 的内置插件架构来扩展它是多么容易。

# 什么是静态代码分析？

静态代码分析是在不运行代码的情况下自动评估代码质量的尝试，而动态代码分析是在运行时自动分析代码质量的尝试。

所以静态代码分析工具的输入要么是源代码本身，要么是它的字节码表示(如果存在的话)，而它的输出取决于我们要达到的目标。

# 什么是棉绒？

Linters 是静态代码分析器的一个子类型，旨在检测潜在的 bug、错误、弊端和风格问题。

所以像所有静态代码分析器一样，linters 将源代码作为输入和输出，它提供了一个可疑代码语句的列表，开发者应该改进这些语句。

# 什么是 Pylint？

Pylint 可以说是 python 编程语言中最流行的 linter。

由于 python 是一种解释型语言，所以它没有编译时间。
因此，没有内置的错误检测机制。
林挺是 python 开发者在执行前验证代码的唯一方式。

除此之外，pylint 执行样式检查以确保代码符合 [pep8](https://peps.python.org/pep-0008/) 。

# 动手示例

让我们来看一个简短的代码示例:

这是一个非常简单的例子，它以写权限打开`somefile.txt`，它将“hello world”写入文件。

让我们运行这段代码:

```
python example.py
```

我们得到一个写着“hello world”的文件。

让我们用 pylint 来 lint 这段代码，首先我们必须从 PyPI 仓库安装 pylint:

```
pip install pylint
```

现在让我们在代码上运行`pylint`:

```
pylint example.py
```

输出:

```
************* Module example
example.py:1:0: C0114: Missing module docstring (missing-module-docstring)
example.py:1:7: W1514: Using open without explicitly specifying an encoding (unspecified-encoding)
example.py:1:7: R1732: Consider using 'with' for resource-allocating operations (consider-using-with)------------------------------------------------------------------
Your code has been rated at 0.00/10 (previous run: 0.00/10, +0.00)
```

我们得到了 3 个提示:

1-没有模块 docstring。
这“只是”一个约定提示，对运行时没有影响，但是使用 docstrings 来确保代码的可通信性和可读性是非常重要的。

2-打开文件而不显式指定编码。
默认编码是 [utf-8](https://en.wikipedia.org/wiki/UTF-8) ，但是没有明确指定编码可能会有问题。

3 —重构建议在打开文件时使用`with`语句，这实际上是至关重要的重构！
在这段代码中，我使用了 open 语句而没有关闭文件，我有[泄漏资源](https://en.wikipedia.org/wiki/Resource_leak)的风险！

除了这些错误，我的代码得了 0 分(满分 10 分)——是的，就是这么糟糕！

所以让我们快速重构代码来解决这 3 个问题

现在让我们 lint 这段代码:

```
pylint example.py
```

结果是:

```
--------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 10.00/10, +0.00)
```

太好了！代码没有错误，分数是 10/10！

# 用插件扩展 pylint

Pylint 有一个插件架构，允许您轻松扩展它的功能。

为了演示这样一个插件的使用，让我们写一个简短的代码:

这是一个非常简单的脚本，有 3 个未使用的导入，我不想让 pylint 检查(因此提示 pylint 忽略未使用的导入错误)

但是正如您所看到的，我有 2 个来自 sys 模块的 import 语句，它们可以合并成一个。

让我们用 pylint 对这个文件进行 lint:

```
pylint ./app/__main__.py
```

输出:

```
-------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 8.57/10, +1.43)
```

没有错误。

这是因为 pylint 没有用于重复导入的检查器。

所以让我们创造一个我们自己的:)

首先，我们需要创建一个插件模块:

我已经决定把它写在`__init__.py`文件中，这样当模块被加载时代码就会运行。
`register`函数由 pylints 的插件管理器识别，它用于将检查器注册到 pylints 的检查器列表中。

现在让我们创建我们的检查器，检查是否有来自导入的重复项:

Pylint checker 有几个组件:

1.  `msgs` —一个字典，其中的键是一个标准的 pylint 提示索引。
    提示索引以 c(约定)、r(重构)、w(警告)、e(错误)、f(致命)开始，然后是不与任何其他`pylint`数字冲突的 4 位数字。
    字典的值是 3 个元素的元组，即要显示的消息、消息查找名称和建议。
2.  `__init__`我们实例化一个集合的方法。
3.  和`astroid`协议方法。

星形线是一个[访问者](https://en.wikipedia.org/wiki/Visitor_pattern)，他为每个节点类型都指定了钩子方法。
每种节点类型有两种方法，要么是 visit，要么是 leave，通过格式:`visit_<node_type>`或`leave_<node_type>`来识别。

正如所料，当 pylints 解析器访问一个节点时调用 visit 方法，当它离开该节点时调用 leave 方法。

在我们的例子中，我们有两个这样的方法:

1.  `leave_module` —每次当我们离开一个模块时，我们都希望清除这个集合，这样它就不会将聚集的内容从一个模块传递到另一个模块。
2.  `visit_importfrom` —这是验证发生的地方，当我们到达 import from 语句时，我们想要检查我们是否已经遇到了来自具有相同模块名称的语句的导入，并建议开发人员将两个导入语句合并成 1 个。
    我们将首先检查模块名是否在类的集合成员中，如果是，那么我们将发送一个类型为`duplication-module-imports`
    的消息，否则将模块名添加到集合中并继续。
    这是我们选择一个集合的原因，因为它不允许重复，并且它具有常数[O(1)] [时间复杂度](https://en.wikipedia.org/wiki/Time_complexity)用于查找。

现在我们需要使用 pylints `load-plugin`选项运行我们的插件`pylint`。

```
set PYTHONPATH=.pylint --load-plugins pylint_plugins.duplicated_imports_plugin app\__main__.py
```

结果:

```
************* Module __main__
app\__main__.py:6:0: W8801: Import from 'sys' module is duplicated (duplication-module-imports)-------------------------------------------------------------------
Your code has been rated at 8.57/10 (previous run: 10.00/10, -1.43)
```

酷！`pylint`使用了插件提供的规则，检测到了我们想要它检测的错误！

现在让我们重构代码，合并重复的导入语句。

又是棉绒:

```
set PYTHONPATH=.pylint --load-plugins pylint_plugins.duplicated_imports_plugin app\__main__.py
```

结果是:

```
--------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 10.00/10, +0.00)
```

厉害！