# 在 JavaScript 中有效地使用字符串作为数组

> 原文：<https://betterprogramming.pub/use-strings-as-array-without-memory-leak-in-javascript-efff16992b02>

## 用不寻常的方式解决日常任务

![](img/4a500162c1e7ea9341edc8775144d590.png)

由[弗勒](https://unsplash.com/@yer_a_wizard?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

你有没有试过从不同的角度看 JavaScript 中的一个字符串？你知道我们可以把它转换成一个数组，但是它实际上可以表现得像一个真正的数组。

你好，世界！今天我想和你分享一些很酷的东西，可能会对你的工作有所帮助，或者至少在面试中展示你的知识。

# 日常任务

我们的例子很简单，但是它将帮助你改变关于这个主题的范式。

例如，当你必须把一个单词中的每个字母分开，然后再把它放回去时，你可能会面临一个问题。通常，我们使用`split`将一个字符串转换成一个数组，然后再用`join`将它恢复到初始状态，但是使用了所需的分隔符:

```
const str = 'Hello';
str.split('').join('-'); // 'H-e-l-l-o'
```

# 字符串作为数组

正如 David Flanagan 在他的书 *JavaScript:权威指南*中所说，JavaScript 中的字符串模仿数组。我们很容易证明这一点。不使用`charAt`方法，我们可以用方括号的用法得到所需的字母。是的，这看起来不像是一个令人信服的论点，但至少是这样的:

```
const str = 'Hello';
str.charAt(1); // 'e'
str[1];        // 'e'
```

# 它是如何工作的

首先你需要提醒自己一个函数方法:`bind`。

```
functionName.bind(this, args);
```

`bind`返回一个带有变异`this`的函数。我相信这是很清楚的，特别是如果你已经使用了 React 和类组件，并且根本没有使用箭头函数。

接下来的方法是`call`。它做的事情和`bind`几乎一样，但是它没有返回一个新的函数。相反，它立即用一个变异的`this`调用它。

最后但同样重要的是一个类的静态方法。`Array Class`可以为我们提供`join`作为静态方法。这意味着我们可以避免`Array Class` 的任何实例来获得`join` **的功能。**

所以，你可能会觉得解决方案近在咫尺。我们可以用变异的上下文调用静态方法`Array`，在我们的例子中，上下文将是我们的字符串。与常规的 `join`一样，我们可以传递一个分隔符`'-'`:

```
const str = 'Hello';Array.prototype.join.call(str, '-'); // 'H-e-l-l-o'
```

# 这是干什么用的？

这个问题问得好。

实际上，标题的“高效”部分来了。我决定挑战我的想法，即调用`prototype`不会像传统的`split.join`那样使用那么多内存。

我们有三种情况:`split.join`、`Array.prototype.join`和`for-loop`(只是为了得到可靠的结果)。

开始之前，请注意以下几点:

*   每个案例都用长度为 4，000，000 的字符串进行了测试。
*   每个箱子有 10 个循环。
*   测试在苹果 MacBook Air 2018 上运行。

第一种情况是常规的`split.join`:

![](img/e968ba7916ef691d9846d9e3e8a3908e.png)

*   平均速度:247.6675 毫秒
*   平均内存使用量:244.191 Mb

第二个——`Array.prototype`:

![](img/aeac84730031240a94f29f7139595d35.png)

*   平均速度:399.5979 毫秒
*   平均内存使用量:223.265 Mb

最后一个——`for-loop`:

![](img/4a301562fa8a6e66235ca49703b32358.png)

*   平均速度:1186.4507 毫秒
*   平均内存使用量:281.996 Mb

# 摘要

我们能从这些测试中得到什么？因此，从内存使用的角度来看，`for-loop`是最慢的(和往常一样)也是最复杂的。`split.join`被证明是最快的方法，但是不出所料，它比`Array.prototype.join`占用更多的内存。很明显是因为最后一个不用创建数组存储在内存中。同时，`Array.prototype.join`并没有那么快，因为它仍然需要处理上下文，这也需要一些时间。

如果你认为 10 次测试不足以做出决定，你可以用你的源代码自己测试。别忘了在下面分享你的结果。

注意:本文不是关于用`'-'`拆分长字符串。我只是想强调您可以使用 JavaScript 的不同部分以不同寻常的方式解决日常任务的方法。