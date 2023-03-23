# JavaScript 中不应使用“for…in”数组迭代的 3 个理由

> 原文：<https://betterprogramming.pub/3-reasons-why-you-shouldnt-use-for-in-array-iterations-in-javascript-8db3f42d8c73>

## 通过理解数组方法的工作原理来避免错误

![](img/8e42f5cb61800065b9052dab9be50bcb.png)

[Isaiah Rustad](https://unsplash.com/@isaiahrustad?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

如果你想迭代一个数组，为什么不应该使用下面的代码？

嗯，有几个原因。一是官方推荐使用`for…of`语句(还有`forEach`方法和经典的`for`循环)来实现这个目的。但是，使用`for…in`进行数组迭代有什么问题呢？

要回答这个问题，我们需要知道`for…in`实际上做什么。下面是 [MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)对此的描述:

> "`**for...in**` **语句**遍历一个对象的所有[可枚举属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)，这些属性由字符串作为关键字。"

可枚举属性有一个内部可枚举标志`true`。例如，通过简单赋值创建的属性就是这种情况。关于属性默认为可枚举的更多细节，请查看 MDN 条目[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)

# 问题#1:未定义的顺序

第一个问题是迭代的顺序依赖于实现，所以不能保证元素以特定的顺序迭代。通常，当您思考数组迭代时，默认的假设是迭代以指定的顺序发生(通常从第一个条目到最后一个条目，或者相反)。

# 问题#2:扩展数组

在 JavaScript 中，如果给一个索引赋值超过了数组的当前大小，数组会自动扩展。假设我们有一个包含三个元素的数组`let arr = [‘a’, ’b’, ’c’];`，并在索引 5 处分配一个新值:`arr[5] = 'd';`。JavaScript 将使用`undefined`来填补这些空白，因此我们的数组现在看起来像这样:`['a', 'b', 'c', undefined, undefined, 'd']`。到目前为止，一切顺利。

但是为什么这对我们的迭代技术来说是个问题呢？好吧，让我们比较一下那种情况下`for…of`和`for…in`的输出:

这是因为索引 3 和 4 不是数组的可枚举属性。索引 5 是一个可枚举的属性，因为它是直接赋值的。

根据您的用例，这可能是您想要的，也可能不是。然而，它的行为不同于传统的数组迭代，这可能会导致意外。打印的元素数量不再匹配数组的长度。如果你知道你在做什么，并且这正是你想要的用例，使用`for..in`。注意它的行为就可以了。

# 问题#3:自定义属性

如果出于某种原因，将一个属性添加到数组的原型中，可能会出现另一个问题。这是否是一个好的实践是一个不同的讨论，但是您可能无法完全控制代码运行的环境。

例如，如果您正在编写一个其他人可以使用的库，请考虑这样一种情况:其他一些程序员认为为数组添加一个方法可能会有用，如下所示:

输出:

```
a
b
c
() => {...}
```

这里发生了什么？嗯，`flatten`现在是我们数组的一个可枚举属性，所以`for…in`对它进行迭代。这肯定不是你想要的。

# 结论

`for…in`并不打算在通常意义上迭代数组。所以在大多数情况下，它是错误的工具。但是，在极少数情况下，将它用于此目的可能是个好主意。只要有意识地去做，注意确切的行为，这样你就不会在你的代码中引入任何错误。

# 参考

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/for...在](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)
*   [https://tc39.es/ecma262/#sec-for-in-and-for-of-statements](https://tc39.es/ecma262/#sec-for-in-and-for-of-statements)