# 带类型脚本的高阶函数

> 原文：<https://betterprogramming.pub/higher-order-functions-in-typescript-cae8ad36bd56>

## 举例说明打字稿中的 HOF

![](img/12b7b6686caec458efcf5e7147b0e75e.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Jantine Doornbos](https://unsplash.com/@jantined?utm_source=medium&utm_medium=referral) 拍摄

在这篇文章中，我想简要地阐明什么是高阶函数以及如何使用它们。如果您不熟悉 HOF，那么您可能会感到惊讶，因为如果您使用 JavaScript 或 TypeScript，您可能每天都在使用它们。

# 什么是高阶函数？

高阶函数是一个要么接受另一个函数作为参数，要么返回一个函数的函数。所以基本上我们可以说，一个高阶函数在包装另一个函数。

所以你可能会问自己:*为什么一个函数会接受另一个函数作为参数？*

在 JavaScript 生态系统中解释这一点最简单的方法可能是**回调**。回调无处不在，所以高阶函数无处不在。

# 你可能知道的高阶函数

让我们来看看作为 JavaScript 或 TypeScript 开发人员您肯定遇到过的一些流行的高阶函数。

## `setTimeout()`

是的，`setTimeout`是高阶函数。它将另一个函数作为参数，并在规定的时间后调用这个特定的函数。在这个例子中，大约 100 毫秒后。

## **Array.find()**

`Array. find()`也是一个非常流行的高阶函数，在大多数项目中都出现过几次。实际上，引擎盖下的情况更复杂。因为我们在`find()`中的函数只是返回一个布尔值，但是`Array.find()`本身返回未定义的或者想要的元素。

## **Array.filter()**

另一个数组函数也常用来过滤数组。这个函数也接受另一个函数作为参数，并基于我们的数组返回一个新的列表。

# 创建高阶函数

上面的例子向你展示了 HOF，它将一个函数作为参数，所以我想借此机会向你展示高阶函数返回一个函数的第二种方式。

首先，我们创建一个名为`**requiredAge**`的新函数，它以一个数字作为参数，这个数字是所需的最小年龄。这个函数返回另一个函数，在最后返回一个布尔值。

第二，我们在第 7 行和第 8 行调用这个函数两次，在那里我们基本上设置了每个最小年龄。

最后但同样重要的是，我们正在调用`**requiredAge**`的内部函数四次，检查所需的年龄是否为真。

好吧，这次这篇文章很短，但即使这个概念听起来更复杂，实际上并不复杂，作为 JavaScript 或 TypeScript 开发人员，我们实际上一直在处理高阶函数。

感谢阅读这篇文章。我希望，我可以刷新你的知识，或者更好的是，你可以学到一些新的东西。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为](https://medium.com/@hellokevinvogel/membership)的媒体成员。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？给我买杯咖啡。

# 接下来阅读

[](https://blog.bitsrc.io/solid-principles-in-typescript-153e6923ffdb) [## 打字稿中的坚实原则

### TypeScript 对用 JavaScript 编写干净的代码产生了巨大的影响。但是总有办法…

blog.bitsrc.io](https://blog.bitsrc.io/solid-principles-in-typescript-153e6923ffdb) [](https://blog.bitsrc.io/node-js-event-loop-and-multi-threading-e42e5fd16a77) [## Node.js 事件循环:不是单线程的

### Node.js、事件循环和多线程

blog.bitsrc.io](https://blog.bitsrc.io/node-js-event-loop-and-multi-threading-e42e5fd16a77) [](https://blog.bitsrc.io/binary-search-in-typescript-e999dd3fca5e) [## 打字稿中的二分搜索法

### 在我关于 Medium 的第二篇文章中，我想谈谈二分搜索法和这个算法如何在 TypeScript 上工作。我…

blog.bitsrc.io](https://blog.bitsrc.io/binary-search-in-typescript-e999dd3fca5e)