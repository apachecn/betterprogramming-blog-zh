# 了解 Pytest 夹具的 5 个范围

> 原文：<https://betterprogramming.pub/understand-5-scopes-of-pytest-fixtures-1b607b5c19ed>

## 用 Python 高效地运行您的测试代码

![](img/bb84c976dbf6f3b0b3e6e0228da8730c.png)

照片由[大卫·特拉维斯](https://unsplash.com/@dtravisphd)在 [Unsplash](https://unsplash.com/) 上拍摄

Python 有三个主流的单元测试框架:内置的`[unittest](https://docs.python.org/3/library/unittest.html)`、`[nosetests](https://nose.readthedocs.io/en/latest/index.html)`和`[pytest](https://docs.pytest.org/en/stable/)`。根据互联网上许多 [*非官方*排名](https://www.google.com/search?newwindow=1&safe=active&sxsrf=ALeKk03skdFP02yOonee-FrLmk_-A5N1GA%3A1591645399975&ei=15TeXveFO4G1sAfhkbLgAw&q=the+best+unit+test+framework+for+python&oq=&gs_lcp=CgZwc3ktYWIQARgHMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnMgcIIxDqAhAnOgQIABBHUJDOAViQzgFg1uUBaAFwA3gAgAEAiAEAkgEAmAEAoAEBqgEHZ3dzLXdperABCg&sclient=psy-ab)，`Pytest`是最受欢迎的测试框架，这要归功于它最少的样板文件、丰富的特性和[越来越多的插件](https://docs.pytest.org/en/2.7.3/plugins_index/index.html)。

[测试夹具](https://en.wikipedia.org/wiki/Test_fixture)是在电子和软件中使用的概念。它是一个软件或设备，建立一个系统来满足过程的某些先决条件。它最大的优点是提供了一致的结果，因此测试结果是可重复的。夹具的例子可以是将测试集加载到数据库，读取配置文件，设置环境变量，等等。

# Pytest 中的 Fixture 是什么？

夹具在 Pytest 中起着相当重要的作用。与通常具有`setup()`和`teardown()`功能的经典 xUnit 风格相比，Pytest 中的夹具更加灵活和扁平。每个 fixture 都是在一个函数上使用`@pytest.fixture` decorator 定义的，这个函数会像读取配置文件一样完成工作。

[pytest-example.py](https://gist.github.com/highsmallxu/b01368fce447b176b80b9a06451fe5e2)

我们调用 fixture 函数的方式不同于我们通常在源代码中所做的。在源代码中，我们调用另一个函数内部的一个函数，然后我们会得到返回对象，像`config = read_config()`。

然而，在 Pytest 中，我们可以*使用 fixture 函数作为测试函数*的输入参数，并且该输入参数已经是返回对象。这种行为称为[依赖注入](https://docs.pytest.org/en/stable/fixture.html#fixtures-a-prime-example-of-dependency-injection)。我们可以根据需要多次注入 fixture 函数。

但是每次注入都会执行一次 fixture 函数。因此，在下面的例子中，它打开同一个文件两次。

有没有办法控制 fixtures 的执行，让我们的测试代码更有效率？是的，这就是为什么我们需要理解 Pytest fixtures 的不同范围。

[pytest-function.py](https://gist.github.com/highsmallxu/25de62736ce0f73bf167d5c141273c0d)

# Pytest 夹具的 5 个范围

Pytest fixtures 有五个不同的作用域:函数、类、模块、包和会话。范围基本上控制了每个 fixture 的执行频率。

## 功能

函数是默认作用域，没有显式添加`scope="function"`。将根据测试功能执行夹具。如果设备只使用一次，或者它包含一个非常简单的操作，或者您每次都想要一个不同的值，那么这个范围就很适用。

[py test-fixture-function . py](https://gist.github.com/highsmallxu/dfd023286097e903b7524119891a0f27)

## 班级

scope 类为每个测试类运行 fixture。如果你有几个做类似事情的测试函数，比如算术运算或数据库查询，你可以用装饰器`@[pytest.mark.usefixtures](https://docs.pytest.org/en/stable/reference.html#pytest-mark-usefixtures)("fixture-name")`把它们放在同一个测试类中。这个特殊的装饰器将 fixture 添加到一个测试类中，fixture 将在任何测试函数之前执行。查看下面的日志。

[py test-fixture-class . py](https://gist.github.com/highsmallxu/7ab383bee1bf1f375a66dc8d85ee403c)

Pytest 中的`yield`语句有一个特殊用法，允许你在所有测试函数之后执行 fixture *。`yield`之前的代码作为设置代码，`yield`之后的代码作为拆卸代码。一个典型的例子是测试数据库。*

[py test-fixture-class-db . py](https://gist.github.com/highsmallxu/c33fb362226637267223dca1f60d38e0)

## 模块和封装

到目前为止，您一定已经观察到了这里的模式。因此，作用域模块运行每个模块的 fixture，作用域包运行每个包的 fixture。范围模块比包使用得更频繁。让我们回到第一个例子，我们有一个打开配置文件的`read_config`夹具。这是使用作用域模块或更高作用域的一个很好的用例，因为我们只想打开文件一次，并多次重用配置对象。您可以将下面的日志与 [pytest-function.py](https://gist.github.com/highsmallxu/25de62736ce0f73bf167d5c141273c0d) 的日志进行比较。你能看出区别吗？

[py test-fixture-module . py](https://gist.github.com/highsmallxu/f5b0be8eb689c0e78c4d73516f19728e)

## ***会话***

最后也是最广泛的作用域是会话。具有作用域会话的装置在每个会话中只执行一次。每次运行`pytest`，都被认为是一个会话。作用域会话是为昂贵的操作设计的，例如截断表和将测试集加载到数据库。

## **conftest.py**

有了范围函数、类和模块，将 fixtures 和 tests 放在同一个文件中是有意义的。但是使用 scope session，有一个更好的地方来放置这些设备。这是`[conftest.py](https://docs.pytest.org/en/2.7.3/plugins.html?highlight=re#conftest-py-local-per-directory-plugins)`文件，Pytest 的本地每个目录的插件。Pytest 将使`conftest.py`中的夹具对同一目录中的所有测试**可用。下面的例子是对第一个例子的扩展。**

在`conftest.py`中，我们有夹具`read_config`和*作用域*会话。这个 fixture 由两个测试文件中的四个测试函数使用。由于`conftest.py`中的 fixture 对于整个目录都是可用的，*我们不需要在测试文件中显式地导入那些 fixture，Pytest 会为我们做这件事。*

[py test-fixture-session . py](https://gist.github.com/highsmallxu/ad3618d0176364e32307360484a03f1b)

# 自动使用装置

到目前为止，我们不得不使用 fixture 作为测试函数的输入参数来触发它。但实际上，一个 fixture 也可以用另一个关键字`autouse=True`自动触发。在这个例子中，fixture `function_autouse`被自动执行，而没有在任何地方被提及。

但是`autouse=True`并不意味着它将是整个比赛中第一个被触发的比赛。还是和它的范围有关:

[py test-fixture-autouse . py](https://gist.github.com/highsmallxu/b3f2a47380ffeb2ba52935104ca4cfbe)

# 夹具的执行顺序

我们先记住基本的执行顺序:*会话>包>模块>类>函数。查看下面的日志。*

[py test-fixture-order . py](https://gist.github.com/highsmallxu/7096835e696cbc8b119574796ffcb7f1)

现在，让我们为每个范围再添加一个带有`autouse=True`的夹具，并考虑顺序:

```
test/test_code1.py::test_order 
-------- live log setup --------
INFO     root:test_code1.py:13 scope session with autouse
INFO     root:test_code1.py:8 scope session
INFO     root:test_code1.py:23 scope package with autouse
INFO     root:test_code1.py:18 scope package
INFO     root:test_code1.py:33 scope module with autouse
INFO     root:test_code1.py:28 scope module
INFO     root:test_code1.py:43 scope class with autouse
INFO     root:test_code1.py:38 scope class
INFO     root:test_code1.py:48 scope function with autouse
INFO     root:test_code1.py:53 scope function
```

这是你所期望的吗？基本上，该顺序基于两条规则:

*   首先执行更高范围的 Fixtures。
*   带有`autouse=True`的 fixture 在相同范围内的其他 fixture 之前执行。

# 结论

Pytest fixtures 通过使用依赖注入或自动触发(autouse)多次重用相同的对象或相同的函数来提高测试代码的效率。fixture 的五个不同范围控制每个 fixture 的执行频率。fixture 的范围越大，fixture 执行得越早。

此外，`conftest.py`是一个放置带有范围会话的夹具的好地方。它们对整个目录都可用，不需要显式导入。

我希望你喜欢这篇文章！如果你有任何想法，请在下面留下你的评论。

# 参考

 [## pytest fixtures:显式、模块化、可扩展——py test 文档

### 软件测试夹具初始化测试功能。它们提供了一个固定的基线，这样测试可以可靠地执行，并且…

docs.pytest.org](https://docs.pytest.org/en/stable/fixture.html) [](https://pythontesting.net/framework/pytest/pytest-fixtures/) [## pytest fixtures 第 1 部分— Python 测试

### 处理夹具是 pytest 真正出色的领域之一。这是相当难以置信的轻描淡写。的…

pythontesting.net](https://pythontesting.net/framework/pytest/pytest-fixtures/)