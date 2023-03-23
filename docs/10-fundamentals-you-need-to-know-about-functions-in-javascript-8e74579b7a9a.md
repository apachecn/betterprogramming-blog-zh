# 关于 JavaScript 中的函数，你需要知道的 10 个基础知识

> 原文：<https://betterprogramming.pub/10-fundamentals-you-need-to-know-about-functions-in-javascript-8e74579b7a9a>

## 定义后自动执行，函数可以是闭包，等等

![](img/bbc56a757f58a7822f2d64adc6f63112.png)

照片由[戴着红色帽子](https://unsplash.com/@girlwithredhat?utm_source=medium&utm_medium=referral)的女孩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

函数是 JavaScript 中最重要的概念之一。在这篇文章中，我们将看看你可能需要了解的一些基本知识。

# 1.可以使用函数声明来声明函数

```
function compute(){}
```

当使用函数声明时，`function`关键字是第一行。函数必须有名称，并且可以在定义前使用。

函数声明被移动或“提升”到其作用域的顶部。这意味着我们可以在函数被声明之前使用它。以下示例是使用函数声明创建的函数的有效用法:

```
compute();function compute(){}
```

# 2.可以使用函数表达式创建函数

```
const compute = function() {};
```

当关键字`function`不是一行中的第一个时，我们创建函数表达式。在这种情况下，名称是可选的。可以有匿名函数表达式，也可以有命名函数表达式。

首先需要定义函数表达式，然后才能调用它们。以下示例不是函数表达式的有效用法。函数表达式不被提升。

```
compute();
//Uncaught ReferenceError: Cannot access 'compute' before initializationconst compute = function() {};
```

# 3.可以使用箭头语法创建函数

```
const compute = () => {};
```

arrow 函数是一个用于创建匿名函数表达式的 sugar 语法。与其他函数表达式一样，我们不能在创建之前使用函数。以下示例不是用 arrow 语法创建的函数表达式的有效用法。

```
compute();
//Uncaught ReferenceError: Cannot access 'compute' before initializationconst compute = () => {};
```

# 4.函数表达式可以在定义后自动执行

的确，函数表达式可以在定义时被调用。这类函数被称为立即调用函数表达式(IIFE)。

这是一个生活的例子。请注意，`function`不是该行的第一个关键字。

```
(function doSomething(){
  console.log('run');
})();
//'run'
```

您可能见过在`function`关键字之前使用的`!`操作符，并想知道为什么。这就是为什么。它用于将函数转换为函数表达式，以便可以自动执行。

```
!function doSomething(){
  console.log('run')
}();
```

# 5.函数可以是方法

函数可以用作对象的方法。看下一个例子:

```
const obj = {
  compute: function(){}
}obj.compute();
```

`compute`属性保留一个函数。它是一个方法属性。它还有一个简写语法，如下所示:

```
const obj = {
  compute(){}
}
```

当一个函数作为一个对象的方法使用时，它需要访问那个对象。[这个](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)关键字提供了对对象的访问，其中函数被用作方法。

考虑下一个用两个道具创建一个`ratingObj`的例子。`ratings`道具存储一个数组，`rateIt`道具保存一个函数。

`rateIt`方法允许向`ratings`数组添加几个等级，并返回平均等级。

注意如何使用`this`关键字在`rateIt()`函数中访问`ratings`属性。

# 6.箭头函数没有自己的这个

当函数作为方法使用时，它们需要访问它们所附加的对象的属性。`this`关键字指的是那个对象。

用 arrow 语法创建的函数没有自己的`this`，它们不应该作为方法使用。它们的主要目的是创建内联函数。

下面是一个创建内嵌匿名函数作为内置实用程序`setTimeout`回调的例子。用 arrow 语法创建的内部函数是在方法内部定义的。arrow 函数中的`this`关键字引用了与`delayMessage`方法相同的对象。

# 7.函数是对象

函数是 JavaScript 中的对象，这意味着它们是属性的动态集合。

它们从`Function.prototype`对象继承属性，后者又从`Object.prototype`对象继承。检查`sum`功能。

```
function sum(x, y){
   return x + y;
}Object.getPrototypeOf(sum) === Function.prototype;
//true
```

函数继承类似`call`或`apply`的方法属性。

`call`使用给定的关联对象(`this`值)和单独提供的参数调用函数。

下面是一个使用`call`方法调用`sum`函数的例子。这个调用没有关联的对象，所以我们可以将第一个参数作为`null`发送。

```
const total = sum.call(null, 1, 2);
//3
```

使用给定的关联对象和以数组形式提供的所有参数调用函数。

```
const total = sum.apply(null, [1, 2]);
//3
```

# 8.函数可以作为参数发送给其他函数

函数是对象，所以像其他对象一样，它们可以被发送给函数。

在下一个例子中，函数`isEven`作为参数被发送给`filter()`数组方法。

# 9.函数可以从函数中返回

[对象](https://medium.com/programming-essentials/7-things-you-should-know-about-objects-in-javascript-c0b2f251e160)可以从函数中返回。

```
function getEmptyObject() {
  return {};
}
```

像其他对象一样，函数也可以从函数中返回。

```
function createFunction(x){
  return function(){};
}
```

注意`function`不是该行的第一个关键字，所以我们返回一个函数表达式。

# 10.函数可以是闭包

函数可以在其他函数内部声明，并且可以从外部函数访问变量。

重要的是要知道，即使在外部函数执行之后，内部函数也可以访问这些变量。考虑下一个例子:

`createRateIt`函数返回另一个函数。

你可能认为这个例子中的`ratings`变量可以在`createRateIt`函数执行后被垃圾收集，但事实并非如此。`ratings`变量也被`rateIt`函数引用，该函数在`createRateIt`函数执行后仍然有效。

`rateIt()`功能是一个[闭合](https://medium.com/programming-essentials/you-will-finally-understand-what-closure-is-13ba11825319)。即使在执行之后，它也可以从其父函数`createRateIt`访问该变量。

[闭包](https://medium.com/programming-essentials/you-will-finally-understand-what-closure-is-13ba11825319)封装数据。`ratings`数组封装在`rateIt`闭包函数内部，不能从外部直接访问。它只能通过使用 closure 函数来更改。

关于 JavaScript 基础的更多内容，你可以看看[学习这些 JavaScript 基础，成为更好的开发者](https://medium.com/programming-essentials/learn-these-javascript-fundamentals-and-become-a-better-developer-2a031a0dc9cf)。

感谢阅读。