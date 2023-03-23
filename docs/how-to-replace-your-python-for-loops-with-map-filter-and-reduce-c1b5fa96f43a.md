# 如何用 Map、Filter 和 Reduce 替换 Python For 循环

> 原文：<https://betterprogramming.pub/how-to-replace-your-python-for-loops-with-map-filter-and-reduce-c1b5fa96f43a>

## 用函数式编程编写更多语义代码

![](img/7d4c57d5d44f28fce055a4c30716d4f9.png)

[法托斯 Bytyqi](https://unsplash.com/@fatosi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

你曾经看过你的代码，看到一个 for 循环的瀑布吗？你是否发现自己不得不眯着眼睛，靠向显示器才能看得更清楚？

我知道我有。

For 循环是解决问题的瑞士军刀，但是，当扫描代码来快速阅读您所做的事情时，它们可能会让人不知所措。

三种技术——map、filter 和 reduce——通过提供描述*为什么*要迭代的功能替代方案，帮助治疗 for 循环狂躁症。我[以前写过](https://medium.com/better-programming/how-to-start-using-map-filter-and-reduce-e01edba0d81)关于用 JavaScript 开始使用这些技术，但是用 Python 实现略有不同。

我们将简要介绍这三种技术，强调它们在 JavaScript 和 Python 中的语法差异，然后举例说明如何转换常见的 for 循环。

# 什么是映射、过滤和减少？

回顾我以前编写的代码，我意识到在 95%的时间里，当循环字符串或数组时，我会执行下列操作之一:**将一系列语句映射到每个值，**过滤满足特定标准的值，或者**将数据集减少为单个聚合值。******

有了这种认识，这三种方法就是识别和实现，您循环遍历 iterable 的原因通常属于这三种功能类别之一:

*   **映射**:对每个项目应用相同的一组步骤，存储结果。
*   **过滤器**:应用验证标准，存储评估为真的项目。
*   **Reduce** :返回一个在元素间传递的值。

# 是什么让 Python 映射/过滤/还原与众不同？

在 Python 中，这三种技术以函数的形式存在，而不是数组或字符串类的方法。这意味着你应该写`map(function, my_list)`而不是写`my_array.map(function)`。

此外，每种技术都需要传递一个函数，该函数将为每个项目执行。通常，该函数被编写为匿名函数(在 JavaScript 中称为粗箭头函数)。然而，在 Python 中，你经常会看到使用 lambda 表达式。

lambda 表达式和 arrow 函数之间的语法实际上非常相似。将`=>`替换为`:`，并确保使用关键字`lambda`，其余内容几乎相同。

```
// JavaScript Arrow Function
const square = number => number * number;// Python Lambda Expression
square = lambda number: number * number
```

arrow 函数和 lambda 表达式之间的一个关键区别在于，arrow 函数能够扩展为具有多个语句的完整函数，而 lambda 表达式则限于返回的单个表达式。因此，当使用`map()`、`filter()`或`reduce()`时，如果需要对每个项目执行多个操作，首先定义您的功能，然后包括它。

```
def inefficientSquare(number):
   result = number * number
   return resultmap(inefficientSquare, my_list)
```

# 替换为循环

好了，言归正传。以下是将由 map、filter 和 reduce 替换的循环的三个常见示例。我们的编程提示:计算列表中奇数的平方和。

首先，这个例子用 basic for 循环。注意:这纯粹是为了演示，甚至可以在没有 map/filter/reduce 的情况下进行改进。

```
numbers = [1,2,3,4,5,6]
odd_numbers = []
squared_odd_numbers = []
total = 0# filter for odd numbers
for number in numbers:
   if number % 2 == 1:
      odd_numbers.append(number)# square all odd numbers
for number in odd_numbers:
   squared_odd_numbers.append(number * number)# calculate total
for number in squared_odd_numbers:
   total += number# calculate average
```

让我们将每个步骤转换为以下函数之一:

```
from functools import reducenumbers = [1,2,3,4,5,6]odd_numbers = filter(lambda n: n % 2 == 1, numbers)squared_odd_numbers = map(lambda n: n * n, odd_numbers)total = reduce(lambda acc, n: acc + n, squared_odd_numbers)
```

有几个重要的语法点需要强调。

*   `map()`和`filter()`均为本地提供。但是，必须从 Python 3+中的`functools`库导入`reduce()`。
*   lambda 表达式是所有三个函数中的第一个参数，而 iterable 是第二个参数
*   `reduce()`的 lambda 表达式需要两个参数:累加器(传递给每个元素的值)和单个元素本身。

*最美好的祝愿正在远离堆积如山的 for 循环。重要的是要记住 for 循环确实在代码中有一席之地，但扩展工具包绝不是坏事。*