# 减少的力量()

> 原文：<https://betterprogramming.pub/the-power-of-reduce-87bfdb7de04>

## 重构 JavaScript:收集管道(第 2 部分)

![](img/ac0d2a9b9b43f0e188bc750ea726ac64.png)

这是“重构 Javascript:收集管道”系列的一部分。这里可以看介绍[。](https://medium.com/@Michael_Timbs/refactoring-javascript-collection-pipelines-3ebc2e63abee)

在上一篇文章中，我用一个 IP 验证的例子展示了简单的收集管道如何简化代码的可读性。

```
*const* validateIP = (ip: *string*): *boolean* => {
  *const* numbers = ip.split('.');
  *return* numbers.length === 4
    && numbers
      .filter((x) => ***Number***(x).toString() === x).map((x) => parseInt(x, 10)).filter((x) => x >= 0 && x <= 255).length === 4;};
```

这段代码很好，因为它将验证的每一步都分解成了一个步骤。每一步都通过管道将新的集合传递到管道的下一阶段。这使得推断数据流以及它在每一步是如何变化的变得非常容易。

不过，这种方法也有缺点。管道的每一步都需要迭代上一步通过的整个集合。

我们的第一个过滤器需要过滤原始集合中的四个项目；得到的数组被传递给`map()`，它也需要在传递给另一个`filter()`之前对整个集合进行操作，而后者又对整个集合进行操作。我们在这里做了多达 12 个操作(每个贴图/过滤器最多 4 个)。如果我们能把所有这些结合成一个单一的步骤呢？

reduce 函数的典型示例是获取一组数字并对它们求和。它看起来像这样:

```
*const* total = (arr) => arr.reduce((total, val) => total + val, 0);
```

这个很好理解但是用处不大。

`reduce()`比数字相加要强大得多。`reduce()`对集合进行一次迭代，并允许您应用回调——同时提供对每次迭代累积结果的访问。然后，它返回一个值。仔细想想，这给了你很大的力量。比如可以用`reduce()`代替`map()`。

# 用 reduce()实现 map()

```
[1, 2, 3, 4].map((x) => x.toString());
// result [ "1", "2", "3", "4" ][1, 2, 3, 4].reduce((acc, item) => [...acc, item.toString()], []);
// result [ "1", "2", "3", "4" ]
```

在这个例子中，我们已经展示了我们可以使用`reduce()`来复制`map()`,只需转换每个项目，并使用 ES6 spread 语法将其合并到一个累加器数组中。

# 用 reduce()实现 filter()

显然`filter()`可以以类似的方式实现。

```
[1, 2, 3, 4].filter((x) => x % 2 === 0);
// result [ 2, 4 ][1, 2, 3, 4].reduce((acc, item) => item % 2 === 0
  ? [...acc, item]
  : acc
,[]);
// result [ 2, 4 ]
```

我们简单地检查我们的谓词，如果我们得到一个真值结果，我们将这个项目分散到一个累加器数组中；否则，我们将累加器原封不动地传递给下一次迭代。

# 使用 reduce()组合 map()和 filter()

假设一个数组有一百万个条目，而不是四个——那么将连续的贴图/过滤器链接在一起可能会有点慢。令人欣慰的是，你可以使用一个简单的`reduce()`将过滤器和贴图合并到一个步骤中。

例如，这里我们对偶数使用一个过滤器，并将数字映射到一个字符串。

```
*const* ***arr*** = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

***arr***.reduce((acc, item) => (
  item % 2 === 0
    ? [...acc, item.toString()]
    : acc
), []);
['2', '4', '6', '8', '10'];
```

我们首先应用过滤器，然后如果谓词匹配，我们在扩展到累加器数组时进行转换。如果没有，我们返回先前的累加器。

我们可以让我们的`reduce()`函数变得无限复杂，从而在一次迭代中优化我们的管道；然而，我认为，随着我们在我们的`reduce()`函数中加入越来越多的逻辑，我们剩下的并不比使用一个简陋的`for`循环更干净。

# 不错的把戏，但那又怎样？

我的整个系列文章的目标是说服人们开始用`map()`、`filter()`和`reduce()`来思考。这会让你 90%或更多的代码更容易阅读。

在本文中，我的目的是向您展示多个映射和过滤步骤可以合并到一个 reduce 函数中，该函数允许您只对一个集合迭代一次，而不是多次。不过，这在实践中几乎从来都不是问题——也不总是更快！

```
*const* ***smallArray*** = [...***Array***(100)].map((_, i) => i);
// [0,1,2,3,4,... 100]*const* ***bigArray*** = [...***Array***(1e4)].map((_, i) => i);
*// [0,1,2,3,4, .... 9999]**const* time = (f) => {
  *const* s = *new* ***Date***();
  f();
  *console*.log(`${(*new* ***Date***()) - s}ms`);
}; // For small arrays there is no differencetime(() => ***smallArray*** .filter((x) => x % 2 === 0)
  .map((x) => x.toString()));
// 0mstime(() => ***smallArray*** .reduce((acc, item) => (
  item % 2 === 0
    ? [...acc, item.toString()]
    : acc
), []));
// 0ms// For larger arraystime(() => ***bigArray*** .filter((x) => x % 2 === 0)
  .map((x) => x.toString()));
// 8ms 🏎 still pretty fast!time(() => ***bigArray*** .reduce((acc, item) => (
  item % 2 === 0
    ? [...acc, item.toString()]
    : acc
), []));
//32ms 🤯 wait... what!
```

在我们有 10，000 个条目的`bigArray`示例中，将`map()`和`filter()`组合成一个`reduce()`比链接`filter()`和`map()`慢四倍！使用`reduce()`对性能的影响实际上来自于通过 spread 操作符合并数组。

```
[...acc, item.toString()]
```

这里的性能损失比我们连续链接`filter()`和`map()`得到的要大得多。

如果我们没有一条简单的➡管道，而是有一条通往➡、➡、➡、➡、➡和 T22 的管道，会怎么样？

难道`reduce()`仍然比链接所有这些步骤慢吗？没有侧写谁知道呢——这在大多数情况下也无关紧要。

# 那么使用 Reduce 方法而不是 Map/Filter 有什么意义呢？

首先，我想向您展示用`reduce()`替换`map()`和`filter()`的*是可能的，但也强调它并不总是有用的(尽管它经常有用)，并且最好进行剖析以进行检查。*

其次，现在熟悉了`reduce()`之后，我们就可以引入可组合的高阶减速器*(传感器)的概念。*转换器是将一个缩减器作为输入并返回另一个缩减器的函数。传感器让我们建立更高效的数据处理管道。

如果你想要一个使用 reduce()的真实例子，[在这里阅读我的后续文章](https://medium.com/@Michael_Timbs/solving-canvas-technical-challenge-with-a-single-reduce-function-731245b80101)，在那里我使用一个 reduce 函数解决了 Canva 的技术难题！

如果你想了解更多关于重构你的 JS 代码以利用集合管道的知识，你可以在这里预订我的书，只需 10 美元