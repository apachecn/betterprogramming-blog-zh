# 对象键、值和条目:JavaScript ES6 特性系列(第 9 部分)

> 原文：<https://betterprogramming.pub/object-keys-values-and-entries-javascript-es6-feature-series-part-9-d71268791089>

## 最后，ECMAScript 引入了一些非常需要的对象操作方法

![](img/3ad54501e0aa0091be0e8272ff72661b.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/writing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 介绍

这些作品的灵感很简单:事实上，仍然有很多开发人员对 JavaScript 感到困惑——或者，至少，并不总是很有意义。

但是根据维基百科[的数据，在 1000 万个最受欢迎的网页中，JavaScript 仍然占据了将近 95%。](https://en.wikipedia.org/wiki/JavaScript)

因为随着时间的推移，它的使用量只会继续增加，所以我想提供一些我经常使用的 ES6+特性的文章和例子，供其他开发人员参考。

我们的目标是让这些文章简短、深入地解释对该语言的各种改进，我希望它们能启发您使用 JS 编写一些真正酷的东西。谁知道呢，在这个过程中你可能会学到一些新东西。

这篇文章将关注 JavaScript 对象和一些方便的新方法，这些新方法涉及键、值以及这两者的组合，它们在过去几年中被标准化为语言。

# Object.keys()

这听起来可能很难相信，特别是如果你来自另一种编程语言，如 Java，但直到 ES2015，JavaScript 中还没有内置方法来轻松访问对象的属性名，也就是所谓的[键](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)。

真的吗？

令人震惊，我知道。在此之前，像 [Lodash](https://lodash.com/docs/4.17.15#keys) 这样的库可能会被用来做一些基本的、标准化的方法。

令人高兴的是，ECMAScript 委员会在 ES2015 发布时采取了行动，并引入了方法`[Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)`。该方法返回一个给定对象自己的可枚举属性名的数组，其顺序与我们使用普通的`for`循环得到的顺序相同。

对象上的`Object.keys()`示例:

```
const resistanceFighter = {
  name: "John Connor",
  age: 30,
  title: "Resistance Leader",
  fight() {
    return `${this.name} leads the resistance fight against the machines.`
  }
};console.log(Object.keys(resistanceFighter)); // [ 'name', 'age', 'title', 'fight' ]
```

`Object.keys()`的语法非常简单:您将一个对象(如`resistanceFighter`)作为参数传递给方法，方法返回该对象的属性和方法的键或名称。

在这种情况下，它返回三个属性名:`name`、`age`和`title`，加上附加到对象的方法`fight`。

元素以字符串的形式返回到一个数组中，该数组对应于直接在`object`上找到的可枚举属性。属性的排序与通过手动循环对象的属性给出的排序相同。

让我们看看其他一些例子，看看`Object.keys()`将如何与各种 JavaScript 数据结构交互。

`Object.keys()`用简单的阵列:

```
const resistanceTeam = [ "Sarah J Connor", "Terminator", "John Connor"];console.log(Object.keys(resistanceTeam)); // [ '0', '1', '2' ]
```

当对实际数组运行该方法时，返回的`keys`是数组中包含的每一项的“字符串化”索引。

`Object.keys()`带有阵列状物体:

```
const movieTitles = {
  0: "The Terminator",
  1: "Judgment Day",
  2: "Rise of the Machines",
  3: "Salvation",
  4: "Genesis",
  5: "Dark Fate"
}console.log(Object.keys(movieTitles)); // [ '0', '1', '2', '3', '4', '5' ]
```

与真正的数组相似，一个类似于以索引号作为键的数组的对象在运行`Object.keys()`方法时将返回一个数字数组作为字符串。

`Object.keys()`在具有随机键排序的类似数组的对象上:

```
const movieReleaseYears = {
  1991: "Judgment Day",
  2015: "Genisys",
  2003: "Rise of the Machines",
  1984: "The Terminator",
  2019: "Dark Fate",
  2009: "Salvation"
}console.log(Object.keys(movieReleaseYears)); // [ '1984', '1991', '2003', '2009', '2015', '2019' ]
```

如果你在一个随机排序的关键字上运行`Object.keys()`，比如说，这些关键字恰好是不同的*终结者*电影上映的年份，那么产生的关键字数组将从最小到最大排序。

但是，如果在键是非数字字符串的对象上运行同样的方法，它们将不会按排序顺序打印出来。为了对它们进行排序，您仍然需要在数组上运行`.sort()`方法。请参见下面的示例以供参考。

`Object.keys()`在带有键的对象上，不管是未排序的还是排序的:

```
const movieDirectorsUnordered = {
  director2: "Tim Miller",
  director4: "Jonathan Mostow",
  director1: "James Cameron",
  director3: "McG"
}console.log(Object.keys(movieDirectorsUnordered)) // [ 'director2', 'director4', 'director1', 'director3' ]console.log(Object.keys(movieDirectorsUnordered).sort()); // [ 'director1', 'director2', 'director3', 'director4' ]
```

在 ES2015 中，你还可以用`Object.keys()`做另一件事:你可以将字符串等非对象的东西强制转换成对象。

`Object.keys()`将字符串强制转换为对象:

```
const animal = "canine";console.log(Object.keys(animal)); // [ '0', '1', '2', '3', '4', '5' ]
```

如果您试图在 ES5 或更低版本中调用这个方法，将会抛出一个`TypeError`,仅供参考。

现在，让我们转到对象等式的另一半:与键相关的值。

# Object.values()

不管出于什么原因，方法`[Object.values()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Object/values)`直到 2017 年才在 JavaScript 语言中标准化(为什么不是更早？)

`Object.values()`方法返回一个给定对象自己的可枚举属性值的数组，其顺序与`[for...in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)`循环提供的顺序相同(不同之处在于`for-in`循环也枚举原型链中的属性)。

如果你曾经针对你的回购运行过一套相对严格的 [ESLint](https://eslint.org/) 规则，比如 [Airbnb 的](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb)，你可能会因为上面提到的原因看到一个关于使用`for...in`循环的警告。

通过原型链的循环将显示继承的属性以及属于对象本身的属性，这通常是不希望的。

对象上的`Object.values()`示例:

```
const resistanceFighter = {
  name: "John Connor",
  age: 30,
  title: "Resistance Leader",
  fight() {
    return `${this.name} leads the resistance fight against the machines.`
  }
};console.log(Object.values(resistanceFighter)); // [ 'John Connor', 30, 'Resistance Leader', [Function: fight] ]
```

使用我在上面的`Object.keys()`示例中使用的同一个`resistanceFighter`对象，当我调用`Object.values()`并将其作为参数传递时，我得到了对象中值的数组:`'John Connor'`、`30`、`'Resistance Leader'`和`fight()`函数。

与`Object.keys()`返回的字符串数组不同，`Object.values()`返回一个数组，其元素是在对象上找到的*实际*可枚举属性值。同样，属性的排序与通过手动循环对象的属性值给出的排序相同。

这里有一些`Object.keys()`的例子。

`Object.values()`用简单的数组:

```
const resistanceTeam = [ "Sarah J Connor", "Terminator", "John Connor"];console.log(Object.values(resistanceTeam)); // [ 'Sarah J Connor', 'Terminator', 'John Connor' ]
```

就像当`Object.keys()`打印出数组中每一项的索引时，调用`Object.values()`打印出数组中的实际项，就像它们当前被列出一样。

`Object.values()`在类似阵列的物体上:

```
const movieTitles = {
  0: "The Terminator",
  1: "Judgment Day",
  2: "Rise of the Machines",
  3: "Salvation",
  4: "Genisys",
  5: "Dark Fate"
}console.log(Object.values(movieTitles));/* [ 'The Terminator',
  'Judgment Day',
  'Rise of the Machines',
  'Salvation',
  'Genisys',
  'Dark Fate' ] */
```

当在以数字为键的类似数组的对象上调用`Object.values()`时，得到的数组将按键的顺序打印出来。下面的例子可以证明这一点。

`Object.values()`在具有随机键排序的类似数组的对象上:

```
const movieReleaseYears = {
  1991: "Judgment Day",
  2015: "Genisys",
  2003: "Rise of the Machines",
  1984: "The Terminator",
  2019: "Dark Fate",
  2009: "Salvation"
}console.log(Object.values(movieReleaseYears));/* [ 'The Terminator',
  'Judgment Day',
  'Rise of the Machines',
  'Salvation',
  'Genisys',
  'Dark Fate' ] */
```

正如您所看到的，尽管`movieReleaseYears’`键是随机排列的，但是当从`Object.values()`中打印出结果数组时，这些值是按照键值的数字顺序排列的。

当对象的键是非数字值时，这种排序就不成立。

在这种情况下，与`Object.keys()`的例子一样，您必须用`.sort()`手工对结果数组进行排序。该数组将根据其中的值进行排序，而不是这些值曾经附加到的键。

`Object.values()`对一个对象的值，不管是未排序的还是排序的:

```
const movieDirectorsUnordered = {
  director2: "Tim Miller",
  director4: "Jonathan Mostow",
  director1: "James Cameron",
  director3: "McG"
}console.log(Object.values(movieDirectorsUnordered)); // [ 'Tim Miller', 'Jonathan Mostow', 'James Cameron', 'McG' ]console.log(Object.values(movieDirectorsUnordered).sort()); // [ 'James Cameron', 'Jonathan Mostow', 'McG', 'Tim Miller' ]
```

就像`Object.keys()`，`Object.values()`有能力将字符串这样的非对象原语强制转换成对象。

请看，`Object.values()`将一个字符串强制转换为一个对象:

```
const animal = "canine";console.log(Object.values(animal)); // [ 'c', 'a', 'n', 'i', 'n', 'e' ]
```

好了，是时候进入这篇文章的第三部分了:由`Object.entries()`提供的对象键和值的组合。

# **Object.entries()**

ES2017 中也引入了`[Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)`方法，该方法返回给定对象自己的可枚举字符串键属性`[key, value]`对的数组，顺序与`[for...in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)`循环提供的顺序相同。

正如我在前面的`Object.values()`一节中提到的，一个`for-in`循环(它可以获得与`Object.entries()`相似的结果)也会遍历原型链，这通常不是代码想要的行为。此方法提供了一种更推荐的方法来循环访问对象的键和值。

由`Object.entries()`返回的数组的顺序不依赖于对象是如何定义的。如果需要某种排序，那么数组应该像`Object.entries(obj).sort((a, b) => b[0].localeCompare(a[0]));`一样先排序。

对象上的`Object.entries()`示例:

```
const resistanceFighter = {
  name: "John Connor",
  age: 30,
  title: "Resistance Leader",
  fight() {
    return `${this.name} leads the resistance fight against the machines.`
  }
};console.log(Object.entries(resistanceFighter));/* [ [ 'name', 'John Connor' ],
   [ 'age', 30 ],
   [ 'title', 'Resistance Leader' ],
   [ 'fight', [Function: fight] ] ] */for(let [key, value] of Object.entries(resistanceFighter)){
  console.log(`${key}: ${value}`);
}/* name: John Connor
  age: 30
  title: Resistance Leader
  fight: function fight() {
    return this.name + " leads the resistance fight against the machines.";
} */Object.entries(resistanceFighter).forEach(([key, value]) => console.log(`${key}: ${value}`))/* name: John Connor
  age: 30
  title: Resistance Leader
  fight: function fight() {
    return this.name + " leads the resistance fight against the machines.";
} */
```

在上面的例子中，在`resistanceFighter`对象上调用`Object.entries()`会产生一个嵌套数组的数组，每个内部数组对应于对象的一个键值对。

示例的第二和第三部分是一种使用`for...of`循环或`Array.forEach()`方法优雅地遍历对象值的方法，当我需要调试或转换一个对象以备将来使用时，我发现这两种方法特别有用。

再来看看`Object.entries()`的其他一些用途。

`Object.entries()`用一个简单的数组:

```
const resistanceTeam = [ "Sarah J Connor", "Terminator", "John Connor"];console.log(Object.entries(resistanceTeam));/* [ [ '0', 'Sarah J Connor' ],
   [ '1', 'Terminator' ],
   [ '2', 'John Connor' ] ] */
```

在一个简单的数组上调用`Object.entries()`方法将打印出数组中的所有值，并将它们对应的索引值作为它们的键——非常简单，类似于在一个数组上调用`Object.keys()`。

`Object.entries()`在类似阵列的物体上:

```
const movieTitles = {
  0: "The Terminator",
  1: "Judgment Day",
  2: "Rise of the Machines",
  3: "Salvation",
  4: "Genisys",
  5: "Dark Fate"
}console.log(Object.entries(movieTitles));/* [ [ '0', 'The Terminator' ],
   [ '1', 'Judgment Day' ],
   [ '2', 'Rise of the Machines' ],
   [ '3', 'Salvation' ],
   [ '4', 'Genisys' ],
   [ '5', 'Dark Fate' ] ] */
```

类似地，在以索引号作为名称的对象上调用方法，它将生成一个数组的数组，其中包含每个对象属性的键和值。

`Object.entries()`在具有随机键排序的类似数组的对象上:

```
const movieReleaseYears = {
  1991: "Judgment Day",
  2015: "Genisys",
  2003: "Rise of the Machines",
  1984: "The Terminator",
  2019: "Dark Fate",
  2009: "Salvation"
}console.log(Object.entries(movieReleaseYears));/* [ [ '1984', 'The Terminator' ],
   [ '1991', 'Judgment Day' ],
   [ '2003', 'Rise of the Machines' ],
   [ '2009', 'Salvation' ],
   [ '2015', 'Genisys' ],
   [ '2019', 'Dark Fate' ] ] */
```

与本博客的前几篇文章一样，`movieReleaseYears`上的`Object.entries()`使用当前无序的数字键，将返回一个按索引值升序排序的数组列表。

`Object.entries()`关于对象的值，包括未排序的和排序的:

```
const movieDirectorsUnordered = {
  director2: "Tim Miller",
  director4: "Jonathan Mostow",
  director1: "James Cameron",
  director3: "McG"
}console.log(Object.entries(movieDirectorsUnordered));/* [ [ 'director2', 'Tim Miller' ],
   [ 'director4', 'Jonathan Mostow' ],
   [ 'director1', 'James Cameron' ],
   [ 'director3', 'McG' ] ] */console.log(Object.entries(movieDirectorsUnordered).sort());/* [ [ 'director1', 'James Cameron' ],
   [ 'director2', 'Tim Miller' ],
   [ 'director3', 'McG' ],
   [ 'director4', 'Jonathan Mostow' ] ] */
```

但是，如果被制作成数组的数组的对象除了容易排序的数字之外还有字符串或其他关键字，它将只打印出对象中列出的内容，除非您随后手动调用`.sort()`方法。够公平吗？

`Object.entries()`将字符串强制转换为对象:

```
const animal = "canine";console.log(Object.entries(animal));/* [ [ '0', 'c' ],
   [ '1', 'a' ],
   [ '2', 'n' ],
   [ '3', 'i' ],
   [ '4', 'n' ],
   [ '5', 'e' ] ] */
```

最后一个例子。在`Object.entries()`的帮助下，一个字符串可以变异成一个对象。

正如您在上面的代码中看到的，字符串`"canine"`变成了一个数组的数组，字符串中的每个字符变成了数组的值部分，而该字符串的索引变成了键部分。

# 结论

令人惊讶的是，在 ES2015 和 ES2017 发布之前，JavaScript 对象缺乏一些看似基本的方法来读取和操作键和值。对我们来说幸运的是，访问一个对象的键，它的值，甚至一起迭代这些键和值现在是标准的。

要跟上 ECMAScript 委员会即将推出的所有改进可能有点力不从心，这就是我开始撰写这些文章的原因。

本系列文章的目标是深入研究您可能每天都在使用(或者甚至可能没有意识到)的 ES6 语法，这样您就可以成为一名更高效的 web 开发人员。

过几周再来看看，我会写更多的 JavaScript 和 ES6，或者其他与 web 开发相关的东西。

感谢您的阅读，我希望您能在您的 JavaScript 代码中加入 ES6 的最新对象方法——我保证，这将使您的开发生活更加轻松。

如果你喜欢读这篇文章，你可能也会喜欢我在这个系列中的其他作品:

*   [类和继承:JavaScript ES6 特性系列(Pt 8)](https://itnext.io/classes-and-inheritance-javascript-es6-feature-series-part-8-4a81fa3adf0f)
*   [内置模块导入导出:JavaScript ES6 特性系列(Pt 7)](https://medium.com/better-programming/built-in-module-imports-and-exports-javascript-es6-feature-series-part-7-5f0864049e1f)
*   [增强的对象文字值速记:JavaScript ES6 特性系列(Pt 6)](https://itnext.io/enhanced-object-literal-value-shorthand-javascript-es6-feature-series-pt-6-e00dfdc24f64)
*   [字符串模板文字:JavaScript ES6 特性系列(Pt 5)](https://medium.com/better-programming/string-template-literals-javascript-es6-feature-series-pt-5-a40e55a5485b)
*   [Spread & Rest 参数:JavaScript ES6 特性系列(Pt 4)](https://itnext.io/spread-rest-parameters-javascript-es6-feature-series-pt-4-c9e9f0c0228f)
*   [默认函数参数值:JavaScript ES6 特性系列(Pt 3)](https://itnext.io/default-function-parameter-values-javascript-es6-feature-series-pt-3-bd8392a88a12)
*   [箭头功能:JavaScript ES6 特性系列(第二部分)](https://itnext.io/arrow-functions-javascript-es6-feature-series-pt-2-e8c31c823392)
*   [Var，Let & Const: JavaScript ES6 特性系列(Pt 1)](https://itnext.io/var-let-const-javascript-es6-feature-series-pt-1-fa603567809e)

# 参考资料和更多资源

*   `Object.entries()`，MDN docs:[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/entries](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)
*   `Object.keys()`、MDN docs:[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
*   `Object.values()`，MDN docs:[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ objects/Object/values](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Object/values)