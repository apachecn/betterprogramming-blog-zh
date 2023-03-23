# 我对 Lodash.js 的问题

> 原文：<https://betterprogramming.pub/my-problem-with-lodash-7e64df8173f9>

## 反对过度使用某些函数的案例

![](img/36256fbd48ee4bbb339cb2317b59eff2.png)

Lodash 被誉为 JavaScript 的基本库，这是有道理的。这是一个提供了大量函数的库，有些函数比其他函数更直观，这使得开发人员的工作更加容易。然而，过度依赖 lodash 会带来效率和代码可读性的损失。

在进入细节之前，我需要坦白，我不是高级程序员，也不是有正规软件背景的工程师。我在软件工程和编码领域已经有一年的经验，也许我在这里说的超出了我的深度。或许 lodash *就是*一个应该学习，应该经常使用的库。然而，对我来说，它伴随着巨大的可读性成本。

我一直有写作的诀窍，写过一个剧本、几个短篇故事和一个短剧，这就是为什么我非常重视代码质量和可读性。对我来说，写代码非常类似于写短篇小说，因为它需要以一种可读的、密集的语法/风格来写，以便更快地抓住要点。在编码的情况下，这个概念近似地转化为效率。

话虽如此，我认为用 lodash 来解决每个遇到的问题是不必要的。例如，spread 操作符使得 lodash 的许多功能变得多余，并且可读性更好。

# 分配

我发现使用 spread 操作符的最好例子是 lodash `_.assign()`函数。本质上，该函数将一个对象的所有值分配给另一个对象:

```
let prop1 = { b: "Some property", d: null };
let prop2 = { a: [2, 3], c: "An amazing String", d: 777 };
let prop3 = { a: 888 };
let prop4 = { a: 999 };const finalProp = _.assign(prop1, prop2, prop3, prop4); console.log(finalProp);
// ==> { a: 999, b: 'Some property', d: 777, c: 'An amazing String'}
```

这无疑是一个非常有用的功能。然而，它非常容易复制，并且在大多数情况下不需要我们导入`lodash`:

```
const finalProp = { ...prop1, ...prop2, ...prop3, ...prop4 };
console.log(finalProp);
// ==> { a: 999, b: 'Some property', d: 777, c: 'An amazing String' }
```

第一种方法可能看起来更好，尤其是如果您已经熟悉 assign()函数的话。然而，就风格而言，有充分的理由选择第二种方法:

1.  没有额外的函数，这使得文件更具可读性。只使用普通的 JavaScript。
2.  它消除了`assign`函数中发生的事情的模糊性，这可能会做一些程序员没有预料到的事情。
3.  您没有向文件中导入任何内容。

它的效率也更高:在两个独立的文件中运行这两个函数，(n=10)对于自定义函数来说平均花费`3.290ms`，而 lodash 方法平均花费`4.218ms`。因此，自定义函数的效率比 lodash 函数高 28%。

# 联盟

这是我最不喜欢的功能。该函数的名字很恰当，来自集合论，尽管这也要求我们更加注意隐藏的复杂性:对两个数组应用 union 函数，不仅连接了两个数组，而且还删除了最终数组的副本:

```
const _ = require("lodash");
const array1 = [1, 4, "hello", 2, 999];
const array2 = [2, "there", 4.7, "hello"];console.time();
const finalArray = _.union(array1, array2);
console.timeEnd();// console.log(finalArray);
// ==> [ 1, 4, 'hello', 2, 999, 'there', 4.7 ]
```

使用相同的对象，我们可以简单地使用`const finalArray = new Set([...array1, ...array2]);`来获得相同的结果，同样提高了效率(`0.578ms`用于 lodash，`0.107ms`用于内部，效率提高了 540%)。除此之外，当显式地写出来时，也更清楚到底发生了什么。

# 克隆和克隆深度

这是一个有趣的问题，因为这是一个我仍然在使用的函数。当第一次接触 JavaScript/编程时，我们中的许多人可能会对如何不容易地创建新对象感到有点不安:

```
let pocket = ["phone", "wallet"];
let anotherPocket = pocket;
pocket.push("keys");
console.log(pocket === anotherPocket);
// ==> true
```

如果我们不幸找到了解决方案，或者我们仓促地接受了教育(比如我)，那么我们可能已经学会了 lodash 解决方案，它复制了函数给出的任何对象:

```
const _ = require("lodash");
let suitcase = { clothes: true };
let vacationItems = { tripleTap: true, bag: suitcase };
const vacationItemsClone = _.clone(vacationItems);
```

虽然这个解决方案确实可以生成给定对象的浅层副本，但是我平均花费了大约`0.611ms`来克隆这个项目。在我看来，更好的解决方案是再次使用 spread 运算符:

```
let suitcase = { clothes: true };
let vacationItems = { tripleTap: true, bag: suitcase };
const vacationItemsClone = { ...vacationItems };
```

扩展操作符帮助我们实现了同样的结果，同时只取了`0.105ms`。这比 lodash 函数快 5.8 倍(效率提高 581%)。另一种类似的克隆方式给出了大致相同的时间范围。当然，给定的对象非常简单，并且可能存在我的解决方案不起作用的例子。否则，就不再需要 lodash 函数了。

但是如何处理`deepCloning`一个对象，使得内部对象引用也被克隆？对于这一点，`lodash`做了一个非常干净，但是效率低下的工作:

```
const _ = require("lodash");let suitcase = { clothes: true };
let vacationItems = { tripleTap: true, bag: suitcase }; console.time();const vacationItemsDeepClone = _.cloneDeep(vacationItems);
```

在我的 mac 上，这个函数平均需要`0.985ms`，而我的自定义函数只需要`0.197ms`(效率提高了 500%):

```
let suitcase = { clothes: true };
let vacationItems = { tripleTap: true, bag: suitcase };
console.time();
const vacationItemsDeepClone = JSON.parse(
    JSON.stringify(vacationItems)
);
console.timeEnd();
```

显然，这是一个比 lodash 中简单的`cloneDeep()`看起来复杂得多的函数，这就是为什么我仍然使用 lodash 中的`cloneDeep()`。我承认`lodash`函数并不是特别高效，但是它让代码更具可读性，你也不用担心 JSON 类试图用 stringy*和*解析一个非常大的对象。

# 结论

Lodash 是一个拥有许多资源的大型图书馆。我经常用 lodash.js 函数，比如`difference()`或者`isEqual()`。然而，我声明过度使用 lodash.js，尤其是对于我提到的一些函数，会对您的项目产生负面影响。

拥有由许多不同的 lodash.js 函数组成的代码，需要新开发人员学习这个包，而不是 JavaScript，这需要时间和精力。在更简单的函数可以使用的地方使用库函数，会降低一个人对任何给定编程语言的理解，最终会降低一个人编码的效率。

*最初发表于*[*【http://github.com】*](https://gist.github.com/MichalBurgunder/58c1350dcf3c78c723e2886cf50c6efe)*。*