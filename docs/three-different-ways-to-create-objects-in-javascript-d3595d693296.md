# 用 JavaScript 创建对象的三种不同方式

> 原文：<https://betterprogramming.pub/three-different-ways-to-create-objects-in-javascript-d3595d693296>

## 对象文字只是开始

![](img/72221e7a58ffe94a74330450953c33a6.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Javascript 中的几乎所有东西都是对象，不管是数组还是函数。在这篇文章中，我们将学习用 JavaScript 创建对象的三种不同方法:

1.  对象文字
2.  新关键字
3.  班级

# **对象文字**

JavaScript *对象文字*是用大括号括起来的逗号分隔的名称-值对列表。对象文字封装数据，将其封装在一个整洁的包中。

对象文本属性值可以是任何数据类型，包括数组文本、函数文本和嵌套的对象文本。

## 速记属性名

考虑一个场景，你必须在一个对象中放置不同的变量。一种方法是:

使用 [ECMAScript 2015](https://www.ecma-international.org/ecma-262/6.0/) ，可以使用更短的符号来实现相同的功能:

## 重复的属性名称

如果两个属性使用相同的名称，第二个属性将覆盖第一个属性。

重复的属性名称

# `**New**` **关键词**

`Object`构造函数为给定值创建一个对象包装器。如果值是`[null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)`或`[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`，它将创建并返回一个空对象。否则，它将返回与给定值相对应的类型的对象。

使用`new` 关键字也可以创建对象。利用 Javascript 中内置的对象构造函数，`new`创建一个空对象；或者，这个关键字可以和一个自定义的构造函数一起使用:`with builtin Object Constructor`。

首先，看一下这个例子:

新对象()

下一步是向这个空对象添加属性和方法。这可以通过简单的*点符号实现:*

但是，不推荐这种做法，因为在后台有一个范围解析来检查构造函数是内置的还是用户定义的。

## 用户定义的构造函数

函数也可以用来在 JavaScript 中创建对象。如果你仔细想想，它们已经是对象了——所以基本上，对象是用来创建更多的对象的。

通常，此方法优于对象构造函数。想象一下，你必须创建数百个具有相同属性的对象；使用对象构造函数方法，你必须手动添加所有的属性到所有的对象中，但是使用函数构造函数，这些属性可以被预定义。

使用同一个函数构造器，可以创建任意数量的对象。

# 使用 ES6 类创建对象

这个方法非常类似于在用户定义的函数构造函数中使用`new`。类是面向对象编程(OOP)的主要组件。可以创建实际上是对象的类的许多实例。构造函数现在可以被类代替，因为它们在 [ES6](https://es6.io/) 规范中得到支持

这里我定义了构造函数中的所有参数。

方法可以是类的一部分，而声明可以在以后添加到创建的类实例“objects”中:

这里这个方法是对象的一部分，不会影响我们原来的类。

类和构造函数都模仿 JavaScript 的面向对象继承模型，JavaScript 是一种基于原型的继承语言。

熟悉类非常有帮助，因为流行的 JavaScript 库如 [React](https://reactjs.org/) 经常使用`class`语法。