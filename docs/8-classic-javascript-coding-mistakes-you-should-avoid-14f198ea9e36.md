# 你应该避免的 8 个经典 JavaScript 错误

> 原文：<https://betterprogramming.pub/8-classic-javascript-coding-mistakes-you-should-avoid-14f198ea9e36>

## 混淆相等运算符、缺少参数、忽略范围等等

![](img/a4c7b5c3a62ad73bc61cefc696df4e9f.png)

照片由 [Muhd Asyraaf](https://unsplash.com/@8thshot?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是当今 web 开发中最流行的语言之一。它很容易开始，有一个友好的语法，并拥有一些强大的功能。但是掌握它需要时间和大量的练习。

虽然初学者容易犯一些经典错误，但有编程经验的开发人员也不能完全避免错误。任何人都可能因为忽略 JavaScript 的细微差别而犯错误。这可能会在您的代码中导致意外的结果。让我们探讨一些 JavaScript 开发人员会犯的众所周知的错误，并了解如何避免它们。

# 误用加法运算符合并数组

与所有编程语言一样，JavaScript 提供了一个用于整数和浮点数相加的`+`运算符。

但是一旦您使用运算符添加不同的类型，如字符串和数字，类型转换就会发生。这些值被连接起来而不是相加，如下所示:

```
var x = 1 + 1 //2
var y = 1 + '1' // '11'
```

虽然这并不重要，但是使用加法运算符合并数组会导致更多意想不到的结果:

```
a = [1,2]
b = [3,4,5]console.log(a + b); // [1,23,4,5,6]
```

加法运算符并不真正合并数组。相反，它将`a`的最后一个元素与`b`的第一个元素连接起来。为了正确合并 JavaScript 数组，使用`concat()`方法或`spread`操作符。

# 不正确地使用等式运算符

JavaScript 提供了用于相等检查的`==`和`===`操作符，您可以很容易地在代码中互换这两个操作符，从而导致灾难性的结果。这里有一个例子可以说明它们之间的区别:

```
false === 0 //false
false == 0 //true10 === '10' //false
10 == '10' //true
```

triple equals 是一个严格的等式运算符，它在比较中检查数据类型和值。另一方面，double equals 会在完成任何必要的类型转换后进行相等检查，这样两个操作数就有了共同的类型。

由`==`操作符完成的类型强制要求您熟悉 falsy 值，以防止意外的结果。例如，`NaN`不能转换成普通的 falsy 值类型:

```
null == undefined //truenull == NaN //false
```

建议您尽可能使用`===`。另外，需要注意的是`switch`块只做严格的比较。

# 忽略“变量”和范围

初学者在开始时，往往会忘记在声明中使用`var`，JavaScript 也没有抱怨。相反，它假设变量具有全局范围。这可能对您的代码库有害，并导致错误。下面的例子说明了这样一种差异:

```
var a = 1;function simple() {
a = 5;
}simple()
console.log(a); //5
```

此外，避免`var`可能会导致参考误差，如下所示:

```
console.log(x); //ReferenceError
x = 1;console.log(x); //undefined
var x = 1;
```

与大多数编程语言不同，JavaScript 没有为`var`提供块级范围。例如，在下面的代码中，`i`的值将存在于循环之外:

```
for (var i = 0; i < 5; i++) {
  /* ... */
}
console.log(i);  // 5
```

为了确保值`i`保持在那个块内，我们需要使用`let`来代替。不同之处在于:`var`是函数作用域，而`let`是块作用域。

# 函数调用中缺少参数

JavaScript 函数不检查收到的参数数量，这可能会产生错误。在向函数声明中添加新参数，但在调用时不传递参数的情况下，这些参数将被视为`undefined`。

解决这个问题的一个方法是使用`||`来确保参数在主体中有一个默认值。第二种方式实际上更受欢迎，从 ES6+开始就有了。它要求您设置默认参数，如下所示:

```
function sum(num1 = 0, num2 = 0){
console.log(num2 + num1);
}sum(2,3); //prints 5
sum(); //prints 0
```

# 处理``this`'参考

`this`关键字让每个 JavaScript 开发人员感到困惑。也许，它与 Java 等其他编程语言提供的功能有很大不同。

`this`关键字引用一个对象，引用可以根据函数的调用方式而改变，而不是在哪里定义*。*

对于对象内部的方法，`this`指的是调用者对象本身，而对于独立的函数，`this`指的是一个全局对象。

```
let user = {
  name: "your name",
  getName() {
    console.log(this.name);
  }
};user.getName();
```

现在，如果我们提取函数，`this`引用将会改变:

```
let user = {
  name: "your name",
  getName: function(){
    console.log(this.name);
  }
};user.getName() //your name
var getUsername = user.getName;getUsername() //undefined
```

尽管`getUsername`持有`getName`的引用，但是调用位置已经改变，从而使`this`成为一个全局对象。因此`getUsername()` 返回未定义。

`this`关键字的另一个更棘手的情况是在匿名函数中使用。匿名函数中的`this`上下文不能访问外部对象，因此指向全局范围。要访问匿名函数中的对象属性，我们需要传递对象的实例，如下所示:

```
let user = {
  name: "your name",
  getName: function(){
    var self = this;
    (function () {
        console.log(self.name);
    }());

  }
};
```

不像上面那样将`this`引用存储在变量中，我们也可以在匿名函数上调用`call(this)`，如下所示:

```
let user = {
  name: "your name",
  getName: function(){

    (function () {
        console.log(this.name);
    }).**call(this)**

  }
};
```

# 过度沉迷于嵌套回调

异步编程是 JavaScript 的一个重要部分，它很容易陷入嵌套回调，也称为*回调地狱。这里有一个典型的例子:*

![](img/d2c658105b36c88b5477d38b79831e6b.png)

一旦嵌套回调超出了几层深度，我们就会以可怕的末日金字塔告终。你可以通过承诺来避免这种情况，如下所示:

```
getUser()
    .then(user => getLocation(user))
    .then(location => getPosts(location))
    .then(posts => doStuff(posts));
```

# 创造深层次的承诺

就像回调地狱一样，你很容易以*承诺地狱*结束——深度嵌套的承诺。出现这种情况是因为对不同的承诺类型缺乏了解。例如，我们可以有三个不需要嵌套的独立承诺，如下所示:

```
enterHome()
  .then(() => {
    return greet('family')
      .then(() => {
        return goSleep();
      });
  });
```

我们可以使用如下所示的`Promise.all`函数并行运行它们，而不是顺序运行它们:

```
Promise.all([
  enterHome(),
  greet('family'),
  goSleep()
]);
```

# 使用“for-in”循环的数组迭代

JavaScript 提供了几种迭代数组的方法。`ForEach`、经典`for,`和`for` - `of`循环是一些推荐的方式。人们可能会不经意地使用`for` … `in`循环，因为它们看起来很舒服——但这通常不是一个好主意，因为它们枚举了一个对象的属性名。

在数组的情况下，这意味着，`for` - `in`循环本质上是在索引上迭代——有时，以任意顺序，这违背了数组迭代的目的。

继承的方法和原型也在`for` - `in`循环中枚举，最终导致性能变慢。这意味着如果你扩展`Array.prototype`来包含一个方法，它也会被枚举。

```
Array.prototype.hello = "hey!";
var array = ['a', 'b', 'c'];

for (var i in array) {
    console.log(array[i]);
}
```

# 结论

上述众所周知的错误是由于 JavaScript 与其他语言相比在工作方式上的细微差异造成的。更好地理解该语言将有助于避免这些经典错误，并确保您的代码库保持稳固。

这一次到此为止。感谢阅读。