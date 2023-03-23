# 10 个令人困惑但至关重要的 Swift 功能

> 原文：<https://betterprogramming.pub/10-confusing-but-crucial-swift-functions-f290dfaaf-f29080bdfaaf>

## is vs. isKind(of:) vs. isMember(of:)，append() vs. appending()，isEmpty() vs. count==0，等等

![](img/0fecfa50f4dda97a0e97749506ec80cc.png)

[大卫·格兰姆金](https://unsplash.com/@davidgrdm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# is vs. isKind(的:)vs. isMember(的:)

`is`是类型检查操作符，检查一个实例是否属于某种类型。它可以用来检查`class`的实例:

`is`操作符也可以用来检查`class`的子类的实例:

`is`操作符可以用来检查`struct`的实例:

`isKind(of:)`与`class`配合使用，采用`NSObjectProtocol`。它检查给定值是`class`的一个实例还是从那个类继承的任何类的一个实例。

`isMember(of:)`与`class`配合使用，采用`NSObjectProtocol`。它检查给定值是否只是`class`的一个实例。

# append()与 appending()

当连接两个`String`变量时，我们通常使用两个名称相似的函数:`append()`和`appending()`。

`append()`是将给定字符串追加到该字符串的变异方法。其工作原理与加法`addition assignment operator` ( `+=`)相同。

`appending()`是继承了`NSString`的类型。它以与`addition operator` ( `+`)相同的方式创建一个新的`String`值。

# sort()与 sorted()

`sort()`和`sorted()`是 Swift `Array`的内置方法，可以对数组`Element`进行排序。当阵列`Element`符合`Comparable`协议时，它们可用。默认情况下，元素按升序排序。

两者的复杂度都是 *O(n log n)* ，其中 *n* 是集合的长度。

`sort()`改变现有数组，默认情况下按升序排列元素。要按降序对元素进行排序，请将大于号运算符(`>`)传递给`sort(by:)`方法。

`sorted()`默认情况下，按升序返回序列中的元素。要按降序对序列中的元素进行排序，请将大于号运算符(`>`)传递给`sorted(by:)`方法。

# 反转()与反转()

`reverse()`和`reversed()`与`Element`的集合一起工作，其中`Element`采用`Comparable`协议。集合可以是一个`Array`、`String`等。

`reverse()`是将集合中的元素就地反转的突变方法。以下是我们如何使用`reverse()`和`Array`:

复杂度为 *O(n)* ，其中 *n* 是集合中元素的个数。

`reversed()`用`ReversedCollection`实例包装底层集合，它以相反的顺序提供对其元素的访问。它不会为集合分配任何新空间。

复杂度为 *O(1)* 。

# 随机()与随机()

从 Swift 4.2 开始，`shuffle()`和`shuffled()`是混洗`Element`实例集合的方法。他们随机地对元素重新排序，但是不需要元素采用`Comparable`协议。

复杂度为 *O(n)* ，其中 *n* 为集合的长度。

`shuffle()`对现有集合进行变异。它可以与一组可比较和不可比较的元素一起使用。

`shuffled()`返回序列的混洗元素，可以是`String`、`Array`等。

# isEmpty() vs. count == 0

虽然`isEmpty()`和`count == 0`都可以用来检查字符串是否为空，但我们建议使用`isEmpty()`而不是`.count == 0`。

`isEmpty()`比较字符串的开始索引和结束索引。

复杂度为 *O(1)* 。

而`.count == 0`循环字符串以计数所有字符。

复杂度为 *O(n)* 。

当把它们放在一起比较时，`isEmpty()`比`.count == 0`更有性能，更容易阅读。

# 结论

在 Swift 的不同功能之间进行选择时，思考总是有好处的。了解细节对我们帮助很大。

感谢您的阅读，请在评论中留下您可能有的任何问题。

上面提到的所有代码都可以在这个 [GitHub repo](https://gist.github.com/ericleiyang/872b9b89b5fb4f14b0dc381c95fd3eb7) 中找到。