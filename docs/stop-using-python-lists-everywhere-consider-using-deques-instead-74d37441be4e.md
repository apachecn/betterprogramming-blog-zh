# 停止到处使用 Python 列表——考虑改用 Deques

> 原文：<https://betterprogramming.pub/stop-using-python-lists-everywhere-consider-using-deques-instead-74d37441be4e>

## 知道何时使用 deques 作为 Python 列表的替代

![](img/999b4e066c46d0b20ac41e3d17787173.png)

乔纳森·肯珀在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

当谈到存储有序序列数据时，许多人可能会把列表作为他们的第一选择。列表可能是最流行的容器数据结构，通常用于存储任何类型的数据，如整数、字符串或自定义实例。这种流行的一个主要原因是列表的可变性。我们可以很容易地在列表中添加和删除条目。

就数据可变性而言，现有数据变量有不同种类的突变。在某些应用中，我们希望我们的数据具有先进先出(FIFO)能力。FIFO 强调先加入序列(先进先出)的项目将先从序列中移除(先进先出)。这种业务需求可以使用 list 对象和 deques 来解决。然而，deques 在这种业务需求上优于 lists，因为它们在实现上存在固有的差异。因此，当考虑 FIFO 时，我们应该考虑使用 deques 而不是 lists。

# 使用列表实现 FIFO

假设我们正在为一家企业构建一个在线客户聊天系统。在整个营业时间，客户登记入住，我们需要有一个列表来跟踪登记入住的顺序。合理的做法是，一旦客户支持人员有空，就让他们先联系客户支持人员。以下代码片段向您展示了使用列表的可能实现:

FIFO 实现列表

*   我们使用一个列表对象(`clients`)来保存数据。
*   当客户进入系统时，我们将该客户添加到等待列表的末尾。
*   每当一个同事有空的时候，我们通过调用`pop(0)`来删除等待列表中的第一个客户。这个方法不仅返回列表中的第一项，还将它从列表中移除。

值得注意的是，我们需要先检查`clients`是否有任何条目，因为在空列表上调用`pop()`会导致一个`IndexError`。利用这两个函数，让我们模拟一个真实的场景:

使用 FIFO 列表

移除列表对象开头的项目是有意义的。在引擎盖下，Python 会对列表中的每一项进行移位，以调整内存中第一项的空位，这是一个开销很大的操作，时间复杂度为 *O(n)* 。鉴于其相当大的时间复杂性，我们应该考虑一个替代解决方案——使用 deques。

# 使用 Deques 实现 FIFO

deque 数据类型是双端队列，其发音与“deck”相同。由于其双端特性，它支持从两端插入和移除，这使它成为实现客户端聊天管理系统的完美数据类型。

如前所述，在 list 对象上调用`pop()`方法是一个耗费时间和内存的操作。

让我们直接比较一下这个特性的列表和 deques。考虑下面的简化设置，该设置专门关注移除等待队列中的第一个项目:

比较 FIFO 操作列表和队列的函数

*   `deque`数据类型可通过`collections`模块获得。
*   为了比较性能，我们使用了`timeit`函数，它可以计算已定义操作的平均运行时间。
*   类似于列表的`pop`方法，deques 使用`popleft`方法删除 deques 数据类型左端的第一项。

通过此功能设置，我们可以在项目数量变化时进行多次比较。

FIFO 性能:队列与列表

在这个简单的例子中，使用 deques 对 lists 的性能提升似乎并不显著，但是对于企业应用程序来说，即使是很小的提升也是非常显著的。重要的是，使用 deque 数据类型不涉及任何复杂的实现，因为 deque 是标准库的一部分。那么，除了使用内置数据类型之外，为什么不在没有任何成本的情况下享受性能提升呢？

下面是模拟应用程序的修改版本:

FIFO 应用程序的队列

如您所见，该实现主要涉及数据模型和弹出序列第一项的方法(即`popleft`)的改变。

# 结论

在本文中，我们回顾了当 FIFO 是我们的应用程序所关心的问题时，deque 数据类型作为列表的替代数据模型。

因为 deques 是一种双端序列数据结构，所以它是为在两端添加或删除项的操作而设计的。因此，我们不应该将数据模型的选择仅限于列表或其他常见的数据类型。当特定的业务需求(例如 FIFO)出现时，我们应该考虑替代的数据模型。