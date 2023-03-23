# 通过浅层复制数据避免 JavaScript 中的共享可变状态

> 原文：<https://betterprogramming.pub/avoiding-shared-mutable-state-in-javascript-by-shallow-copying-data-ad6051f0eaa2>

## 如何做一个浅显的文案

![](img/ebb4414291110ae3d9eaa17fab3ba9cd.png)

[freestocks.org](https://unsplash.com/@freestocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/copy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在 JavaScript 中，像任何其他编程语言一样，事物都存储在变量中，这些变量可以随时更改。

这可能是一个问题，因为我们可能会意外地更改共享的内容。让许多代码共享同一个可变状态是很难追踪的。这使得调试和阅读代码变得困难。

例如，如果我们在不同的函数中更改同一个数组，如下所示:

然后`arr`的值根据最后调用的是`foo`还是`bar`而变化。如果`foo`被调用，那么`arr`就是`[1, 2, 3]`。另一方面，如果调用了`bar`，那么`arr`就是`[4, 5, 6]`。

这是一个问题，因为代码越复杂，函数调用就越多。如果许多函数都这样做，那么跟踪值就很困难，调试也很混乱。

此外，很难理解调用带有这些副作用的函数时逻辑是如何流动的。

有几种方法可以避免这种情况。有一个`const`关键字来防止重新分配。此外，我们可以复制对象以防止原始对象被修改。

# 对常量使用 const

如果我们想在代码的不同部分之间共享常量，那么我们可以使用`const`关键字来声明常量。这可以防止它们被修改。

例如，如果我们写:

那么无论何时调用`foo`或`bar`，我们都会得到一个错误。

![](img/fdbdf0e0199d9fc4bd4795d6b7171262.png)

[狮子狗帮](https://unsplash.com/@thepoodlegang?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 浅层复制数据

当我们在函数中操作数据时，我们还可以通过复制原始数据来防止原始数据被更改。

复制数据有两种方法。一种是进行浅层复制，我们复制一个对象或数组的顶级条目。如果我们有嵌套的数组或对象，那么我们必须做一个深度拷贝，拷贝对象或数组的所有级别。

在本文中，我们将了解如何浅层复制数据。

要进行浅层复制，我们可以使用 spread 运算符。它对对象和数组都有效。例如，我们可以为对象编写以下内容:

我们可以为数组编写以下代码:

```
let arr = [1, 2, 3];
let arrCopy = [...arr];
```

使用 spread 运算符时有一些限制。首先，原型不是复制的，所以如果我们有从原型继承的东西，如下面的代码:

我们看到第一个`console.log`输出与第二个完全不同。`obj`的原型是我们明确设定的`{foo: 1}`。但是，`objCopy`的原型是`Object.prototype`，完全不一样。

像正则表达式这样的特殊对象也有不被复制的特殊内部槽。此外，我们可以从上面的日志输出中看到，继承的值没有使用 spread 操作符进行复制。

此外，只复制可枚举的属性。这意味着，例如，如果我们使用 spread 运算符将一个数组复制到一个对象，如下所示:

我们在第二个`console.log`的输出中缺少了`length`属性。

最后，getters、setters 和属性描述符也不会被复制。例如，如果我们写:

然后我们看到`obj`和`objCopy`的属性描述符是不同的。

我们可以绕过这些问题。我们可以将原始对象的原型复制到新对象中，如下所示:

然后我们得到在上面的`console.log`语句中`obj`和`objCopy`有相同的原型。

通过编写以下代码，我们可以复制该值以及其他属性描述符:

属性描述符对象包括一个属性的值，所以我们可以用`defineProperties`方法定义所有属性，并通过调用`getOwnPropertyDescriptors`传入属性描述符，通过传入`obj`来获得`obj`的属性描述符。

我们应该看到属性`b`在`obj`和`objCopy`的属性描述符中都将`writable`设置为`false`。

防止共享可变状态是 JavaScript 中的一个问题。我们希望避免这种情况，以防止共享数据发生变异，这使得跟踪代码和调试变得困难。

用 JavaScript 精确复制对象是很棘手的。spread 运算符不会彻底复制对象。属性描述符、getters 和设置以及 prototype 不会被复制。

大多数问题可以通过手动复制它们来解决，就像我们对对象的原型和属性描述符所做的那样。

对于常量，我们使用`const`来防止意外的重新分配。