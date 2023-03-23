# 如何在 Python 函数中有效地验证输入类型

> 原文：<https://betterprogramming.pub/how-to-efficiently-validate-input-types-in-python-functions-1f662f45c24c>

## 油漆工来拯救

![](img/ab62431e2d45cc30da30a3e82b3f1d9e.png)

[Denise Jans](https://unsplash.com/@dmjdenise?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当要编写在生产中运行的复杂管道时，清楚地理解每个函数的作用以及它的输出如何影响下游函数是至关重要的。但是，尽管我们尽了最大努力来编写模块化的、经过良好测试的功能，错误还是喜欢藏在功能之间的交接中——即使是端到端的测试也很难捕捉到它们。

本文将介绍一个用于输入验证的 Python 装饰器，我们可以用它来“锁定”函数的输入，并在出现意外的不匹配时立即注意到。

# 示例管道

考虑一个简单的管道，其中我们查询一个 API，清理我们得到的数据，然后保存一个 CSV。我们有一个用户可以触发的主函数(`run_pipeline`)，它接受一个日期范围，运行`query_api`和`process_dict`，然后保存一个 CSV:

很简单，对吧？现在让我们看看`query_api`和`process_dict`。虽然它们看起来也很简单，但实际上有两个失败点——一个比另一个更容易发现。你能看见他们吗？

## 故障 1:对`query_api`的非日期时间输入

你可能已经抓住了失败的第一点。在`query_api`中，如果输入不是`datetime`对象呢？

如果`start_date`和`end_date`不是`datetime`值，当我们试图将这些对象转换成字符串时，我们会在第 16-17 行立即失败。没有什么可以阻止这种情况发生，因为`run_pipeline`(实际上是向用户公开的函数)会毫不犹豫地将其输入直接传递给`query_api`。

我们可以通过检查`run_pipeline`的输入并确保它们是`datetime`对象来解决第一个问题，在继续管道之前立即提醒用户这个问题。

但是我们的第二期呢？你找到了吗？

## 故障二:`query_api`查询失败

从 API 请求数据并不总是有效的。服务器可能脱机，URL 可能无效，或者我们的请求可能导致服务器端的内部错误。为了解决这种可变性并避免我们的管道崩溃，我们将我们的 API 请求放在一个`try` - `except`块中。

很好…除了如果`except`被触发会发生什么。

如果我们的 API 请求出错，我们会记录一条错误消息，但不会显式返回任何内容。默认情况下，Python 函数返回`None`。如果我们的代码采用`except`分支，`query_api`返回`None`，然后将它传递给`process_dict`。`process_dict`然后当我们试图获取应该是字典的`sales`和`date`字段时崩溃。

当我们开始认真地运行我们的管道时，这些问题将不可避免地出现，但是理想情况下，我们应该在开发的早期发现它们。

# 解决办法

如果我们密切关注哪些值进入了我们的函数，我们就能更快地发现错误。虽然类型提示表明我们*期望什么数据类型*，Python 不会阻止错误的输入被传入。因此，我们需要自己编写逻辑来验证输入是正确的。

有几种方法可以验证输入:一种简单的方法是函数中的某种帮助方法，如下面的`_validate_query_api_inputs`:

但是如果我们所做的只是检查输入是否匹配它们的类型提示，我们可能需要一个更通用的函数。否则，我们需要为`run_pipeline`、`query_api`和`process_dict`编写`_validate`函数，它们都有效地做同样的事情。

为了满足这些需求，我编写了一个名为`enforce_type_hints`的装饰器。这个装饰器遍历函数的输入，并确认它们与提供的类型提示相匹配。如果不匹配，`enforce_type_hints`会产生一个`AssertionError`。这里有一个简单的例子:

现在让我们来看看`enforce_type_hints`是如何工作的。想跳过教程看原码？查看 GitHub repo 并提交一份 PR，如果你看到一个使它变得更好的方法！

# `ArgChecker`

因为运行我们的类型检查需要多个组件，所以我将`enforce_type_hints`放在一个名为`ArgChecker`的 Python 类中。`ArgChecker`看起来是这样的:

简而言之，`enforce_type_hints`从函数中获取参数和类型提示，必要时将类型提示转换为原生 Python 类型，然后迭代检查位置和关键字参数是否匹配它们的类型提示。这个检查作为一个`assert isinstance(arg, accepted_types)`调用发生。

# 修理我们的管道

通过用`enforce_type_hints`修饰我们的函数，我们保证了在函数内部执行的任何代码都是参数的类型提示中提供的数据类型。

是的，提高一个`AssertionError`会让我们的管道陷入停顿。但是它立即将我们引向*错误在向下游传播之前发生的地方。移除函数输入中的任何解释空间意味着我们不得不努力思考每个函数(以及它之前和之后的函数)到底应该做什么。*

但是回到我们最初的管道。以下是我如何改变我们的渠道，使其更加稳健:

让管道的输入正确至关重要，因为这可以节省大量的下游错误处理。通过用`enforce_type_hints`锁定输入，如果输入无效，我们会立即停止执行管道。

现在让我们改变`query_api`和`process_dict`:

我们从在`query_api`和`process_dict`上实施类型提示开始。在`query_api`的`except`块中，我们现在返回一个空字典而不是`None`来保持一致的输出。最后，在`process_dict`中，我们检查输入字典是否包含必要的字段。如果没有，我们记录缺失的字段并返回一个空的`DataFrame`。

还有很多其他的漏洞我们可以探究，比如`run_pipeline`是否应该写一个空的 CSV 或者发送某种作业失败的警告。但是我会让你来决定！

# 结论

本文介绍了`enforce_type_hints`，一个装饰器，用于确保函数的输入匹配它们的类型提示。当一个函数接收到一个它没有预料到的输入时引发一个错误可能看起来有点极端，但是它迫使我们批判性地思考*我们的函数到底在做什么*以及上游输出是如何被下游接收的。

有兴趣投稿吗？分叉[GitHub repo](https://github.com/mgsosna/jewelry)并提交 PR。还有很多其他领域可以探索。我个人对用于验证`DataFrame`或`dict`输入是否有必要字段的装饰器感兴趣(如上面的第 34-37 行)。

感谢您的阅读，愿您的管道永远如您所愿。

# 资源

*   [珠宝 GitHub 回购](https://github.com/mgsosna/jewelry)