# JavaScript 函数有什么特别之处？

> 原文：<https://betterprogramming.pub/whats-so-special-about-javascript-functions-791202b93a21>

## 我们看 JavaScript 函数声明、表达式和第一类函数

![](img/113e9c9fd0d9e9330d0373ad994537e5.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 JavaScript 中，有多种方法来声明函数。一种方法是用 function 关键字声明它。另一种方法是将其声明为箭头函数。

事实上，用`function`关键字声明函数有两种*方式。一种方法是使用函数声明，也称为函数语句，另一种方法是使用函数表达式。*

在 JavaScript 中，函数是一级的，这意味着它们是我们可以动态定义的对象。

# 函数声明

函数声明，也称为函数语句，是我们定义函数的方式——关键字`function`后跟函数名。

例如，我们可以编写以下代码，用函数声明来定义函数:

```
function foo(){
  return 'foo';
}
```

在执行程序之前，函数声明存储在内存中。这意味着我们可以在代码中的任何地方引用它，甚至在声明它之前。在执行程序之前在内存中存储一个变量在 JavaScript 中称为“提升”。

例如，如果我们编写以下内容:

```
console.log(foo());function foo() {
  return 'foo'
}console.log(foo());
```

我们从两个函数调用中获取`foo`日志。

# 函数表达式

函数表达式是存储为变量值的函数。

例如，要将函数定义为函数表达式，我们编写:

```
const foo = function() {
  return 'foo'
}
```

除非是函数声明或语句，否则函数表达式不会在程序运行前存储在内存中。函数是在运行时定义的，所以我们只能在声明后引用它们。

例如，如果写出如下内容:

```
console.log(foo());const foo = function() {
  return 'foo'
}console.log(foo());
```

我们会得到下面的错误:`Uncaught ReferenceError: Cannot access ‘foo’ before initialization’`。

在上面的代码中，当程序最初运行时，我们有变量声明，但是我们还没有给它分配函数，所以我们不能访问它。此外，用`let`或`const`关键字定义的任何东西在定义之前都不能与这些变量或常量一起使用，这也阻止了它的运行。

# 什么时候应该使用函数声明或表达式？

看个人风格了。函数声明更方便——它们在脚本的任何部分都可用——而函数表达式让我们可以将函数赋给变量，并以各种方式引用变量，比如它拥有的调用方法。

![](img/45d0f543b3ebdab1345eb5f4c424b081.png)

照片由 [Wexor Tmg](https://unsplash.com/@wexor?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 一流的功能

正如我们在函数表达式示例中看到的，我们可以在 JavaScript 中将函数赋给变量。这个术语叫做一级函数。一级函数是在程序运行时动态定义的函数。

这是因为在 JavaScript 中，函数和其他东西一样都是对象。函数有自己的方法和其他属性，可以像对象一样使用。

例如，所有的函数都有`toString`方法，用定义函数的代码将它们转换成一个字符串。

函数中的其他方法是调用具有不同`this`值的函数的方法，比如`bind`、`call`和`apply`。

我们可以通过编写以下代码来使用`toString`方法:

```
function foo() {
  return 'foo'
}console.log(foo.toString());
```

然后我们从`console.log`中得到以下内容:

```
function foo() {
  return 'foo'
}
```

`bind`、`call`和`apply`方法类似。它们都将函数中的关键字`this`的一个对象作为第一个参数。`bind`和`call`采用逗号分隔的参数列表作为其他参数，`apply`采用一个参数数组作为第二个参数。

例如，我们可以通过编写以下代码，用`call`方法更改函数中`this`的值:

```
let obj = {
  firstName: 'Joe',
  lastName: 'Smith'
}let person  = {
  firstName: 'Jane',
  lastName: 'Smith',
  getFullName()  {
    return `${this.firstName} ${this.lastName}`
  }
}console.log(person.getFullName());
console.log(person.getFullName.call(obj));
```

第一个例子是一样的，除了我们将`call`改为`apply`，因为我们没有传入第二个或后续的参数:

```
let obj = {
  firstName: 'Joe',
  lastName: 'Smith'
}let person  = {
  firstName: 'Jane',
  lastName: 'Smith',
  getFullName()  {
    return `${this.firstName} ${this.lastName}`
  }
}console.log(person.getFullName());
console.log(person.getFullName.apply(obj));
```

然后我们得到第一个`console.log`的`Jane Smith`，因为我们还没有用`apply`改变值，但是我们在第二个`console.log`中用`apply`方法把`this`的值改成了`obj`，这样我们就得到`Joe Smith`。

我们可以用传入的对象调用`bind`，如下面的代码所示:

```
let person = {
  firstName: 'Jane',
  lastName: 'Smith',
  getName() {
    return `${this.firstName} ${this.lastName}`
  }
}const joe = {
  firstName: 'Joe',
  lastName: 'Smith'
}console.log(person.getName.bind(joe)());
```

然后我们得到了`'Joe Smith'`,因为我们将`joe`对象传递给了`bind`方法的第一个参数，它将`getName`方法中的`this`对象设置为`joe`对象。

在 JavaScript 中，有两种方法可以用`function`关键字声明一个函数。一种方法是使用函数声明，也称为函数语句，另一种方法是使用函数表达式。

函数声明用关键字`function`后跟函数名来定义函数。它们在程序中的任何地方都可用。

函数表达式是赋给变量的函数。它们只有在声明并赋给变量后才可用。

在 JavaScript 中，函数是一级的，这意味着它们是我们可以动态定义的对象。

考虑到这些特性，我们可以以不同于 Java 等语言的方式使用，在 Java 中，函数只能在类内部。