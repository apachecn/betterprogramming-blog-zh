# Swift 中的高阶函数

> 原文：<https://betterprogramming.pub/higher-order-functions-in-swift-13c31a769c0c>

## 在代码中引入“map”、“reduce”、“filter”、“flatMap”和“compactMap”

![](img/bd03e87fd1671b579c4b3defa9962d73.png)

照片由 [Avel Chuklanov](https://unsplash.com/@chuklanov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[![](img/4559de80fb63bfa9a7c3d7a9ceaa962f.png)](https://raulferrer.dev)

在之前的文章中，我们已经看到了如何通过[设计模式](https://medium.com/be-developer/design-patterns-in-software-b5fc4baa9595)和[坚实原则](https://medium.com/better-programming/solid-principles-application-to-swift-development-1de8d7c57fdf)的使用来改进我们代码的架构。现在我们将看看如何用高阶函数来改进我们的 Swift 代码。当然，您已经不止一次地使用过它们，但是它们是什么，您如何用高阶函数来改进您的 Swift 代码呢？

# Swift 高阶函数

*高阶函数*是以其他函数或闭包为自变量，返回一个函数或闭包的函数。这些函数与数组、集合和字典一起使用，并作用于它们包含的元素(这是通过使用 point 语法应用于集合元素的方法来完成的)。

一些最著名的函数是`map`、`compactMap`、`flatMap`、`reduce`、`filter`、`contains`、`sorted`、`forEach`和`removeAll`。

# '地图'

`map`函数的工作方式是对一个集合的所有元素执行操作，并返回一个包含该操作结果的新集合。

例如，假设我们有一个数组，里面有几个单词，所有的字母都是小写的，我们想获得一个新的数组，每个单词都是大写的。我们可以用一个`for` … `in`循环来实现:

让我们看看如何用`map`函数来实现。如[苹果文档](https://developer.apple.com/documentation/swift/array/3017522-map)所示，声明为:

`transform`接受集合或序列中的元素作为参数，并返回相同类型或不同类型的转换值。

在本例中，我们看到应用如下:

它所做的是遍历整个元素数组，对每个元素应用`uppercased()`方法，并返回一个包含这些值的新数组。

无论如何，我们可以通过使用简写参数`$0`来简化这个表达式，它引用数组的任何元素:

# '紧凑地图'

现在假设在前一个例子的数组中有`nil`值。如果我们使用该函数，我们必须考虑要作用的值是否是`nil`:

但是如果我们真正想要的是得到新的数组，但是没有`nil`值，那该怎么办呢？为了实现这一点，我们使用了`compactMap`功能。

因此，在本例中，我们看到:

换句话说，`compactMap`遍历数组中的所有元素，并将该方法应用于非`nil`值，并在数组中返回它们，在本例中是类型`String`(也就是说，`String`的值不是可选的)。

# '平面地图'

`flatMap`函数允许我们将一组数组转换成包含所有元素的单个集合。正如苹果在其[文档](https://developer.apple.com/documentation/swift/sequence/2905332-flatmap)中所宣称的:

例如，让我们先来看看如果没有`flatMap`我们会怎么做:

但是使用`flatMap`，我们可以将代码简化如下:

# '减少'

`[reduce](https://developer.apple.com/documentation/swift/array/2298686-reduce)`是一个函数，当应用于一个集合时，返回该集合的元素的组合结果:

例如，如果我们有一个数字为 1-10 的数组，并且我们想要获得它们的和，我们可以通过以下方式实现，而不使用 reduce 函数:

使用`reduce`，我们只需执行以下操作:

在第一次迭代中， *x* 的值是 0(正如我们在函数中指出的)，而 *y* 的值是 1。所以 *x + y* 的结果将是 1。在第二次迭代中， *x* 为 1， *y* 为 2。所以结果会是 3。诸如此类。

用一种更简单的方式，我们可以写:

# '过滤器'

顾名思义，`[filter](https://developer.apple.com/documentation/swift/sequence/3018365-filter)`函数过滤集合的内容并返回一个新的集合，该集合包含满足特定条件的元素:

例如，假设我们有第一个例子中的单词集合，我们想得到一个包含字母`o`的新单词集合。如果没有过滤功能，我们可以用下面的方法:

在这种情况下，我们没有使用`contains`函数，因为，正如我们将在后面看到的，它也是一个高阶函数。

现在我们将使用`filter`函数来简化这段代码:

但它不必应用单一条件；我们可以应用几个条件。例如，在前面的例子中，我们可以让它返回包含元音字母`o`并且长度为`5`个字符或更多的单词:

# 包含

在前面的例子中，我们使用了`contains`函数来确定一个单词是否包含元音`o`。嗯，`contains`是一个高阶函数，允许你检查是否有满足某个条件的元素，并根据它们是否满足条件返回`true`或`false`。

正如 Apple 在它的[文档](https://developer.apple.com/documentation/swift/array/2945493-contains)中指出的，`contains`返回一个布尔值，表明序列是否包含给定的元素。

# '已排序'

在许多情况下，我们会找到一些想要以某种方式显示的元素。例如，在到目前为止看到的单词数组示例中，这些单词不是按字母顺序排列的。

但是如果我们想按字母顺序排序呢？我们可以使用一些算法，比如:

我们可以使用`[swapAt](https://developer.apple.com/documentation/swift/array/2893281-swapat)`方法减少这段代码，它允许我们交换序列中两个元素的位置:

为了进一步减少这段代码，我们可以使用`[sorted](https://developer.apple.com/documentation/swift/array/2945003-sorted)` 功能。该函数返回按升序排序的序列元素(只要集合的元素采用`Comparable`协议):

另一方面，如果我们想使用我们自己的条件对集合进行排序，我们使用函数`sorted(by:)`，正如 Apple 在其[文档](https://developer.apple.com/documentation/swift/array/2296815-sorted)中指出的，该函数返回序列的元素，使用作为元素之间的比较给出的谓词进行排序。

例如，如果我们希望单词按反字母顺序排列，我们可以这样做:

在这种情况下，通过指示符号`>`，我们以降序对集合进行排序。

# ' forEach '

在`[forEach](https://developer.apple.com/documentation/swift/array/1689783-foreach)`里面`continue`和`break`都不能用，只能用`return`:

# '全部删除'

高阶函数`[removeAll(where:)](https://developer.apple.com/documentation/swift/array/3017530-removeall)`允许我们从满足特定条件的序列中删除元素:

例如，如果我们想从一个序列中删除所有偶数，我们可以使用`removeAll`执行以下操作:

在 Apple 在其文档中展示的例子中，可以更清楚地看到`removeAll(where:)`的威力，在这个例子中，它使用它来删除一个短语的元音:

# 函数串联

一阶函数可以连续、级联应用。例如，我们可以获取一个包含数字数组的数组，并计算它们的和:

首先，我们应用`flatMap`函数获得一个包含所有数字的数组。然后我们应用`reduce`函数来添加它们。

# 结论

我们刚刚通过一些例子看到了一些最常用的高阶函数及其威力。这些函数允许我们，一方面，减少代码量，另一方面，使我们的代码更加清晰和简洁。