# 如何检查 JavaScript 对象上是否存在属性

> 原文：<https://betterprogramming.pub/how-to-check-if-a-property-exists-on-a-javascript-object-4601a03f7546>

## 三种不同用例的三种方法

![](img/b781abcf595cc74ea8a051a3d87ba7ff.png)

照片由[马特·拉瓦瑟](https://unsplash.com/@mattylava?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

在 JavaScript 中，有几种方法可以检查对象上是否存在属性。选择哪一个很大程度上取决于您的用例，所以您需要知道每一个是如何工作的。

我们来看看最常用的方法。

# 1.检查真实性

检查属性的一个非常简单的方法是简单地检查属性是否真实:

如您所见，这导致了几个关于 [falsy](https://developer.mozilla.org/de/docs/Glossary/Falsy) 值的问题，所以使用这种方法时要非常小心。

# 2.in 运算符

如果一个属性存在于一个对象上或者沿着它的原型链，`in`操作符返回`true`:

`in`操作符没有虚假值的问题。然而，它也为原型链上的属性返回`true`。这可能是你想要的。如果没有，你应该使用第三种方法。

# 3.hasOwnProperty()

`hasOwnProperty()`继承自`Object`。像`in`操作符一样，它检查对象上是否存在属性，但不考虑原型链。

不过，要小心。不是每个对象都继承自`Object`:

如果你很少遇到这种情况，你仍然可以这样使用它:

# 结论

这三种方法都有各自的用例。重要的是，你要熟悉他们的不同之处，这样你才能挑选出最好的。只要确保你的代码传达了你的意图。

# 参考

*   [https://developer.mozilla.org/de/docs/Glossary/Falsy](https://developer.mozilla.org/de/docs/Glossary/Falsy)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/hasOwnProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)