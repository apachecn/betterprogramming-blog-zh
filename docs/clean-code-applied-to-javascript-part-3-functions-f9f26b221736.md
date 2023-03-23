# 应用于 JavaScript 的干净代码—第 3 部分:函数

> 原文：<https://betterprogramming.pub/clean-code-applied-to-javascript-part-3-functions-f9f26b221736>

## 关于编写和维护干净代码的惊人好处的系列文章

![](img/1526a269007ab3e97d292ef16b15e6df.png)

由 [You X Ventures](https://unsplash.com/@youxventures?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

本文是一系列文章中的第三篇，将深入探讨众所周知的*干净代码*的话题，但将把它应用于 JavaScript。

在本系列中，我们将讨论每个程序员都应该知道的关于干净代码的经典技巧，并将它们应用于特定的 JavaScript/TypeScript 语言。

*   [第 1 部分:开始之前](https://medium.com/better-programming/clean-code-applied-to-javascript-part-1-9f3badd5715)
*   [第二部分:变量](https://medium.com/better-programming/clean-code-applied-to-javascript-part-ii-variables-8302f01e539c)
*   [第 3 部分:功能](https://medium.com/@ccaballero/clean-code-applied-to-javascript-part-3-functions-f9f26b221736)
*   [第 4 部分:注释](https://medium.com/better-programming/clean-code-applied-to-javascript-part-4-3f6db21ee4e5)
*   [第 5 部分:例外情况](https://medium.com/better-programming/clean-code-applied-to-javascript-part-5-exceptions-d46e07691c19)
*   [第 6 部分:避免条件复杂性](https://medium.com/better-programming/clean-code-applied-to-javascript-part-6-avoid-conditional-complexity-5ee9cbb1b26a)

# 介绍

在本文中，我们将介绍生成干净代码的基本技巧和建议，重点关注允许我们重用代码的元素:函数。

我们所有的例子都是用 JavaScript 说明的，但是这些最佳实践可以应用于任何编程语言，包括“最接近金属”的编程语言

我做这个评论是因为我和用 C 或 Go 等语言工作的同事讨论过，他们不喜欢应用这些实践，认为在他们的编程语言中没有人这样做。

然后，我总是回答必须有人第一个打破僵局——只要是为了改善事情。然而，这个评论引起了同事之间长时间愉快的谈话，其中讨论了这些实践的优点和缺点。

因此，我们将从生成专门应用于变量的良好代码的技巧开始。

# 使用默认参数，而不是短路或条件

在大多数编程语言中，可以在函数中定义默认参数。这一事实将使我们不必在代码体中使用条件或短路。

下面几段代码演示了这个例子。

```
function setName(name) {
 const newName = name || ‘Juan Palomo’;
}function setName(name = ‘Juan Palomo’) {
 // …
}
```

# 函数参数(理想情况下，两个或更少)

这个概念对于提高代码质量至关重要:您应该减少函数参数的数量。两个或更少就足够了，但是不要被这些数字所困扰，因为这完全取决于你所使用的特定编程语言。

这个建议非常重要，因为尽管我们不相信它，但当我们有许多论点时，通常几个被组合在一起构成一个对象。我们必须使用原语(如字符串、数字、布尔等类型)进行转义。)并开始使用更高抽象层次的对象。事实上，我们会更接近业务逻辑，离底层越来越远。

在下面的第一个例子中，我们有一个接收四个参数的汉堡包的创造性函数。这些参数是固定的，按照这个顺序，这限制了我们很多。事实上，它回到了非常刚性的功能。

一个相当大的改进是用一个汉堡之类的物体来创造一个新的汉堡。通过这种方式，我们将属性分组到一个对象中(在这种情况下，它将是一个平面对象，没有原型)。

在第三个例子中，我们可以对发送的对象使用析构，我们可以让函数体访问这些属性。但是我们实际上使用的是单一参数，这给了我们更大的灵活性。

```
function newBurger(name, price, ingredients, vegan) {
 // …
}function newBurger(burger) {
 // …
}function newBurger({ name, price, ingredients, vegan }) {
 // …
} const burger = {
  name: ‘Chicken’,
  price: 1.25,
  ingredients: [‘chicken’],
  vegan: false,
};
newBurger(burger);
```

# 避免副作用—全局变量

副作用是未来麻烦的来源。从定义上来说，副作用不一定是有害的，但是当我们包含更多的副作用时，代码中出现错误的可能性会令人眩晕地增加。

因此，本节的建议是不惜一切代价避免副作用。这将帮助您生成可测试的函数。它还会帮助你应用一些技巧，比如记忆和其他我们在这篇文章中无法描述的优势。

下面的例子是一个经典的副作用，函数会修改超出其作用域的变量或对象。无法测试此函数，因为它没有要测试的参数；事实上，它所修改的变量的状态不受修改它的函数的控制或充分管理。

避免这种副作用的最简单的方法是将该函数范围内的变量作为参数传递(这是显而易见的，但当我们需要记住它时就不那么明显了)。

```
let fruits = ‘Banana Apple’;function splitFruits() {
 fruits = fruits.split(‘ ‘);
}splitFruits();console.log(fruits); // [‘Banana’, ‘Apple’];function splitFruits(fruits) {
 return fruits.split(‘ ‘);
}

const fruits = ‘Banana Apple’;
const newFruits = splitFruits(fruits);

console.log(fruits); // ‘Banana Apple’;
console.log(newFruits); // [‘Banana’, ‘Apple’];
```

# 避免副作用——对象是可变的

当通过代码的不同部分使用同一个对象修改数据时，会产生更多的副作用。

其中一个主要的副作用是对象的修改，它导致我们失去了软件开发的许多优势，而避免了这个特性。如果你在 web 世界，你会知道 JavaScript 从诞生之日起就是一个伟大的对象变异器，有很多库的目的是避免变异对象(创建新对象)。

如果前面的例子在变量超出作用域时修改函数中的变量对您来说合乎逻辑，那么函数对对象的改变应该也有同样的效果。

在 JavaScript 中，处理数组数据结构的方法分为对对象进行突变的方法和不进行突变的方法。例如，`push`、`pop`或`sort`操作处理相同的数据结构，而`filter`、`reduce`或`map`操作生成新的数据结构，并且不会改变主数据结构。

如果您希望开发团队创建干净且可维护的代码，您必须习惯于寻找协议和工作模式，以提高所有团队成员对代码和业务逻辑的理解，即使这可能会使软件的效率稍微降低，甚至更加冗长。

我将给出两个清晰的例子，其中一个对 cart 数据结构进行了突变，另一个没有进行这种突变。

```
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
}; const addItemToCart = (cart, item) => {
  return […cart, {
    item, 
    date: Date.now(),
  }];
};
```

# 函数应该做一件事

这是所有编程学校每天都会听到的编程原则之一，但在实践中，由于缺乏将理论付诸实践，它往往没有得到遵守。

每个函数必须只做一个概念性的任务。混淆概念或任务是没有意义的。自然地，一组小任务组合在一起会形成一个更大的任务，但是这些任务不应该混合在一起——这就是所谓的*耦合。*

因此，一个函数应该只做一件事。在下面的例子中，我们模拟了通过接收活动客户列表向客户发送电子邮件的功能。从概念上讲，这是一个简单的业务规则，但是在实现它时，它们是两个明显不同的任务。

```
function emailCustomers(customers) {
  customers.forEach((customer) => {
    const customerRecord = database.find(customer);
    if (customerRecord.isActive()) {
      email(client);
    }
   });
}
```

首先，我们必须过滤活跃的用户，这是一个独立于前一个功能的功能。当你在代码中写一个`if`时，你应该害怕。这并不意味着`if`是做得不好的同义词；不过，滥用`if`肯定是不好的。

一旦我们过滤了活跃的客户端，我们需要另一个函数负责向每个客户端发送电子邮件。

```
function emailActiveCustomers(customers) {
  customers
   .filter(isActiveCustomer)
   .forEach(email);
 }

function isActiveCustomer(customer) {
  const customerRecord = database.find(customer);
  return customerRecord.isActive();
}
```

记住，你应该集中精力确保每个功能只做一件事。

# 功能应该只是抽象的一个层次

当我们设计函数时，我们必须满足的另一个要求是每个函数应该只有一个抽象层次。

以下示例显示了在 JavaScript 中解析的一个可能的函数。在这个函数中，你可以看到不同的抽象层次。

```
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // …
  ];

 const statements = code.split(‘ ‘);
 const tokens = [];
 REGEXES.forEach((REGEX) => {
   statements.forEach((statement) => {
     // …
   });
 });

 const ast = [];
 tokens.forEach((token) => {
   // lex…
 });

 ast.forEach((node) => {
   // parse…
 });
} 
```
```

解决这个问题的技术非常简单——我们只需识别不同的抽象层次，并创建满足本文所述需求的函数。因此，应用重构后，我们的功能如下:

```
const REGEXES = [ // …];
function tokenize(code) { 
  const statements = code.split(‘ ‘);
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* … */ );
    });
  });
 return tokens;
}function lexer(tokens) {
 const ast = [];
 tokens.forEach((token) => ast.push( /* */ ));
 return ast;
}function parseBetterJSAlternative(code) {
 const tokens = tokenize(code);
 const ast = lexer(tokens);
 ast.forEach((node) => // parse…);
}
```

# 比起命令式编程，更喜欢函数式编程

由于这不是本文的目的，我不想参与编程范式之间的争论，您应该尝试学习函数式范式并将其用于命令式范式。

我推荐阅读 Alvin Alexander 的博客，特别是他描述函数式编程好处的文章。

下面，我总结了在命令式上使用函数式编程的主要优点。

1.纯函数更容易推理。
2。测试更容易，纯函数非常适合基于属性的测试等技术。
3。调试更容易。
4。程序更加防弹。
5。程序是在更高的层次上编写的，因此更容易理解。
6。函数签名更有意义。
7。并行/并发编程更容易。

与命令式编程相比，函数式编程的另一个特点是代码可读性更强。如果你阅读了本系列的第一篇文章，你会发现质量代码的一个特点是可读的。

因此，我们拥有与函数式编程相关的无尽优势；尽管如此，对于用一种范式学习并开始解决问题的初级程序员来说，他们很难用这种编程范式工作，因为它改变了他们的工作习惯。如果这是你的情况，也许你入错行了。

在这个行业，我们必须适应变化，最重要的是，有大量的工具，让我们可以在任何情况下使用它。

观察制作简单计数器的代码。你要记住几个变量:`total`、`i`、`items`、`items.length`、`price`；而在函数实现中，我们只有:`total`、`price`和`items`。如果你已经习惯了函数操作符，那么它的阅读速度非常快，非常友好。

```
const items = [{
   name: ‘Coffe’,
   price: 500
 }, {
   name: ‘Ham’,
   price: 1500
 }, {
   name: ‘Bread’,
   price: 150
 }, {
   name: ‘Donuts’,
   price: 1000
 }
];let total = 0;
for (let i = 0; i < items.length; i++) {
 total += items[i].price;
}const total = items
 .map(({ price }) => price)
 .reduce((total, price) => total + price); 
```

# 使用方法链接

当我们设计对对象或数据流(在本例中是一个对象)进行操作的函数时，它们通常是执行单一任务的函数。它们只有一个抽象层次，没有副作用。要执行复杂的任务，我们需要执行其中几项的组合。这开发了一个链式方法，允许更多的可读代码。这是在设计功能时已经很好地履行了以前的职责的副作用。

如果你了解 Linux，你会认为所有的命令都是为了做一件事，并把它做好，但是我们有一个复杂的操作系统和简单的功能。这要归功于使用管道来组合不同的命令。

在我们的具体案例中，我们必须构建类似的东西，无论是使用对象还是函数。在下面的例子中，我们举例说明了使用链式方法代替传统方法的`Car`类。

```
class Car {
 constructor({ make, model, color } = car) {
   /* */
 }

 setMake(make) {
   this.make = make;
 } setModel(model) {
   this.model = model;
 } setColor(color) {
   this.color = color;
 } save() {
   console.log(this.make, this.model, this.color);
 }
} const car = new Car(‘WV’,’Jetta’,’gray’);
car.setColor(‘red’);
car.save();class Car {
 constructor({ make, model, color } = car){}

 setMake(make) {
   this.make = make;
   return this;
 } setModel(model) {
   this.model = model;
   return this;
 } setColor(color) {
   this.color = color;
   return this;
 } save() {
   console.log(this.make, this.model, this.color);
   return this;
 }
}const car = new Car(‘WV’,’Jetta’,’gray’)
.setColor(‘red’)
.save(); 
```

# 结论

在整篇文章中，我们讨论了如何将干净的代码应用到开发人员的基础部分:函数。

应用干净代码的函数设计是必不可少的，因为函数是分离代码的基本元素。

然而，函数设计中的不良实践可能会导致我们保持代码的耦合性——看起来就像没有它们一样，但是引入了函数却很复杂。此外，糟糕的函数设计会导致难以发现的严重错误。随着我们在软件抽象层次上的提升，定位错误发生点将变得更加困难。

因此，本文中给出的建议将使您在代码质量方面提高一个级别，但不要在没有充分思考的情况下应用它们。请记住，没有神奇的秘诀或银弹，但有一套技术可以让你解决更广泛的问题。

最后，我们讨论的要点如下:

*   使用默认参数，而不是短路或条件
*   函数参数(理想情况下两个或更少)
*   避免副作用—全局变量
*   避免副作用——对象是可变的
*   函数应该做一件事
*   功能应该只是抽象的一个层次
*   比起命令式编程，更喜欢函数式编程