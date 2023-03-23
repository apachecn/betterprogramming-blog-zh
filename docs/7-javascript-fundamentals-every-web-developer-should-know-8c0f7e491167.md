# 每个 Web 开发者都应该知道的 7 个 JavaScript 基础知识

> 原文：<https://betterprogramming.pub/7-javascript-fundamentals-every-web-developer-should-know-8c0f7e491167>

## 函数是值，对象从其他对象继承，等等

![](img/fe392adec6806093e516aa90202da067.png)

埃里克·布洛林在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在本文中，我们将看看我认为 JavaScript 的一些最重要的——也是最独特的——特性。

# 1.功能是独立的行为单位

[功能](/10-fundamentals-you-need-to-know-about-functions-in-javascript-8e74579b7a9a)是行为的单位，但这里重要的一点是它们是独立的。在其他语言中，如 Java 或 C#，函数必须在类中声明。在 JavaScript 中不是这样。

函数可以在全局范围内声明，也可以在模块内定义为可以重用的独立单元。

# 2.对象是道具的动态集合

对象实际上只是属性的集合。在其他语言中，它们被称为映射、哈希映射或哈希表。

它们是动态的，因为一旦创建，就可以添加、编辑或删除属性。

下面是一个简单的[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)，使用对象的字面语法定义。它有两个属性:

```
const game = {
  title : 'Settlers',
  developer: 'Ubisoft'
}
```

# 3.对象从其他对象继承

如果您来自基于类的语言，如 Java 或 C#，您可能习惯于从其他类继承的类。同样，在 JavaScript 中情况并非如此。

对象从称为原型的其他对象继承。

如前所述，在这种语言中，对象是属性的集合。当创建一个对象时，它有一个名为`__proto__`的“隐藏”属性，用来保存对另一个对象的引用。这个被引用的对象称为原型。

下面是一个创建空对象的例子(可以说是一个没有属性的对象):

```
const obj = {};
```

即使它看起来是空的并且没有属性，它也有一个对`Object.prototype`对象的引用。它拥有`__proto__`隐藏的属性:

```
obj.__proto__ === Object.prototype;
//true
```

例如，在这种对象上，我们可以访问`toString`方法，即使我们还没有定义这样的方法。这怎么可能呢？

这种方法是从`Object.prototype`那里继承来的。当试图访问该方法时，引擎首先试图在当前对象上找到它，然后查看其原型的属性。

不要被[类](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)关键字误导。它只是原型系统上的语法糖，试图让来自基于类的语言的开发人员熟悉这种语言。

# 4.函数是值

[函数是 JavaScript 中的值](https://medium.com/programming-essentials/make-your-code-easier-to-read-with-functional-programming-94fb8cc69f9d)。像其他值一样，它们可以分配给变量:

```
const sum = function(x,y){ return x + y }
```

这是任何其他语言都做不到的。

像其他值一样，函数可以传递给不同的函数或从函数返回。下面是一个函数返回另一个函数的示例:

在同一个例子中，我们可以看到从`startsWith`函数返回的函数是如何作为参数发送给`filter`数组方法的。

# 5.函数可以变成闭包

函数可以在其他函数中定义。内部函数可以引用其他函数中的变量。

此外，在外部函数执行之后，内部函数可以引用外部函数中的变量。下面是一个例子:

`count`函数可以从`createCounter`父函数中访问`x`变量，即使在它被执行之后。`count`是一个[闭合](https://medium.com/programming-essentials/you-will-finally-understand-what-closure-is-13ba11825319)。

# 6.图元被视为对象

JavaScript 给人一种错觉，认为原语就是对象，就像对待对象一样。事实是原语不是对象。原语不是属性的集合。

然而，我们可以在原语上调用方法。例如，我们可以在字符串上调用`toUpperCase`方法:

```
const upperText = 'Minecraft'.toUpperCase();
console.log(upperText);
//'MINECRAFT'
```

像`'Minecraft'`这样的简单文本是原语，没有方法。JavaScript 使用内置的[字符串](https://medium.com/programming-essentials/how-to-convert-values-into-strings-in-javascript-13df1c09b3ed)构造函数将其转换成一个对象，然后对新创建的对象运行`toUpperCase`方法。

通过在幕后将原语转换为包装对象，JavaScript 允许您调用它们的方法，从而将它们视为对象。

# 7.JavaScript 是一种单线程语言

JavaScript 是单线程的。这意味着在特定时间只执行一条语句。

两个函数不能在主线程中同时执行。

您可能听说过像 web workers 一样并行执行函数的选项，但是 worker 不与主线程共享数据。他们只通过信息传递来交流，没有什么是共享的。

这使得事情更容易理解，我们只需要注意让函数运行得更快。一个需要很长时间执行的函数会使页面没有响应。

关于 JavaScript 基础的更多内容，你可以看看[学习这些 JavaScript 基础，成为更好的开发者](https://medium.com/programming-essentials/learn-these-javascript-fundamentals-and-become-a-better-developer-2a031a0dc9cf)。

感谢阅读。