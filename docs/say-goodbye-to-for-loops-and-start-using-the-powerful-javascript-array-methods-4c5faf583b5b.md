# 告别 For 循环，开始使用强大的 JavaScript 数组方法

> 原文：<https://betterprogramming.pub/say-goodbye-to-for-loops-and-start-using-the-powerful-javascript-array-methods-4c5faf583b5b>

## 使用过滤、映射、简化和其他数组方法快速操作

![](img/53f73fa52f46235b08a10a911f29b30f.png)

作者照片。

`for`循环为我们提供了很好的服务，但是现在是时候为了数组方法而抛弃它了。让我们来看看一些例子，看看你的想法。

在所有的例子中，我们将处理相同的电视节目列表。这是:

# 过滤器

考虑下面的例子，它只从电视节目列表中选择情景喜剧。它仍然在使用一个`for`循环:

我们从一个空的情景喜剧数组开始，遍历节目列表，只将情景喜剧放入其中。

我们能做得比这更好吗？

`filter`数组方法使用谓词函数从列表中选择元素。`filter`不改变输入数组。它创造了一个新的。

谓词是一个[函数](/10-fundamentals-you-need-to-know-about-functions-in-javascript-8e74579b7a9a)，它检查条件是否满足，并返回一个布尔值(`true`或`false`)。

下面是我们检查电视节目是否是情景喜剧的谓词函数:

```
function isSitcom(show){
  return show.type === 'sitcom';
}
```

现在我们可以将谓词函数传递给`filter`数组方法，只从列表中选择情景喜剧:

```
const sitcoms = shows.filter(isSitcom);
```

就是这样。你怎么想呢?

# 地图

现在我们想把电视节目对象列表转换成字符串列表。对于每个对象，我们想要创建一个新的字符串，如下所示:``${show.name} is a ${show.type}``。

下面是如何用一个`for`循环完成转换的:

我们从一个空的字符串数组开始，遍历电视节目列表，并向其中添加计算出的字符串文本。

我认为我们可以做得更好。

使用`map`可以正确地将数组转换成具有相同数量元素的新数组。

数组方法将一个值列表转换成一个新的值列表。新值可以是从原语到对象的任何值。`map`不改变输入数组。它创造了一个新的。

下面是我们将对象转换成字符串的映射函数:

```
function toShowText(show){
  return `${show.name} is a ${show.type}`;
}
```

我们可以将映射函数传递给`map`数组方法，并进行与之前相同的转换:

```
const showsTexts = shows.map(toShowText);
```

你认为它看起来更好吗？

# 减少

考虑下面的代码，它使用一个`for`循环来检测每种类型有多少个节目:

我们从一个空的[对象](https://medium.com/dailyjs/7-differences-between-objects-and-maps-in-javascript-bc901dfa9350) ( `{}`)开始，遍历节目列表，在每一步，我们增加当前电视节目类型的计数。

下面，您可以检查每一步的计数是如何增加的:

第一个节目是一个情景喜剧，所以它的计数被设置为`1`。第二个节目仍然是情景喜剧，因此情景喜剧的计数增加到`2`。第三个节目是一个情景喜剧，所以计数成为`3`。第四个节目是冒险，所以情景喜剧的计数仍然是`3`，冒险的计数被设置为`1`。

让我们使用数组方法重构代码。

`reduce`数组方法使用 reducer 函数将一列值转换成一个新的聚合值。

在每一步，reducer 函数获取聚合值和当前元素，并返回新计算的聚合值。

下面是我们的 reducer 函数，它获取带有每种类型和当前节目计数的聚合[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)，并返回一个当前节目类型计数增加的聚合对象:

```
function countByType(counts, show){
  counts[show.type] = (counts[show.type] || 0 ) + 1;
  return counts;
}
```

`(counts[show.type] || 0 )`表达式返回特定演出类型的计数(如果该类型存在)。否则，计算结果为`0`。

接下来，我们可以将 reducer 函数传递给`reduce`方法，并计算与前面的循环逻辑相同的结果:

```
const counts = shows.reduce(countByType, {})
```

`reduce`方法的第二个参数是计算出的聚合值的初始值。在我们的例子中，这是一个空的[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d) ( `{}`)。

# 每一/一些

在接下来的例子中，使用一个`for`循环来检测是否所有的节目都是情景喜剧，以及列表中是否至少有一个情景喜剧。

我们从设置为`true`的`areAllSicoms`变量开始，遍历列表。当我们发现一个不是情景喜剧的节目时，我们将其设置为`false`并停止循环:

以类似的方式，我们从设置为`false`的`hasSitcoms`变量开始，遍历列表。当我们找到一个情景喜剧时，我们将它设置为`true`，并停止循环:

我认为我们可以改进这段代码。

`every`方法检查[数组](https://medium.com/programming-essentials/how-to-remove-duplicates-from-arrays-61050fc7b62)中的所有元素是否都通过了谓词函数的测试，并返回一个布尔值。如果谓词函数返回一个 falsy 值，它将停止并返回`false`。否则，处理完所有元素后返回`true`。

方法检查数组中是否至少有一个元素通过了谓词函数的测试，并返回一个布尔值。如果谓词函数返回真值，它将停止并返回`true`。否则，在处理完所有元素后返回`false`。

下面是我们可以用来检查一个节目是否是情景喜剧的谓词函数:

```
function isSitcom(show){
  return show.type === 'sitcom';
}
```

我们现在可以将谓词函数传递给`every`和`some`数组方法，以检测是否所有的节目都是情景喜剧，以及列表中是否至少有一个情景喜剧:

你觉得现在好看了吗？

# 发现

以下是搜索列表中特定电视节目的循环示例:

我们从一个设置为`null`的`sitcom`变量开始，遍历列表。当我们找到具有特定名称的节目时，我们设置变量来引用那个[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)并停止循环。

我们能简化这段代码吗？

`find`方法从[数组](https://medium.com/programming-essentials/how-to-add-conditional-elements-to-array-and-object-literals-d708124b4c6b)中返回满足谓词函数的第一个元素。如果没有找到元素，则返回`[undefined](https://medium.com/programming-essentials/essential-things-on-undefined-and-null-178e2baa21b8)`。

下面是我们的谓词函数，它检查我们要查找的节目的名称:

```
function isFriendsSitcom(show){
  return show.name === 'Friends';
}
```

现在我们可以将谓词函数传递给`find`数组方法:

```
let sitcom = shows.find(isFriendsSitcom);
```

使用 arrow 函数可以实现类似的谓词函数。这是它的样子:

```
const sitcom = shows.find(show => show.name === 'Friends');
```

你觉得现在好看了吗？

# 结论

这些是你可以用来重构`for`循环的一些最有用的数组方法。在我的上一篇文章中，你可以找到更多关于如何将你的代码重构为函数式风格的想法。

感谢阅读。