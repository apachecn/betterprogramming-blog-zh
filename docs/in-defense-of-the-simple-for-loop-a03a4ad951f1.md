# 为了保护简单的 For 循环

> 原文：<https://betterprogramming.pub/in-defense-of-the-simple-for-loop-a03a4ad951f1>

## Map、filter 和 reduce 都很棒，但是简单的 for 循环仍然是 JavaScript 程序员的好工具

![](img/77aa38f3c9b2199fd6f30a8186b1b807.png)

彼得·托马斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我最近读了很多文章，推荐开发人员“停止使用`for`循环”,转而使用更专业的循环技术，如`map`、`filter`、`reduce`、`forEach`等。

我在某种程度上同意这个建议。`Map`、`filter`和`reduce`是很棒的工具，我在自己的程序中使用它们获得了很多价值。

但另一方面，简单的`for`循环也没什么不好。For 循环在很多情况下和`map`、`filter`、`reduce`一样好。在其他一些情况下，普通的`for`循环可能是最佳选择。

本文将探讨简单的`for`循环仍然是一个好选择的四种情况。下面的例子是用 JavaScript 编写的，但是一般原则也适用于其他编程语言。

# 1.当你不迭代一个数组的时候

循环通常用于迭代数组，但这不是使用它们的唯一方式。有时候，你只是想从一个数字开始向上或向下计数，并对该范围内的每个数字做一些事情。

通过一个简单的`for`循环，这很容易:

```
for (let i = 99; i > 0; i--) {
  console.log(`${i} bottles of beer on the wall...`)
}
```

或者，用数组方法实现这一点更麻烦:

```
[...Array(99).keys()]
  .map(index => 99 - index)
  .forEach(i => console.log(`${i} bottles of beer on the wall...`))
```

# 2.当性能是关键考虑因素时

简单的`for`循环执行速度非常快，可以用来加速将性能放在首位的应用程序。这些可能包括以下内容:

1.  处理大量数据的程序。
2.  具有多维数组的程序，这些数组要求循环嵌套在循环嵌套中。
3.  非常短的时间间隔对企业运营非常重要的任何情况。想想高频金融交易系统。

For 循环几乎总是在性能上占优势，因为它们没有创建回调函数的开销。

注意:这可能不适用于大多数运行 JavaScript 的应用或网站！

大多数应用程序都有更大的目标来优化它们的性能，比如最小化静态资产、减少数据库查询、实现智能缓存等。

循环迭代速度是你通常需要担心的最后一件事！

# 3.当你执行“副作用”时

`Map`、`filter`和`reduce`是高阶函数，它们需要一个回调函数作为参数。

这个回调函数应该使用所提供的参数来返回值，并且不应该做任何其他事情(它应该是一个“纯”函数)。例如，您不会希望它做诸如改变浏览器 DOM 或写入服务器日志文件之类的事情。这类事情被称为“副作用”。

副作用不适用于函数数组方法，但它们仍然是 JavaScript 程序的重要组成部分，有时需要在循环中完成。

当需要这样做时，一个简单的`for`循环是一个不错的选择:

```
const root = document.getElementById('root')
const names = ['Lisa', 'Bart', 'Maggie']
for (let name of names) {
  root.innerHTML += `<li>${name}</li>`
}
```

`Map`可以用来做同样的事情，但是以这种方式使用它通常是不可取的，原因如上所述:

```
const root = document.getElementById('root')
const names = ['Lisa', 'Bart', 'Maggie']
names.map(name => {
  // not a pure function!
  root.innerHTML += `<li>${name}</li>`
})
```

然而，您可以将副作用从回调函数中移除，并在这个带有`reduce`的特定示例中获得类似的结果。

这被认为是对上述示例的改进，但比简单的`for`循环示例需要更多的工作:

```
const root = document.getElementById('root')
const names = ['Lisa', 'Bart', 'Maggie']
root.innerHTML = names.reduce((html, name) => {
  return `${html}<li>${name}</li>`
}, '')
```

# 4.当你编写独特而复杂的算法，不适合任何特定的数组方法时

有经验的程序员知道一个程序运行的方式是没有数量限制的。这适用于我们在全球范围内的应用程序，也适用于特定算法在较小范围内的运行方式。

如果我们正在编写一个接受一组值的算法，并且需要对该组中的每个值应用一个公共转换(“映射”)，那么使用像`map`这样的工具可能是完美的。

如果我们正在编写一个接受一组值的算法，并且需要“过滤”掉该组中的特定值，那么使用像`filter`这样的工具可能会很好。

如果我们正在编写一个算法，该算法接受一组值，并需要将该组值“简化”为特定值(例如，将物料清单简化为总成本，或将一组考试分数简化为班级平均值)，那么使用类似`reduce`的工具是理想的。

`Map`、`filter`、`reduce`等数组方法对于解决特定类型的问题非常优秀，但它们并不是为解决所有可以想象的问题而设计的。

另一方面，简单的`for`循环不是为任何特定类型的算法定制的，它给了开发人员很多控制权。这使得它成为处理那些用现成的数组方法不容易处理的棘手的小问题的一个很好的工具。

它甚至附带了一些不错的小关键字，比如`continue`可以让你跳过一次迭代，或者`break`可以让你完全终止循环。

是的，`reduce`也非常灵活，可以用来解决大多数(所有？)数组算法。但是仅仅因为你能，并不意味着你应该。

`Reduce`应在语义上使用。一些算法可能采用输入值的一个小而简单的子集，并将其转换为一组更大、更复杂的输出值。

在这种情况下，说输入数组被“简化”为输出数组有意义吗？不会的。

# 摘要

作为一名开发人员，在您的工具包中拥有`map`、`filter`、`reduce`和其他数组方法是很好的，但是仍然有简单的`for`循环的空间。ES6 甚至引入了`for...of`循环和`for...in`循环来支持经典的增量版本。

计算编程是一门非常微妙的手艺，当你深入研究时，许多关于最佳实践的规则都有例外。

高阶数组方法通常是提高代码质量的好方法，但在以下情况下，简单的`for`循环可能是正确的选择:

1.  你不是在一个数组上迭代——你只需要一个递增的整数。
2.  你需要循环尽可能快。
3.  你需要在循环中产生副作用。
4.  您正在处理一个独特或复杂的算法，没有一个可用的数组方法非常适合它。