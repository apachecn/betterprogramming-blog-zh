# JavaScript 中的嵌套析构赋值

> 原文：<https://betterprogramming.pub/javascript-nested-destructuring-assignment-de7348cfc04b>

## 如何获取数据内部的数据

![](img/9c38c6edba7efd3ce30fe6abed3f37a9.png)

照片由[肖恩·惠兰](https://unsplash.com/@sswhelan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# Javascript 析构赋值

现代 JavaScript 允许我们从现有的集合、对象、数组或值中快速定义变量。这种简写叫做[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)，简称析构。如果一个集合包含其他集合作为元素，这些集合也可以被析构。

# 简单对象析构

这里我们有一个简单的对象`obj`，我们从这个对象中取出`obj`键`b`和`d`的值，并分配给新的同名的`const`变量`b`和`d`。其他键`a`、`c`和`e`没有被析构，因此没有被赋给同名的变量。

# 使用“Rest”语法进行对象析构

如果我们想显式地从`obj`中取出几个项目，然后在单个变量下取出剩余的项目，我们可以使用`[rest](https://medium.com/@pddivine/javascript-ellipses-the-spread-and-rest-syntax-c12df294548d?source=friends_link&sk=2ff12313e119d8511b805d30ce7e7305)` [语法](https://medium.com/@pddivine/javascript-ellipses-the-spread-and-rest-syntax-c12df294548d?source=friends_link&sk=2ff12313e119d8511b805d30ce7e7305):

# 简单数组析构

析构也适用于数组。在数组的情况下，第一项按顺序取出:

# 用“Rest”语法析构数组

如果我们想取出前几个项目，然后将剩余的项目放在一个新的数组中，我们可以使用`rest`语法。

# 重命名析构变量

有时我们想要重命名我们析构的变量——可能是因为命名冲突或者为了清晰起见。我们可以这样做，在析构项后面加上一个冒号`:`和我们需要的变量名，就像这样:

# 嵌套对象和数组析构

这里我们有一个对象，每个键的值都有一个数组。如果我们想要一个嵌套的值，我们可以向下析构到那个层次。

这是另一个对象数组的例子:

你可以随心所欲地破坏:

如您所见，键`a`、`b`和`c`没有被隐式定义，尽管我们在`c`从数组中取出了嵌套值`firstElemOfC`和`remainingElementsOfC`。

如果我们还想定义`a`、`b`和`c`，我们可以在析构中显式地这样做:

# 嵌套析构的陷阱

嵌套析构不能做的一件事是析构一个空值或未定义的值。如果我们试图这样做，它将抛出一个错误:

因此，如果我们想使用嵌套析构，我们必须确定嵌套结构存在，或者有意捕捉抛出的错误。

# 摘要

JavaScript 析构、嵌套或其他方式是一种很好的简化方式，允许我们快速地从集合、对象或数组中的值定义变量。

我们可以将它与`rest`语法一起使用，为剩余的元素分配一个变量。我们可以将取出的元素重命名为我们选择的变量名。

希望这篇文章对你有所帮助！