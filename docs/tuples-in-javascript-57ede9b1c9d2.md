# JavaScript 中的元组

> 原文：<https://betterprogramming.pub/tuples-in-javascript-57ede9b1c9d2>

## 拥抱细微差别

![](img/7da1dd0de5363b90d112d36ee844391b.png)

图为[迪安·普](https://unsplash.com/@wezlar11?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

JavaScript 缺少像 [Python](https://www.python.org/) 、 [Elm](https://elm-lang.org/) 、 [Erlang](https://www.erlang.org/) 等语言提供的对元组的语法支持。这种语法支持的缺乏剥夺了 JS 开发人员接触不同的、更细微差别的数据容器的机会。

正因为如此，JavaScript 开发人员错过了用更有目的的数据结构来丰富代码库的机会。每当 JavaScript 程序员使用数组或对象文字作为元组的替身时，他们就错过了向其他开发人员提供他们在建模底层数据时的意图的机会。

此外，每当使用其他数据结构代替元组时，这些替代数据结构的语义值就会降低。

ES6 引入了新的结构化数据类型，如`Map`和`Set`，我认为这增强了语言。为了继续这种增强 JS 的趋势，我认为 ECMAScript 委员会应该引入对元组的语法支持。

那么为什么是元组呢？它们在其他语言中是什么样子的？

# 其他语言中的元组&它们的 JS 等于

Python 和 Elm 等语言中的基本元组如下所示:

```
color = (255, 0, 0)
```

在 JavaScript 中，我们可以将相同的数据表示为:

```
const color = [255, 0 , 0]
```

像 Python 这样的语言也支持命名元组:

```
color = (red=255, green=0, blue=0)
```

我们可以在 JS 中用对象文字达到同样的结果。

```
const color = {red: 255, green: 0, blue: 0}
```

# 容器语义

作为一名开发人员，当您看到一个数组时，您应该考虑一个同质数据类型的容器。这就是在像 [Haskell](https://www.haskell.org/) 这样的强类型编程环境中链表的处理方式。

这里是 JS 中的一个数组；正确用于模型名称数据:

```
const names = ['John', 'Peter', 'Glen', 'Chris']
```

但是因为 JavaScript 缺乏元组支持和数据类型强制，我们不能假设数组也不用于建模异构数据。

我所说的异构数据是指不同的数据类型(例如字符串、数字)、不同的数据(名字、城市、餐馆)，或者两者都有。

例如让我们看看`record`:

```
const record = ['William', 24, 'Washington, D.C.']
```

大多数 JavaScript 开发人员更喜欢用对象文字来表示像这样的异构数据。这比数组更可取，因为它具有标记数据的额外语义价值。

```
const record = {
  name: 'William',
  age: 24,
  hometown: 'Washington, D.C.'
}
```

但是，这种数据类型有一些限制。一个限制是不能保证保持秩序。还有额外的开销，因为对象中的每个值都需要一个键。

# 为什么是元组？

JavaScript 中包含语法元组支持的情况与 ES6 添加了用于声明变量的关键字`const`和`let`有一定关系。

这为开发人员提供了更有意义的语义。它为开发人员提供了对底层数据和原始程序员意图的快速洞察，一切一目了然。

假设你在一个有严格林挺和谨慎开发者的代码库中，当你看到`const`时，你知道标识符*不会被重新分配。*

同样，当您看到`let`时，您知道标识符*将被重新分配。*

类似地，当您看到一个元组与一个数组时，您知道您正在处理一个数据实体，而不是多个数据。

# 元组是不必要的

希望已经展示了使用 JS 来满足您的数据需求的方法。JavaScript 中的元组支持*不必要*。但是我认为对元组的支持将丰富 JavaScript 开发者的编程体验。

元组的语法糖是不必要的，就像字符串的语法糖是不必要的一样。毕竟，JS 中的字符串只是一个字符数组。

```
const string = ['i',' ','a','m',' ','a',' ','s','t','r','i','n','g']
```

语法糖是使数据结构可访问的东西。上面的代码片段变成了:

```
const string = 'I am a string'
```

类似地，删除元组构造函数的语法开销，如:

```
const color = new Tuple(255, 0, 0)
```

取而代之的是:

```
const color = (255, 0, 0)
```

使不熟悉元组的程序员更容易访问数据容器。

我将引用莱纳斯·托沃兹的话来结束我的演讲:

> “事实上，我非常支持围绕数据设计代码，而不是反过来……糟糕的程序员担心代码。优秀的程序员会担心数据结构和它们之间的关系。”
> —莱纳斯·托瓦尔兹

# 结论

Syntactic sugar 将为 JavaScript 程序员提供一种在日常编程中非常有用的新型数据结构。

这将让他们以一种新的、更微妙的方式思考他们的数据。