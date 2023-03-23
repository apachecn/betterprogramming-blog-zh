# 使用 Swift 的高阶函数简化了 5 种复杂算法

> 原文：<https://betterprogramming.pub/5-complex-algorithms-simplified-using-swifts-higher-order-functions-fa94a32a72c3>

## 映射、减少、过滤等的真实示例

![](img/99a03ee6fcd3f5fec23aed1adc6af0bd.png)

Clark Van Der Beken 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

作为开发人员，我们经常需要处理复杂的算法，这些算法需要几个小时甚至几天才能开发出来。感谢 Swift 的高阶函数如`map`、`reduce`、`filter`等。，其中一些复杂的算法现在只需几行代码就可以轻松解决。

在本文中，我将向您展示五种算法，这些算法过去很难实现，但由于 Swift 中的高阶函数，现在变得非常容易实现。

在整篇文章中，我将使用下面的`students`数组作为模型对象，这样您可以更好地了解这些高阶函数是如何工作的:

让我们开始吧！

# 1.根据条件对数组元素进行分组

假设我们想按学生名字的第一个字母对他们进行分组。传统上，我们必须手动遍历数组中的每个元素，并相应地对它们进行分组。

现在，在`Dictionary(grouping:by:)`初始化器的帮助下，我们不需要使用`for-in`循环就可以实现。方法如下:

从上面的示例代码中可以看出，初始化器将生成一个类型为`[KeyType: Student]`的字典。如果我们想按标准对学生分组并在多部分表格视图中显示他们，这尤其有用。

我们可以通过使用 Swift 中的简写参数名称或关键路径语法来进一步简化该代码:

*想知道如何通过自定义对象对数组元素进行分组？查看我以前的文章“* [*在 Swift*](https://swiftsenpai.com/swift/group-array-elements-with-dictionary/) *中用字典对数组元素分组。”*

# 2.计算数组元素的出现次数

计算一个数组中元素的总数很容易，但是如果我们想根据某些标准计算元素的出现次数呢？例如，假设我们想知道`students`数组中有多少男女学生。

一种方法是使用我们刚刚看到的`Dictionary(grouping:by:)`初始化器:

上面的方法可能会给我们想要的结果。但是，它确实有一些内存开销。如你所见，初始化器将生成我们并不真正需要的男女学生数组。我们需要的只是男女学生的发生。

为了克服内存开销，我们可以利用数组的`reduce(into:)`函数。让我们来看看下面的示例代码:

这个示例代码的作用是将`students`数组简化成一个`[Gender: Int]`类型的字典。在闭包内，我们通过计算男女学生的出现次数来累积填充最终的字典。

现在您已经理解了如何使用`reduce(into:)`函数来计算出现次数，让我们通过给`result`字典一个默认值`0`来进一步简化示例代码，如下所示:

这样，我们避免了内存开销，同时保持了代码的简单和整洁。

# 3.获取数组的和

接下来，我将向你展示如何用一行代码得到一个数组的和。假设我们想得到学生年龄的总和。为此，我们可以像这样使用数组的`reduce(_:_:)`函数:

正如您可能已经猜到的，我们可以通过使用简写的参数名称来进一步改进示例代码:

```
let sum = students.reduce(0, { $0 + $1.age })
```

对于数组元素类型支持加法运算符(`+`)的情况，我们可以通过省略简写参数名称来进一步简化它:

很酷，不是吗？

# 4.通过 ID 访问数组元素

在处理数组时，我们需要执行的最常见的操作之一是使用对象 ID 查找特定的数组元素。最直接的方法是使用一个`for-in`循环或者数组的`filter(_:)`函数来遍历每个数组元素。

对于大多数情况，这两种方法都足够好了。然而，它们都有 *O(n)* 的时间复杂度，这意味着当数组变大时，它们将花费更多的时间来找到特定的元素。对于追求速度和响应的应用程序，这些方法肯定会造成性能瓶颈。

为了使这个操作具有 *O(1)* 的复杂性，我们可以将`students`数组转换成一个字典，其中键是学生 ID，值是`Student`对象。

为此，我们将首先利用数组的`map(_:)`函数将数组转换为包含学生 ID 和`Student`对象的元组数组。之后，我们将使用`Dictionary(uniqueKeysWithValues:)`初始化器将元组数组转换成字典。

请注意，将数组转换为字典的过程仍然是一个 *O(n)* 操作。然而，我们只需要做一次。一旦字典准备就绪，对字典执行的任何读取操作都是一个 *O(1)* 操作。

# 5.从数组中获取多个随机元素

最后这个例子不涉及任何高阶函数，但我觉得还是值得分享的。从数组中获取大量随机元素曾经是一个很难实现的算法，因为我们需要处理各种各样的边缘情况。

现在，在 Swift 数组中的`shuffled()`和`prefix(_:)`函数的帮助下，这个操作变得非常容易实现。

下面是如何从`students`数组中随机挑选三名学生:

这种方法的一个好处是，即使我们试图获取的元素数量大于数组的总元素数量，它也不会触发“索引超出范围”异常。

# 包扎

上面显示的所有例子都可以通过使用传统的`for-in`循环来解决。然而，这需要我们手动处理各种边缘情况。所以极易出错。

通过使用高阶函数，我们可以大大降低代码的复杂性，从而减少出错的可能性。最重要的是，它使我们的代码更容易维护。

如何看待利用 Swift 的高阶函数降低代码复杂度？你还有其他的例子可以分享吗？

感谢阅读。

*本文原载于 2021 年 7 月 6 日 https://swiftsenpai.com*[](https://swiftsenpai.com/swift/5-complex-algorithms-simplified/)**。**

# *进一步阅读*

*   *[用 Swift 中的字典对数组元素进行分组](https://swiftsenpai.com/swift/group-array-elements-with-dictionary/)*
*   *[Swift 中 Map、Filter、Reduce 和 flatMap 与 for-in 循环的性能对比](https://www.skoumal.com/en/performance-of-built-in-higher-order-functions-map-filter-reduce-and-flatmap-vs-for-in-loop-in-swift/)*
*   *46%的人认为这个 Swift 代码有错误，显然，它没有！*
*   *[你能正确回答这个简单快捷的问题吗？](https://swiftsenpai.com/swift/can-you-answer-this-simple-swift-question-correctly/)*