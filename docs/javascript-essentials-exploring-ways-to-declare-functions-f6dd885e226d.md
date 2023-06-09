# JavaScript Essentials:探索声明函数的所有不同方式

> 原文：<https://betterprogramming.pub/javascript-essentials-exploring-ways-to-declare-functions-f6dd885e226d>

## 让我们深入探讨声明函数的不同方式以及如何使用它们

![](img/06c2a98203848993dfec062dd928c5b7.png)

照片由[杰米街](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上的 [Unsplash](https://unsplash.com/s/photos/explore?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 介绍

在 JavaScript 中，有几种不同的方法来声明函数，如果不知道它们之间的区别，选择合适的方法有时会令人困惑。因此，让我们深入研究定义 JavaScript 函数的所有方法。

一个*函数*是一个设计用来执行特定任务的代码块，可以被调用任意次。它减少了我们程序中的冗余代码。语句序列被称为*函数体。*

## 函数是一级对象

在 JavaScript 中，函数是一级对象，因为它们可以是:

*   分配给变量
*   作为参数传递给函数
*   作为函数的值返回
*   用作对象属性

在 JavaScript 中，函数总是返回值。如果没有指定返回值，函数将返回`undefined`。

# 声明函数的不同方式

可以使用几种方法来声明函数:

## **功能声明**

```
function funcName(){};
```

## **函数表达式**

```
*//Anonymous Function Expression*
let name = function(){};*//Named function Expression
let name = function getName() {};*
```

## **箭头功能**

```
let name = params => params* 2;
```

## **立即调用函数表达式(****)**

```
let name = (function(){
            return "javascript";
            })();
```

# 函数声明

函数声明是 JavaScript 中最古老的声明函数的方式之一。它使用关键字`function`，后跟函数名；圆括号内的参数列表，如`(param1,param2...)`；和一对花括号`{..}`，将函数语句括起来。

```
function welcomeUser() {
    alert("Welcome Back 😊!!");
}
```

上述函数可以通过它的名字来调用，`welcomeUser()`。

要创建函数声明，函数定义必须以关键字`function`开头，后跟函数名。函数的名称是必须的，省略名称会导致无效的语法错误。

## 提升

提升的概念不仅适用于变量，也适用于函数声明。

关于函数声明有趣的事情是它的函数定义被提升了——也就是说，在代码执行之前，JavaScript 将它们移动到了它们作用域的顶部，从而允许我们在定义函数之前调用它。

```
***// Outputs: Welcome Back* 😊*!!***welcomeUser();function welcomeUser() {
     alert("Welcome Back 😊!!");
}
```

在上面的程序中，整个函数定义被提升到作用域的顶部。但是对于变量(用`var`声明),只提升它们的声明——而不是它们的值。

# 函数表达式

定义函数的另一种方法是使用表达式。在这里，函数被赋给变量，这也是函数成为一级对象的原因。

```
***//function definition is stored in variable sum***
let sum = function(x,y) {
     return x + y;
}sum(5,6);
```

将函数表达式存储在变量中后，该变量可用于调用函数。

函数表达式可以是命名函数，也可以是匿名函数。一个匿名函数表达式没有名字。

```
***//Anonymous Function expression***
let getName = function() { return "JavaScript" }
```

但是，您也可以提供函数表达式的名称。当你想在函数体内引用当前函数时，这很有用——例如，在递归的情况下。

但是这个名字只能在函数体内部访问，不能在外部调用函数。

```
***// Named Function Expression***var factorial = function calculateFac(n) 
{ 
   return n < 2 ? 1 : n * calculateFac(n - 1);   
};

console.log(factorial(6));***//Uncaught ReferenceError: calculateFac is not defined***
console.log(calculateFac(7));
```

如果我们想将函数作为参数传递给另一个函数，函数表达式是很有用的。

```
let multiply = function(num1) {
                return num1 * 10;
               }function calculateFunc(num, func){
      return func(num);
}***//The*** ***multiply*** ***function expression definition is passed as an argument to*** ***calculateFunc***calculateFunc(3,multiply);
```

## **吊装**

与函数声明相比，函数表达式没有被提升，也就是说，它不能在定义之前被调用。

```
***// this will work*** let a = function(){ return "JS 🤩" };
 a();***//this doesn't work***b();             ***// TypeError (b is undefined)*** 
 let b = function(){ return "JS 🤩" };
```

# 箭头功能

上面，我们已经介绍了使用`function`关键字定义函数的方法。然而，随着 ES6 的加入，出现了一种新的、简洁的、流行的定义函数的方法，称为*箭头函数表达式*或*胖箭头函数* ( `=>`)。箭头函数总是匿名函数。

```
let sum = (a,b) => { return a+b } sum(3,4);
```

## **带 ES5 语法的传统函数**

```
[2, 4, 6].map(function(x) {return x * x});***// [4, 16, 36]***
```

## **我们现在可以这样写**

```
[2, 4, 6].map(x => x * x);***// [4, 16, 36]***
```

与函数表达式一样，箭头函数也不会被提升。

## 立即调用函数表达式(IIFE)

一个*life*(读作 *iffy* )是一个 JavaScript 函数，它在创建后立即运行。

```
( **function** () {  
     *// code here
* })();or( () => {   
   *//code here*
})();
```

调用括号也可以放在表达式括号内，并且不影响 life**函数的功能。**

```
(function() {
  *//code here*
}());
```

**IFFE 是一个匿名函数，它被包装在括号内，将它转换成 JavaScript 表达式，然后使用`()`立即执行。**

**使用 IIFE 的基本原因是为了获得数据隐私。在生命内部声明的变量对外部世界是不可见和不可访问的。**

**在 JavaScript 中，括号内的所有内容都被视为表达式，JS 表达式返回值。在上面的例子中，表达式将返回一个函数，然后立即执行。**

**一元运算符也可以用来将任何东西转换成表达式，但是它们很少被使用，并且不支持箭头函数。**

**`**!function** () { */** code here**/* };`**

**`**+function ()** { //code here }`**

## **返回值并将参数传递给函数**

**IIFEs 可以返回一个可以赋给变量的值。让我们看一个简单的例子。**

```
let result = ( function() {
                return "I am working";
              })();console.log(result);  **//"I am working"**
```

*   **我们有一个返回字符串的生活`“I am working”`**
*   **当执行上述代码时，结果变量将记录从 IIFE 返回的值**

**参数可以很容易地在调用时传递给生命。**

```
( function (num1, num2) {
     console.log( num1 + num2 );
  }
)(2,3);  **//arguments passing**
```

# **关键要点**

**以下是一些要点:**

*   **函数是为执行特定任务而设计的代码块**
*   **函数声明会被提升，但函数表达式不会**
*   **函数*总是*返回值——如果我们不指定返回值，它将返回`undefined`**
*   **功能是一级对象**
*   **生命函数一旦被定义就被调用——如果需要数据隐私，就使用生命函数**

# **参考**

*   **MDN Web Docs:[JavaScript 中的函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)**
*   **MDN 网络文档:[生活功能](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)**

**感谢阅读！**