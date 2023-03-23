# JavaScript 中的集合介绍

> 原文：<https://betterprogramming.pub/an-introduction-to-sets-in-javascript-5bebaace3135>

## 使用集合处理数据集中的唯一元素

![](img/bb76bc68dea9db2c73ef042d65d46770.png)

约书亚·科尔曼在 [Unsplash](https://unsplash.com/search/photos/sets?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

集合是 ES6 中包含的一种新的对象类型，允许创建**唯一值**的集合。集合中的值可以是简单的原语，如字符串和整数，也可以是更复杂的对象类型，如对象文字或数组。把集合想象成一个没有重复元素的数组。

在许多情况下，我们有一个包含许多元素的数组，我们希望将这些元素转换成一个唯一的数组。虽然它可以通过数组上的暴力、检查元素、使用循环、不同的回调方法等来完成。，最好的方法是使用 set()。

语法:

```
new Set([*iterable*]);
```

我们先来看一个例子:

从示例中可以清楚地看到，通过从数组中创建一个新的集合并使用 spread 运算符，可以将一个集合创建为一个数组。

# 设置方法

现在让我们看看一些基本的 set 方法，你可能不需要它们，但是最好熟悉它们以便优化你的代码:

*   `add`
*   `size`
*   `has`
*   `forEach`
*   `delete`
*   `clear`

非常简单，`add`方法添加元素，`delete`方法删除元素，`has`方法与数组中的`includes`具有相同的功能，`clear`方法清空集合。

正如我们在语法中看到的，`set`方法将 iterable 作为参数，因此字符串也可以用作参数。

现在让我们看看`set`方法的实际例子:

# **结论**

最后，我想总结一下，在处理数据集中的独特元素时，应该使用集合。数组应该是首选，因为它们有足够的方法和函数来处理几乎所有的问题。