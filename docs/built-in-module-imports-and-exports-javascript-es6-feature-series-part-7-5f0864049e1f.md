# 内置模块导入和导出:JavaScript ES6 特性系列(Pt 7)

> 原文：<https://betterprogramming.pub/built-in-module-imports-and-exports-javascript-es6-feature-series-part-7-5f0864049e1f>

## 导入意味着再也不用写‘要求’

![](img/4b57065e0fdee3dc4f9ef902f11d7b60.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/learning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 介绍

这些作品背后的灵感很简单:对于很多开发人员来说，JavaScript 并没有多大意义——或者至少有时令人困惑。

根据维基百科的数据，截至 2017 年 5 月，在 1000 万个最受欢迎的网页中，JavaScript 占了不到 95%。

由于 JS 对 web 的贡献如此之大，我想提供一些我经常使用的 ES6+特性的片段和例子，供其他开发人员参考。

我们的目标是让这些文章简短、深入地解释对该语言的各种改进，我希望它们能启发您使用 JS 编写一些真正酷的东西。谁知道呢？在这个过程中，你甚至可能会学到一些新东西。

我的系列文章中的第七篇涉及 JavaScript 中的内置模块支持，这是一种更简单的方法，可以用最少的语法在代码库中的任何地方使用对象、函数、类和变量。

# 要求被 ES 模块取代

在深入研究内置模块之前，让我给你上一堂关于 CommonJS 和`require`语法的简短历史课。

在 ES2015 之前，最广泛使用的将各种库模块添加到 JavaScript 文件范围的方法是通过 [CommonJS](http://www.commonjs.org/) 。Node.js 使用它，它是今天 npm 上大多数包使用的系统。CommonJS 模块中的主要概念是一个名为`require`的函数。当您使用依赖项的模块名调用此函数时，它会确保模块已加载，并返回要在文件范围内使用的接口。

如果您曾经使用过 Node.js 脚本，或者更早的客户端 JavaScript 文件，您可能以前见过这种语法。下面是一个将广泛使用的服务器端框架 ExpressJS 导入 Node.js `app.js`文件的例子:

## 普通人需求剖析

```
const express = require('express');
```

只需声明变量`express`并调用`require('express')`，ExpressJS 的所有不同方法现在都可以用于该文件。

很长一段时间以来，CommonJS 模块工作得非常好，允许 JavaScript 社区通过 npm 大规模共享代码。

但是 CommonJS 有一些怪癖，使它不是理想的解决方案:也就是说，`require`是一个普通的函数调用，接受任何类型的参数，而不仅仅是字符串文字，这使得在不运行代码的情况下很难确定模块的依赖关系。还有比这更多的问题，但我不会在这里讨论它们。毕竟，这是一段短暂的历史。

可以说，ES2015 引入了自己的模块系统，并在这样做的过程中，将新的模块支持符号集成到语言中。依赖关系和接口的主要概念保持不变，但是细节有所不同——这就是我现在要讨论的。

如果你想更深入地了解模块、包和共性，我推荐你阅读《雄辩的 JavaScript》的这一章。

但是现在，让我们继续讨论 ES6 的内置模块支持。

# 进口所有的东西

当 es 模块出现时，与 CommonJS 的模块语法最大的不同之一是访问依赖项的特殊的新关键字`import`。

让我们看一下所有可用的不同类型的导入。在下面的部分，我还将介绍导出选项。

## 导入默认导出

第一种类型的进口围绕着默认出口。这是最常用的导入类型，有几种不同的方法可以将默认导出与其他类型的导入结合起来。

仅导入默认导出的最简单示例:

```
import carTires from './autoParts/tires.js';
```

在第一个例子中，`carTires`是从文件`tires.js`默认导出的。要在这个文件中访问它，需要做的就是用相同的名称声明导出。

如果一个文件有多个您想要访问的值(比如说，一个函数或变量，加上一个默认导出)，语法将类似于下面的默认导出和附加函数的导入示例:

```
import carTires, { shineTires } from './autoParts/tires.js';
```

在这个特定的导入语句中，被析构的导出`{ shineTires }`就是所谓的名为导入的**，因为它被括在花括号中，并从`tires.js`文件中以相同的名称导出。**

还有一个选项，默认导出可以通过通配符导入`*`与整个模块的内容结合。

在文件中导入默认导出和所有其他导出的示例:

```
import carTires, * as tireOptions from './autoParts/tires.js';
```

如果文件`tires.js`中有一个名为`tireType`的变量和一个名为`rotateTires()`的函数，导入文件可以通过调用`tireOptions.tireType`或`tireOptions.rotateTires()`来访问它们。因为额外的导出是用语法`* as tireOptions`导入的，所以文件的所有导出都可以被引用`tireOptions.xyz`。这就是所谓的**名称空间导入。**

注意:如果您在导入命名或名称空间导入的同时导入默认导出，请注意必须首先声明默认导出。

但是我想得太多了。让我们从导入默认导出转移到所有其他导入类型，然后细节应该会变得更加清楚。

## 整个模块的内容:名称空间导入

正如我在上面提到的，如果一个文件有很多你想要访问的单独的导出，而不需要通过名字全部导入，有一种方法可以在`import * as abc`的帮助下完成。

这将把`abc`插入到当前作用域中，包含来自位于`/romanAlphabet/letters.js`的文件中的模块的所有导出。

从文件一次导入多个导出的示例:

```
import * as *abc* from '/romanAlphabet/letters.js';
```

这里，访问导出意味着使用模块名(本例中为 *abc* )作为**名称空间**。例如，如果上面导入的模块包含一个导出`singMyAbcs()`，您可以这样调用它:`abc.singMyAbcs();`

很简单，对吧？现在我们继续。

## 模块的单个导出:命名导入

上面的第二个例子，在默认导出部分，提到了命名导入，这是一个简单的方法，使用 ES6 对象析构将一个对象或值引入导入文件的范围。

下面是另一个单个命名导入的示例，让它更加清晰:

```
import { apples } from './plants/fruits.js';
```

对象`apples`从文件`fruits.js`中导出，要么隐式地导出整个模块，要么使用`export`语句显式地导出，然后插入当前范围。

同样的语法也可以用于多次导入。

## 单个模块的多个导出

与单个命名导入类似，可以使用相同的代码样式从一个文件中导出多个值和对象。

多个命名导入的示例:

```
import { carrots, potatoes, onions } from './plants/veggies.js';
```

从`veggies.js`的所有导出都被导入到当前文件的范围内，并且可以通过名称访问，就像一个单独的导入值一样。

## 用更方便的名称作为导入的别名

现在假设您有一个想要导入到文件中的导出，但是它有一个非常长的名字，比如`mySuperCaliFragilisticExpialidociusObject`。打出来挺痛苦的，对吧？

好吧，有一个解决方案:在导入导出时用一个更方便的名称给它起别名。看看这个。

为方便起见，重命名命名导出的示例:

```
import { mySuperCaliFragilisticExpialidociusObject as mySuperObject } from './maryPoppins.js'
```

每当您需要访问当前作用域中的导入值时，您可以简单地用`mySuperObject`调用它，而不是原来的、长得多的导出名称。多方便啊！

## 在导入过程中重命名多个导出

但是假设您有多个长的命名导出对象。你能在导入时重命名它们吗？事实证明，你可以。

就像重命名一个导出一样，您可以在导入时重命名多个导出。

多个导出的别名示例:

```
import { ladyMaryCrawley as ladyMary, ladyEdithCrawley as ladyEdith, ladyCoraCrawley as ladyGrantham } from './downtonAbbeyFamily/ladies.js';
```

《唐顿庄园》中所有被导入到当前文件中的女士都有很长的头衔，因此，为了方便起见，当前作用域将用她们的简短别名`ladyMary`、`ladyEdith`和`ladyGrantham`来访问她们。

## 仅导入副作用

下一个例子我从未亲自使用过，但是你可以选择只导入一个完整的模块来产生副作用，而不导入任何东西。这有运行模块的全局代码的副作用，但实际上并没有导入任何值。

导入模块但不导入任何特定内容的示例:

```
import './helperFunctions.js';
```

如果有什么不同的话，这个语法让我想起了 CSS 文件导入到单独的 JavaScript 文件中的方式。

## 动态导入

好了，最后一个导入:动态导入。这一提议将在 ES2020 版本中最终确定，因此我不会将它投入生产，但它值得关注。

可以调用关键字`import`作为动态导入模块的函数。当这样使用时，它返回一个承诺。

带有 promise 语法的动态导入示例:

```
import ('./waysToTravel.js')
  .then((vehicles) => {
    // do something with planes, trains and automobiles
  });
```

它也可以与较新的 ES6 `async / await`语法一起使用。

使用 async / await 语法的动态导入示例:

```
let vehicles = await import('./waysToTravel.js');
```

我自己还没有使用过这种类型的导入，但是在内置模块支持的帮助下，他们正在用这么多不同的方法跨范围访问对象和值，这很好。

# 出口所有其他的东西

好了，我们已经讨论了 ES6 模块支持的导入方面。现在让我们看看等式的另一半:ES6 `export`关键字和语法。

## 默认导出

如果您曾经在 ReactJS 中工作过，您可能会非常熟悉默认导出。如果您简单地将`default`关键字放在您为导出定义的变量之后，它将成为默认导出。因此，当导入到另一个文件中时，您已经排除了它需要花括号。让我们来看一些例子。

从文件导出的默认函数、变量或类的示例:

```
// this is how to export a function as a default
export default function getMovies() {
  // fetch some movie data and return it
};// this is how to export a variable as a default
export default const movie = {
 title: "The Lion King", 
 releaseDate: "July 19, 2019",
 synopsis: "Simba idolizes his father, King Mufasa, and takes to heart his own royal destiny on the plains of Africa."
};// this is how to export an ES6 class (a React class to be exact)
export default class Movies extends Component {
  // render some movie data in JSX
}
```

注意:每个 JavaScript 文件只能有一个默认导出。同一个文件中的所有其他导出文件前面都会有关键字`export`。

## 个别指定出口

下一个要讨论的导出类型是单独命名的导出，我在上面的导入部分讨论这些值的导入时已经提到了。

命名导出并不复杂——正如我前面说过的，除了默认导出之外，您希望在其他 JavaScript 文件中使用的任何内容都可以是命名导出。

文件中多个命名导出的示例:

```
// this first array of pets is available to be imported into any other file because it has the 'export' keyword
export const myPets = [ "dog", "cat", "guinea pig", "gold fish"];// mySecretPets can't be directly accessed outside the scope of this file because it lacks the 'export' keyword ahead of it
const mySecretPets = [ "dragon", "griffin", "Loch Ness monster", "Big Foot"]// nameMyPets can be called in the scope of other files
export function nameMyPets() {
  console.log(myPets, mySecretPets)
}
```

从上面的例子可以看出，变量`myPets`和函数`nameMyPets`都是从文件中显式导出的，因此可以使用指定的导入语法`import { myPets, nameMyPets } from './pets.js';`导入到另一个文件中。然而，另一个变量`mySecretPets`没有从文件中导出，因此不能在它的原始范围之外显式访问。

## 单行中的多个命名导出

如果您不想在整个文件中多次键入`export`,还有另一种方法可以在 JavaScript 中从单个文件中导出多个值。

在模块的最后，使用与导入多个命名导入几乎相同的语法，您可以声明任何您想要导出到当前文件之外的其他范围的值。

具有多个命名导出的单行示例:

```
export { cakes, cookies, makeDessert, makeTea };
```

如果你可以想象在上面的例子中，变量`cakes`和`cookies`与函数`makeDessert()`和`makeTea()`一起存在，并且你想在你的代码库的其他区域访问所有这些值，简单地通过在模块的末尾导出它们，你可以在任何你想要的地方访问它们。

这是一个稍微干净的语法，但是实际上，在模块的末尾，每个值的显式导出或者所有值的单个导出将为您实现完全相同的事情。

## 使用别名导出

我将介绍的最后一个导出选项是使用别名导出，这类似于使用别名导入。

别名导出的示例:

```
export { dumplings, xiaoLongBao as soupDumplings, bbqPorkBuns, orderDimSum, pickUpSteamerBaskets, pourTea as fillTeaCups };
```

正如在导入文件中重命名命名的导出一样，您实际上可以在导出导出时重命名导出。在上面的例子中，我将变量`xiaoLongBao`重命名为`soupDumplings`，将函数`pourTea`重命名为`fillTeaCups`，以便导入任何其他范围。

老实说，我真的不知道为什么在一个文件中给一个值一个名称，然后选择用不同的名称导出到所有其他文件。(如果在另一个文件的范围内存在命名冲突，为什么不直接重命名导入呢？)但这是一种选择，我想用我所能掌握的所有知识来武装你。

这就结束了我们通过 ES6 的内置模块支持导入和导出的详细过程！

# 结论

JavaScript 的 ES6 语法已经出来很多年了(在久而久之得到了越来越广泛的采用)，但是它引入了一些突破性的语言变化，这些变化对于一些开发人员来说太陌生了，以至于他们没有兴趣立即学习。

虽然我同意它非常不同，但它也非常强大，使得编写 JavaScript 代码比几年前容易得多。

我撰写这一系列文章的目的是强调您每天使用的 ES6 语法，并解释如何使用 JavaScript 语言的这些新部分来获得最大效果。

到目前为止，npm 中所有非常有用的 JavaScript 库模块都没有标准化的内置模块支持。ES2015 改变了这一点，引入了新的专业关键词和无数种方法，可以将各种 JS 值从一个范围导入和导出到另一个范围，比以前少了很多麻烦和烦恼。这是一个很大的游戏改变。

感谢您的阅读，如果您还没有开始在自己的项目中利用内置模块支持，我希望您能开始利用它。

本系列以前的文章:

*   [增强的对象文字值速记:JavaScript ES6 特性系列(Pt 6)](https://itnext.io/enhanced-object-literal-value-shorthand-javascript-es6-feature-series-pt-6-e00dfdc24f64)
*   [字符串模板文字:JavaScript ES6 特性系列(Pt 5)](https://medium.com/better-programming/string-template-literals-javascript-es6-feature-series-pt-5-a40e55a5485b)
*   [Spread & Rest 参数:JavaScript ES6 特性系列(Pt 4)](https://itnext.io/spread-rest-parameters-javascript-es6-feature-series-pt-4-c9e9f0c0228f)
*   [默认函数参数值:JavaScript ES6 特性系列(Pt 3)](https://itnext.io/default-function-parameter-values-javascript-es6-feature-series-pt-3-bd8392a88a12)
*   [箭头功能:JavaScript ES6 特性系列(Pt 2)](https://itnext.io/arrow-functions-javascript-es6-feature-series-pt-2-e8c31c823392)
*   [Var，Let & Const: JavaScript ES6 特性系列(Pt 1)](https://itnext.io/var-let-const-javascript-es6-feature-series-pt-1-fa603567809e)

# 参考资料和更多资源

*   导入，MDN Docs:[https://developer . Mozilla . org/en-US/Docs/Web/JavaScript/Reference/Statements/import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
*   导出，MDN Docs:[https://developer . Mozilla . org/en-US/Docs/web/JavaScript/reference/statements/export](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export)
*   模块，雄辩的 JavaScript:[https://eloquentjavascript.net/10_modules.html](https://eloquentjavascript.net/10_modules.html)