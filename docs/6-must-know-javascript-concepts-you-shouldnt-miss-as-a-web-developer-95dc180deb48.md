# 作为一名 Web 开发人员，你不应该错过的 6 个必须知道的 JavaScript 概念

> 原文：<https://betterprogramming.pub/6-must-know-javascript-concepts-you-shouldnt-miss-as-a-web-developer-95dc180deb48>

## 用五分钟时间了解最基本的 JS 概念

![](img/87aaa5ce45b9bc6238c0aef522867dfe.png)

妮可·沃尔夫在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

> “JavaScript 的优势在于你可以做任何事情。缺点是你会。”-雷吉·布莱斯维特

JavaScript 无处不在。是啊，到处都是。从互联网上超过 90%的网站到移动开发等应用开发领域，甚至是机器学习，所有的东西都在 JavaScript 上运行。

也许尼克·摩根总结得最好:

> “JavaScript 是一个皮套，里面有一把非常非常锋利的刀。那把刀可以切任何东西，有了它，你可以做任何事情。你可以杀死一只熊。你可以抓鱼。你可以把一块木头削成一匹小马。它甚至是一根牙签。”

JavaScript 基本上被认为是 web 开发的主干。世界上有超过 18 亿个[网站，其中 95%使用 JavaScript。无论我们谈论的是小型创业公司还是大型科技公司的网站或应用程序，几乎所有的网站和应用程序都需要很好的语言知识。](https://www.internetlivestats.com/total-number-of-websites/)

成为一名熟练的 JS 开发人员或 web 开发人员，需要的不仅仅是用 JavaScript 语言编写代码——它需要技能、思维，最重要的是，它需要对这种语言的重要概念有很好的了解。

所以这里有一些 JavaScript 概念，作为一个成功的 web 开发人员应该知道:

# 1.穿越大教堂

JavaScript 确实是 web 开发的强大武器，但是如果开发人员不能遍历 DOM，这真的没有关系。一个熟练的 JS web 开发人员应该知道如何遍历 DOM。

文档对象模型( [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) )是 HTML 和 XML 文档的编程接口。它是一个 API，允许开发人员使用 JavaScript 对 HTML 进行修改。这个概念是构建动态网站的关键，有助于在前端链接 HTML 和 JavaScript。

DOM 本质上是一个树状结构，我们的 DOM 中的元素被安排在一个层次结构中，这个层次结构用于帮助组织我们的 HTML 元素，找出什么样的 CSS 样式规则适用，以及什么样的规则必须适用于什么样的事物。

JavaScript 就是通过根据用户需求动态改变页面来增强用户体验。理解如何使用 JS 遍历 DOM 确实是 web 开发中需要的基本技能之一。

# 2.范围

范围是 JavaScript 的一个功能，基本上是指代码的当前上下文，它决定了变量的可见性和可访问性。

范围是 JavaScript 中需要理解的一个重要概念，因为它决定了整个代码中变量、函数和对象的生命周期、访问和可见性。作用域的主要优点之一是，它通过设置某些限制为您的代码提供了一定程度的安全性。

在 JavaScript 语言中，有两种类型的作用域:

*   全球范围

在任何函数外部声明的变量都被认为是全局变量，可以从任何函数中访问和修改。

*   局部范围

局部变量是在函数中声明的变量，只能在该函数中访问。

示例:

```
// program showing local scope of a variablelet x= "GoodMorning";

function greetings() {
    let y= "People"
    console.log(x + y);
}

greetings();
console.log(x + y); // error
```

输出:

```
GoodMorningPeople
Uncaught ReferenceError: y is not defined
```

在上面的例子中，变量`x`是一个全局变量，而变量`y`是一个局部变量，只能在函数`greetings`中访问——当我们试图在函数外部访问变量`y`时，就会出现错误。

# 3.关闭

在 JavaScript 中，闭包是被广泛讨论的重要概念之一。

闭包是一个可以从另一个函数的作用域访问变量的函数，这是通过在函数内部创建一个函数来实现的。如 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) 中所定义:

*“闭包是指独立(自由)变量的函数。换句话说，闭包里定义的函数‘记住’了它被创建的环境。”*

在 JavaScript 中，闭包是在每次创建函数时创建的。大多数 JavaScript 开发人员有意识或无意识地使用闭包——但是了解闭包可以在使用它们时更好地控制代码。

示例:

在上面的例子中，函数`sName()`是闭包；它有自己的局部作用域(使用变量`welc`)，也可以访问外部函数的作用域。在执行`Spellname()`之后，作用域不会被破坏，函数`sName()`仍然可以访问它。

# 4.值与参考变量赋值

理解 JavaScript 如何分配变量是编写无 bug JavaScript 的基础。这个概念是当今困扰网站的无数错误的根源。简单地看一下计算机内存就能帮助我们找出到底发生了什么。

JavaScript *总是*通过值来分配变量。当赋值恰好是 JavaScript 的五种原语类型之一时，即`Boolean`、`null`、`undefined`、`String`和`Number`，实际值被赋值。

然而，当赋值恰好是 JavaScript 的三种数据类型(通过复制它们的引用来复制它们)时，内存中对象的引用被赋值:`Array`、`Function`和`Object`。

# 5.立即调用函数表达式(IIFE)

立即调用函数表达式(IIFE)是 JavaScript 中最流行的设计模式之一。

生活是一种立即执行功能的方式——一旦它们被创建。

生命非常有用，因为它们不会污染全局对象，因为在生命中声明的变量不能被外界访问。使用 IIFE 还意味着立即执行代码并获得数据隐私。

如果我们有一些不需要再次使用的启动代码，那么我们可以使用生命模式。

示例:

```
(function () {
  // some initiation code
  let num1;
  let num2;
})();

// num1 and num2 will be discarded after the function is executed.
```

# 6.提升

在 JavaScript 中，提升是一种行为，其中函数或变量可以在声明之前使用。

基本上，它给了我们一个优势，无论变量和函数在哪里声明，它们都被移动到它们作用域的顶部，无论它们的作用域是局部的还是全局的。

示例 1:在声明后调用函数

```
function dog(sound){
  console.log(sound);
}
dog('bark');
```

示例 2:在声明之前调用函数(使用提升)

```
dog('bark');
function dog(sound){
  console.log(sound);
}
```

无论你是在声明之前还是之后调用一个函数，上面的两个例子都会在你的控制台中得到相同的输出`bark`，这就是 JavaScript 中的提升。