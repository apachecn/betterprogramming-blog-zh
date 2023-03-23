# 运行时自省 Python 对象的所有方法

> 原文：<https://betterprogramming.pub/all-the-ways-to-introspect-python-objects-at-runtime-80e6991b4cc6>

## 在运行时检查 Python 对象和获取代码信息的技巧和诀窍

![](img/3417822dd372a725c87b89ae7bed83be.png)

杰西·卡森在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Python 提供了很多询问代码问题的方法。无论是像`help()`函数这样的基本东西，像`dir()`这样的内置函数，还是`inspect`模块中更高级的方法——这些工具都可以帮助你找到问题的答案。

让我们看看 Python 能为我们解答哪些关于我们自己代码的问题，以及它如何在调试会话、处理类型注释、验证输入等过程中帮助我们。

# 内置的

正如已经提到的 Python 中有几类自省工具/函数——让我们从最基本的一类开始，即内置函数。

Python 包含了一组基本的[内置函数](https://docs.python.org/3/library/functions.html)，其中大部分我们已经知道了——比如`len()`、`range()`或者`print()`。然而，有几个模糊的问题可以帮助我们回答一些关于我们代码的问题:

`locals()`、`globals()`和`hasattr()`，可以帮助我们找出局部/全局变量或类实例属性是否存在。

此外，我们还可以使用内置函数来检查变量是否是函数:

有几种方法可以做到这一点——最好的选择是使用`callable()`,但是如果你运行的是 Python 3.1，那么你必须使用`hasattr`函数来检查`__call__`属性的存在。最后一个选项是使用来自`inspect`模块的`isfunction()`，不过要小心，因为它会为内置函数返回`False`，比如`sum`、`len`或`range`，因为这些函数是用 C 实现的，所以它们不是 Python 函数。

你可能想检查的另一件事是变量是一个列表(序列)还是一个标量:

“显而易见”的解决方案是使用`isinstance`来检查变量是否是抽象类`Sequence`的实例。然而，这不适用于非内置类型，如 NumPy 数组。它还认为字符串是正确的序列，但可能并不理想。另一种方法是使用`hasattr`来检查变量是否有`__len__`属性，这将适用于 NumPy 数组，但对于字典也将返回`True`。最后一个选项是将一组类型传递给`isinstance`来定制您需要的行为。

我们已经看到了如何使用`globals()`来检查变量是否存在，但是我们也可以使用它通过字符串调用函数:

用`getattr(instance, "func_name")()`可以对对象(类)属性使用类似的策略。

最后一个内置函数的例子使用了`locals()`。假设你有一个函数，它有很多参数，这些参数都需要传递给另一个函数，你不想把它们都写出来。嗯，你可以简单地使用`locals()`和`**`(析构)操作符，就像这样:

上面应该给了你一个如何使用内置函数的好主意，然而这并不是一个详尽的列表。还有几个功能，如`dir()`或`vars()`，所以一定要查看[文档](https://docs.python.org/3/library/functions.html)以获得完整的图片。

# 对象属性

如果上面的内置没有给你所有的答案，那么我们可以挖得更深一点。Python 中的每个对象都有一组非常广泛的属性，可以告诉我们关于特定对象的更多信息。

这些基本上是用来构造`help(object)`输出的值，所以您在`help(object)`输出中发现的任何东西都可以从`object`属性中提取。例如，您可以找到其对象的文档字符串(`.__doc__`)、函数的源代码(`.__code__`)或回溯/堆栈信息(例如`.tb_frame`)。

尽管弄乱属性并不理想，但是有一种更好的方法。我们来看看`inspect` modul。

# `Inspect`模块

`inspect`模块利用所有上述属性(和[更多的](https://docs.python.org/3/library/inspect.html#types-and-members))来允许我们更有效地自省我们的代码。

您可能已经使用了内置的`dir()`方法来获取对象的所有属性。`inspect`模块有一个类似的叫`getmembers()`:

`inspect.getmembers()`的优点是提供了第二个参数，可以用来过滤属性。这里我们用它分别过滤掉变量属性和函数。

`inspect`模块的另一个很好的用例是调试。例如，您可以使用它来调试发电机的状态:

这里我们通过将`yield`放入函数体来定义一个虚拟生成器。然后我们可以使用`isgeneratorfunction()`测试它是否真的是一个生成器。我们还可以使用`getgeneratorstate()`来检查它的状态，它将返回`GEN_CREATED`(尚未执行)、`GEN_RUNNING`、`GEN_SUSPENDED`(等待`yield`)或`GEN_CLOSED`(已消耗)中的一个。

在`inspect.signature`的帮助下，你还可以调试与函数签名相关的东西，比如可变的默认参数:

众所周知，你[不应该为参数默认值](https://docs.python-guide.org/writing/gotchas/)使用可变类型，比如`list`，因为它们会在每次函数执行期间被修改(变异)。用`inspect.signature()`检查函数签名在这里就很清楚了。

关于参数默认值，我们也可以使用`signature()`函数来读取它们:

如果您想将一个函数的默认值传递给另一个函数，这可能会很有帮助。上面的例子表明，您可以遍历所有的参数并挑选出具有非空默认值的参数，或者如果您知道参数名，那么您可以直接查询它。

`signature()`函数的一些更高级的用法包括使用装饰器向函数注入额外的参数:

上面的代码片段定义了一个名为`optional_debug`的装饰器，它在应用于函数时注入`debug`参数。它首先检查函数签名并检查是否存在`debug`参数。如果不存在，它就用一个新的函数签名替换原来的函数签名，并附加一个仅包含关键字的参数。

现在，假设你有一个函数，它只声明了参数`*args`和`**kwargs`。这使得该函数非常“通用”，但是使得参数检查相当混乱。我们可以用`inspect`模块中的`Signature`和`Parameter`类来解决这个问题:

首先，我们使用`Parameter`类定义期望的参数(`params`)，指定它们的类型和默认值，之后我们从它们创建签名。在“通用”函数中，我们使用签名的`bind`方法将提供的`*args`和`**kwargs`绑定到准备好的签名。如果参数不满足签名，我们会收到一个异常。如果一切正常，我们可以使用`bind`方法的返回值访问绑定参数。

上面的方法适用于验证基本函数中的参数，但是如果我们想验证生成器从修饰函数中获得了所有必要的参数，该怎么办呢？

上面的代码片段显示了一个认证装饰器的实现，它期望将`username`参数传递给被装饰的函数。为了验证参数是否存在，我们使用了`Signature`类的`from_callable()`方法，该方法从函数中提取所提供的参数。然后，在执行任何实际逻辑之前，我们检查`username`是否在返回的元组中。

从`inspect.signature`继续，您也可以使用`inspect`模块来自省您的源代码文件:

这里我们简单地使用`getfile`查找`some_func`函数的位置(文件)。这同样适用于内置函数/对象，如上面的`datetime.date`所示。

`inspect`模块还包含了用于回溯的助手。例如，您可以使用它来查找当前正在执行的源文件和行:

有几种方法可以做到这一点，最直接的方法是使用`getframeinfo(currentframe())`，但是你也可以通过`stack()`函数访问堆栈帧。在这种情况下，您必须在堆栈中进行索引，以找到正确的帧。

另一种选择是直接使用`currentframe()`，在这种情况下，您将不得不访问`f_back`和`f_lineno`来分别找到正确的框架和线。

使用回溯助手可以做的下一件事是访问调用者对象:

为此，我们使用`inspect.currentframe().f_back.f_locals['self']`来访问“父”对象。这只应该用于调试——如果你需要访问调用者对象，你应该把它作为一个参数传递给函数，而不是遍历堆栈。

最后但同样重要的是，如果你在代码中使用类型提示，那么你可能熟悉`typing.get_type_hints`，它可以帮助你检查类型提示。然而这个函数通常会抛出`NameError`(特别是在类上调用的时候)，所以如果你在 Python 3.10 上，你可能应该切换到使用`inspect.get_annotations`，它会为你处理很多边缘情况:

有关在 Python 3.10 及更高版本中访问注释的更多细节，请参见[文档](https://docs.python.org/3.10/howto/annotations.html#annotations-howto)。

# 结束语

正如我们已经看到的，有足够多的工具来自省和询问您的 Python 代码。其中一些非常晦涩，您可能永远不需要使用它们，但是知道它们的存在是有好处的，尤其是那些可用于调试的。

然而，如果以上还不够，或者您只是想更深入一点，那么您可能想看看`ast`，它可以用来遍历和检查语法树，甚至是用于反汇编 Python 字节码的`dis`模块。

[](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) [## 你应该使用 Python 的 Walrus 操作符吗？(是的。原因如下)

### Python 有争议的赋值表达式——也称为 walrus 运算符——可以改进您的代码，现在是您…

better 编程. pub](/should-you-be-using-pythons-walrus-operator-yes-and-here-s-why-36297be16907) [](/python-list-comprehensions-are-more-powerful-than-you-might-think-3363a90e5bb0) [## Python 列表理解比你想象的更强大

### 借助这些未知的功能和技巧，写出更好的列表理解

better 编程. pub](/python-list-comprehensions-are-more-powerful-than-you-might-think-3363a90e5bb0) 

```
**Want to Connect?**This article was originally posted at [martinheinz.dev](https://martinheinz.dev/blog/82)
```