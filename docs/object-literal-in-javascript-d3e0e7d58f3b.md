# JavaScript 中的对象文字

> 原文：<https://betterprogramming.pub/object-literal-in-javascript-d3e0e7d58f3b>

## 这些在面试中出现的次数比你想象的要多

![](img/bf060a121ba93f5c158d3a973190650f.png)

由 [Arian Darvishi](https://unsplash.com/@arianismmm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 中的对象文字是什么？

不止一个面试官问过我这个前端职位的问题。如果您做过任何类型的 JavaScript 开发，那么您在某个时候都使用过对象文字。我将解释并提供一些示例代码。

# 对象文字

简单地说，对象文字是花括号内逗号分隔的名称-值对列表。

这些值可以是属性和函数。下面是一个对象文字的片段，它有一个属性和一个函数。

```
var **greeting** = **{**
    *fullname*: "Michael Jackson",
    *greet*: (message, name) => {
        console.log(message + " " + name + "!!");
    }
**}**;
```

下面是使用我们刚刚创建的对象来记录`fullname`并使用`fullname`值调用`greet`函数的代码片段。

假设我们想访问`greet`函数中的`fullname`，而不是从参数中读取它。如果我们尝试这样直接访问它:

```
greet = (message)=> {
     console.log(message + " " + fullname + "!!");
}
```

我们将得到一个未定义的错误。`fullname` 属性不直接可见。

但是，您可以创建一个函数(例如，`getFullName()`)来使用`this`关键字访问`fullname`值，这只是当前的对象上下文。

如果你想阅读更多关于关键词`this`的内容，请访问 [W3 学校](https://www.w3schools.com/js/js_this.asp)。该函数如下所示:

```
***getFullName***: () => {
     return this.***fullname***;
}
```

greet 函数现在看起来像这样:

```
greet: (message) => {
    console.log(message + " " + this.***getFullName***() + "!!");
}
```

JavaScript 中对象文字的所有成员，包括属性和函数，都是公共的。唯一可以放置私有成员的地方是函数内部。

例如，我可以在`greet`函数中添加一个私有变量来进一步定制消息。该函数的代码现在看起来像这样。

```
(message, name) => {
    var ***customMessage*** = *message + " " + name +
        "!!! It's nice to meet you."*
    console.log(***customMessage***);
}
```

`customMessage`变量只在你的函数中可见，但是如果它作为一个属性放在函数之外，那么它将是公共的。

将一个对象文本赋给另一个变量只执行一次浅层复制，这意味着您获得的是对象的引用而不是实际值。

假设我想使用我们之前创建的 greeting 对象创建另一个`greeting`对象。

```
var anotherGreeting = greeting;
```

这样做起初看起来不错，但是对复制对象(`anotherGreeting`)的任何更改都会反映在原始对象中(问候语)。

```
var copiedGreeting = greeting;
copiedGreeting.fullname = "Michael Buble";
console.log(greeting.fullname + " == " + copiedGreeting.fullname);**---------------output----------------** Michael Buble == Michael Buble
```

如果不手动复制所有值，就无法复制对象文字。

最后，您可以改变对象文字的成员，这意味着您可以随意添加和删除它们。

如果我需要从对象中移除`fullname`属性并添加其他属性(例如，名和姓)，我可以简单地这样做。

```
***delete*** greeting["**fullname**"];
greeting.***firstname*** = "Michael";
greeting.***lastname*** = "Jackson";
```

如果您想随时创建对象，而不想将值复制到另一个对象或者将一个对象映射到另一个对象，那么应该使用 Object literal。

如果您需要创建一个结构的多个实例，并基于预定义的值执行操作，那么您应该使用一个函数构造器，即一个实例对象 ***、*** ，我将在另一篇文章中深入讨论。

感谢您的阅读。编码快乐！