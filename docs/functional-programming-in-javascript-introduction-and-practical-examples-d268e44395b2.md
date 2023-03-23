# JavaScript 中的函数式编程:简介和实例

> 原文：<https://betterprogramming.pub/functional-programming-in-javascript-introduction-and-practical-examples-d268e44395b2>

## 从纯函数和组合子到组合和容器

![](img/acb458b01d7b861ccac358b4058710df.png)

函数式编程借鉴了数学中的一些思想。在 [Unsplash](https://unsplash.com/s/photos/equation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[罗马法师](https://unsplash.com/@roman_lazygeek?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片。

函数式编程(FP)是一种越来越流行的编码风格。有很多内容解释了什么是函数式编程，但是关于如何应用它的内容却很少。对我来说，知道如何应用它更有价值:只有当你把它付诸实践时，你才能真正理解和感受一种编程风格。这就是这篇文章的目的 JavaScript 中函数式编程风格的实用介绍。

与我遇到的一些作品不同，这个作品不会鼓励你使用高阶函数，如`map`、`filter`和`reduce`，并就此打住。是的，这些函数是函数式程序员工具箱中有用的一部分，但它们只是整体情况的一部分——许多代码库在使用它们时没有遵循其他函数式编程原则。相反，我们将使用普通的 JavaScript 来构建函数，帮助我们尽可能地坚持函数范式。但是首先，我们需要理解两个关键的功能概念。

注意:ES6 的 JavaScript 特性，比如箭头函数和 spread 操作符，使得编写 FP 代码变得更加容易，所以建议您在一个 ES6 友好的环境中学习！

![](img/01718234c2e658c6caf4086d0c631f7c.png)

[Erda Estremera](https://unsplash.com/@erdaest?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/pure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 概念 1。纯函数

这些是函数式编程的核心。纯函数有三个属性:

1.同样的论点必然总是导致同样的结果。

```
*/** This function is* ***pure****:
*   if the input is the same, the result will always be the same.
*/* const cubeRoot = num => Math.pow(num, 1/3);*/** This function is* ***impure****:
*   here, the same argument can produce different results.
*/* const randInt = (min, max) => {
  return parseInt(Math.random() * (max - min) + min);
};
```

2.一个纯函数不能依赖于在其作用域之外声明的任何变量。

```
const stock = ['pen', 'pencil', 'notepad', 'highlighter'];*/** This function is* ***impure****:
*   it refers to the stock variable in the global namespace.
*/*
const isInStock = item => {
  return stock.indexOf(item) !== -1;
};*/** This function is* ***pure****:
*   it does not depend on any variables outside its scope.
*/*
const isInStock = item => {
  const stock = ['pen', 'pencil', 'notepad', 'highlighter'];
  return stock.indexOf(item) !== -1;
};*/** This function is also* ***pure****: 
*   all variables are passed in as arguments.
*/*
const isInStock = (item, array) => {
  return array.indexOf(item) !== -1;
};
```

3.不能有副作用:这意味着不能改变外部变量，不能调用`console.log`，不能触发额外的进程。

```
let fruits = ['apple', 'orange', 'apple', 'apple', 'pear'];*/** This function is* ***pure****:
*   it does not change the fruits variable.
*/*
const countApples = fruits => fruits.filter(word => word === 'apple').length;*/** This function is* ***impure****:
*   it 'destructively' changes the fruits variable (a* ***side-effect****).
*/*
const countApples = () => {
  fruits = fruits.filter(word => word === 'apple');
  return fruits.length;
};
```

这些特征使得纯函数类似于数学中的函数。通过尽可能频繁地使用纯函数，我们使代码更加透明、可预测和独立。这使得我们的代码更容易维护和调试。它还鼓励我们将大任务分成更简单、更易管理的步骤。

# 概念 2。组合子

组合子类似于纯函数，但它们更受限制。组合子具有与纯函数相同的要求，外加一个要求:

*   组合子不包含自由变量。

自由变量是其值不能被独立访问的任何变量。组合子中的每个变量都必须通过参数传递。

所以下面这个函数是纯函数，但不是组合子。它依赖于一个`conversionRates`变量，我们不能独立地访问它:它不是函数的参数。

```
const convertUSD = (val, code) => {
  const conversionRates = {
    CNY: 7.07347,
    EUR: 0.906250,
    GBP: 0.796313,
    INR: 71.1427,
    USD: 1,
  }; if (!conversionRates[code]) {
    throw new Error('This currency code is not available');
  }; return val * conversionRates[code];
};
```

要使`convertUSD`成为一个组合子，我们需要将转换率数据作为一个参数传入。

相比之下，下面的函数是组合子:

```
const add = (x, y) => x + y;
const multiple = (x, y) => x + y;const sum = (...nums) => nums.reduce((x, y) => x + y);
const product = (...nums) => nums.reduce((x, y) => x * y);
```

应该清楚的是`add`和`multiply`不包含自由变量，但是`sum`和`product`呢？当然，他们引入了两个新变量`x`和`y`，它们不是参数？但是在这种情况下，`x`和`y`的值直接由传递给每个函数的参数决定。`x`和`y`不是表示新变量，而是现有变量的别名，所以我们也可以将`sum`和`product`定义为组合子。

注意:通常在函数式编程中，组合子接受并返回函数。所以在实践中，你不会经常看到像上面描述的组合子那样的函数，尽管它们遵循所有的规则！对于经典的 FP 组合器，请参见下面“介绍合成函数”一节中的`compose`函数。

# 为什么函数式程序员会回避循环？

网上有很多内容告诉我们函数式程序员避免使用`for`或`while`循环，但是很少解释为什么。这里有一个例子。下面是一个用于创建从`1`到`100`的值数组的函数:

```
const list1to100 = () => {
  const arr = [];
  for (let i = 0; i < 100; i++) {
    arr.push(i + 1);
  };
  return arr;
};
```

使用这样的`for`循环，我们可能需要两个自由变量(`arr`和`i`)。这防止了`for`循环成为一个组合子。从技术上讲，它是一个纯函数，因为它不会在局部范围之外改变任何变量。但是，如果可能的话，我们宁愿完全避免突变。

这里有一个更适合函数式编程范例的替代方案:

```
const list1to100 = () => {
  return new Array(100).fill(undefined).map((x, i) => i + 1);
};
```

现在我们不再定义任何新的变量(在这种情况下，`i`指的是数组中项的索引，这个值是在数组创建时存储在内存中的)。我们也没有改变任何变量。这个函数更有 FP 的味道！

# 纯函数和组合子为什么有用？

如果你是函数式编程的新手，你可能会认为我们给自己强加了很多不必要的限制。您甚至可能想知道是否有可能以这种方式编写整个应用程序，并且仍然遵循规则！

核心思想是函数式编程更容易写，更容易理解。我们可以在任何上下文中使用纯函数:它们将总是返回相同的结果，并且它们不会改变代码的任何其他部分。组合子更加透明:组合子中的每个变量都是你选择传递的。

至于编写真实世界的应用程序，函数式编程确实只能带我们走这么远。我们需要通过将事情记录到控制台来调试应用程序。我们还需要变异变量(例如，控制状态)，触发外部流程(例如，对数据库的 CRUD 操作)，以及处理未知数据(例如，用户输入)。满足这些需求的 FP 方法是将非 FP 代码隔离到容器中，并在这些代码和我们整洁的、可重用的 FP 代码之间提供桥梁。通过将可变代码保存在容器中，我们限制了它变得混乱和影响我们不希望它影响的事情的能力！

在本文的剩余部分，我们将研究一些实用的方法:

*   写 FP 代码
*   解决上面确定的一些问题

我们将从构建一个实用程序来使函数式编程更加自然开始:`compose`。

![](img/1a7c891c1de24297892b699829ec692e.png)

比尔·牛津在 [Unsplash](https://unsplash.com/s/photos/cogs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 介绍撰写功能

函数式编程中一个非常常见的任务是将多个函数组合成一个函数。按照惯例，我们将执行此操作的函数称为`compose`。这个函数恰好也是一个组合子！

假设我们需要一个将美分转换成美元的函数。FP 鼓励我们将这个任务拆分成更小的子任务，所以让我们从制作四个函数开始:`divideBy100`、`roundTo2dp`、`addDollarSign`和`addSeparators`。

```
const divideBy100 = num => num / 100;const roundTo2dp = num => num.toFixed(2);const addDollarSign = str => '$' + String(str);const addSeparators = str => {
  // add commas before the decimal point
  str = str.replace(/(?<!\.\d+)\B(?=(\d{3})+\b)/g, `,`);
  // add commas after the decimal point
  str = str.replace(/(?<=\.(\d{3})+)\B/g, `,`);
  return str;
};
```

上面的代码大多非常简单，除了`addSeparators`函数，它使用一些漂亮的正则表达式在每第三个数字前添加逗号。

现在我们有了各自的功能，我们需要一种方法来组合它们。传统的方法是使用括号，就像这样:

```
const centsToDollars = 
  addSeparators(
    addDollarSign(
      roundTo2dp(
        divideBy100
      )
    )
  );
```

这没关系，但是随着我们需要的函数数量的增加，跟踪括号可能会变得令人困惑。这就是`compose`的用武之地。`compose`功能将允许我们像这样组合功能:

```
const centsToDollars = compose(
  addSeparators,
  addDollarSign,
  roundTo2dp,
  divideBy100,
);
```

没有复杂的括号，这就清楚多了。那么，我们如何构建`compose`？

# 构建合成函数

使用高阶`reduceRight`功能，这可以在一行中实现:

```
const compose = (...fns) => x => fns.reduceRight((res, fn) => fn(res), x);
```

那么，上面的代码是怎么回事呢？

*   首先，我们使用扩展操作符`...`传递任意数量的函数作为参数。
*   接下来，我们想把我们的函数数组(`fns`)变成一个输出。我们可以使用 JavaScript 的`reduce`函数，但是按照惯例`compose`是从右向左运行的，所以我们将使用`reduceRight`。
*   `reduceRight`将回调函数作为第一个参数。在这个回调中，我们将传递两个参数:我们的结果(`res`)，其中我们跟踪最新返回的结果，以及一个函数(`fn`)，我们将使用它来触发`fns`数组中的每个函数。
*   最后，`reduceRight`有一个可选的第二个参数，它定义了它的初始值。这种情况下应该是`x`。

注意:如果你喜欢从左到右排列你的功能，你可以用`reduce`代替`reduceRight`。按照惯例，`compose`的这种从左到右的变化将被称为`pipe`或`sequence`。

现在，下面的代码应该返回一个函数:

```
const centsToDollars = compose(
  addSeparators,
  addDollarSign,
  roundTo2dp,
  divideBy100,
);
```

如果我们运行`console.log(typeof centsToDollars)`，应该会看到`"function"`。

现在让我们试一试。如果我们执行`console.log(centsToDollars(100000000))`，我们应该得到一个`$1,000,000.00`的结果。完美！

我们刚刚编写了第一个真实世界的函数代码示例！不再想添加美元符号？只需从`compose`中删除`addDollarSign`作为参数。你的初始值是美元，而不是美分？然后取下`divideBy100`。因为我们遵循 FP 原则，我们可以确信移除这些函数不会影响我们代码的任何其他部分。

我们也可以很容易地在我们代码库的任何部分重用这些小函数。例如，我们可能想在应用程序中使用`addSeparators`来格式化其他数字。函数式编程意味着我们可以放心地重用那个函数！

# 调试撰写

但是我们有一个新问题。假设我们在应用程序中使用方便的`addSeparators`函数来格式化 20 个不同的数字，但是有些东西不工作。通常，我们可能会在函数中添加一个`console.log`语句来查看发生了什么:

```
const addSeparators = str => {
  str = str.replace(/(?<!\.\d+)\B(?=(\d{3})+\b)/g, `,`);
  str = str.replace(/(?<=\.(\d{3})+)\B/g, `,`);
  console.log(str);
  return str;
};
```

但是这并没有太大的帮助，因为每次我们的应用程序加载时该函数触发 20 次，所以我们会看到 20 个`console.log`的实例！

我们需要一种方法来查看只有当`addSeparators`作为`centsToDollars`的一部分被调用时会发生什么。为此，我们可以使用一个名为`tap`的组合子。

# 抽头函数

`tap`函数使用提供的对象运行函数，然后返回该对象:

```
const tap = f => x => {
  f(x);
  return x;
};
```

这允许我们在传递给`compose`的各种函数之间运行额外的函数，而不会影响结果:这使得`tap`成为登录控制台的理想位置。

# 跟踪功能

我们将调用我们的日志记录函数`trace`，我们将调用`console.log`作为回调函数:

```
const trace = label => tap(console.log.bind(console, label + ':'));
```

注意，我们必须使用`bind`来确保在执行`tap`时全局`console`对象是可用的。下一个参数`label`，允许我们在记录到控制台的任何内容之前添加一个字符串，这可以使调试更加清晰。

回到`compose`，我们可以添加`trace`函数，在对象从一个对象传递到另一个对象时跟踪对象:

```
const centsToDollars = compose(
  trace('addSeparators'),
  addSeparators,
  trace('addDollarSign'),
  addDollarSign,
  trace('roundTo2dp'),
  roundTo2dp,
  trace('divideBy100'),
  divideBy100,
  trace('argument'),
);
```

现在，如果我们运行`centsToDollars(100000000)`，我们将在控制台中看到:

```
argument: 100000000
divideBy100: 1000000
roundTo2dp: 1000000.00
addDollarSign: $1000000.00
addSeparators: $1,000,000.00
```

如果在任何阶段出现问题，现在都更容易发现！

要查看我们到目前为止创建的所有函数，包括`centsToDollars`示例，[请查看这个要点](https://gist.github.com/BretCameron/0630bbdc332ed128de6c702efea47ccc)。

![](img/3cb844a7153cc4c94e81e07e536d3af8.png)

由 [sergio souza](https://unsplash.com/@serjosoza?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/shipping-containers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 容器

在本文的最后一部分，我们将快速了解一下容器。我们无法完全避免混乱的、有状态的代码，所以函数式编程的解决方案是将这些代码与我们代码库的其他部分隔离开来。这就把所有易变的、副作用大的、不纯的代码放在了一个地方，保持了整洁。我们的纯逻辑可以使用*桥*与这些代码进行交互——我们创建这些方法来触发副作用，并以可控、可预测的方式改变变量。

首先，让我们创建几个实用函数，帮助我们检查函数是否作为参数传递:

```
const isFunction = fn => fn && Object.prototype.toString.call(fn) === '[object Function]';const isAsync = fn => fn && Object.prototype.toString.call(fn) === '[object AsyncFunction]';const isPromise = p => p && Object.prototype.toString.call(p) === '[object Promise]';
```

我们将使用 ES6 类语法来创建我们的容器，但是您也可以使用常规函数来实现相同的目的:

```
class Container {
  constructor(fn) {
    *this*.value = fn;
    if (!isFunction(*this*.value) && !isAsync(*this*.value)) {
      throw new TypeError(`Container expects a function, not a ${typeof *this*.value}.`);
    };
  } run() {
    return *this*.value();
  }
};
```

我们的`constructor`接受一个函数或者异步函数。如果两者都没有提供，它将抛出一个`TypeError`。然后`run`方法执行这个函数。

我们可以在容器中存储不纯的函数，这些函数不会运行，除非它们被特别调用，如下所示:

```
const sayHello = () => 'Hello';const container = new Container(sayHello);
console.log(container.run());  // 'Hello'
```

当然，我们的`sayHello`函数实际上不是不纯的。但也有可能！

为了让容器更有用，最好对容器的`run`方法的结果执行额外的函数。为此，让我们将`map`作为方法添加到我们的`Container`类中:

```
map(fn) {
  if (!isFunction(fn) && !isAsync(fn)) {
    throw new TypeError(`The map method expects a function, not a ${typeof fn}.`);
  }; return new Container(
    () => isPromise(*this*.value()) ?
      *this*.value().then(fn) : fn(*this*.value())
  )
}
```

这需要一个新函数作为它的参数。如果原始函数(`this.value()`)的结果是一个承诺，它使用`then`方法链接新函数。否则，它只执行`this.value()`上的功能。

现在我们可以将函数链接到用于创建容器的函数上。在下面的例子中，我们向序列中添加了一个新函数`addName`，并使用前面的`tap`函数将结果记录到控制台。

```
const sayHello = () => 'Hello';
const addName = (name, str) => str + ' ' + name;const container = new Container(sayHello);const greet = container
  .map(addName.bind(*this*, 'Joe Bloggs'))
  .map(tap(console.log));
```

当我们执行`greet.run()`时，我们应该在控制台中看到`Hello Joe Bloggs`。

要查看本节的完整代码，请查看要点。

容器的内容远不止本文所能涵盖的。例如，流行的工具 Redux，其核心是一个状态管理容器。但是希望这个例子足以向您展示什么是容器，以及它为什么有用。

# 结论

我希望这篇文章为您提供了一些将函数式编程引入 JavaScript 代码的实用方法。大多数函数式编程都很简单:尽可能多地编写纯函数。对于感兴趣的人，您可以更深入地了解:

*   如果你愿意投资付费课程，我强烈推荐迈克尔·罗萨塔的课程《使用函数式 JavaScript 构建声明式应用程序》(可在 [LinkedInLearning](https://www.linkedin.com/learning/building-declarative-apps-using-functional-javascript) 或 [Udemy](https://www.udemy.com/course/building-declarative-apps-using-functional-javascript/) 上获得)。当我刚开始学习函数式编程时，我使用了 Michael 的课程，并且在我关于容器的最后一节中借鉴了他的一些想法。
*   另一个改进函数式编程的好方法是查看库 [RamdaJS](https://ramdajs.com/) 。它是专门为函数式编程风格设计的，实现了我们之前构建的几个函数，即`compose`、`pipe`和`tap`。【Ramda 官方网站有一些好文章的链接让你入门。Christopher Okhravi 的视频也是一个很好的介绍，它通过给出更多关于如何使用`compose` / `pipe`的例子来补充这篇文章。

如果你有任何问题，或者你想读一篇更深入挖掘特定 FP 概念的文章，请告诉我！