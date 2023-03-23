# JavaScript 中的装饰器是什么？

> 原文：<https://betterprogramming.pub/what-is-a-decorator-in-javascript-a098a243ff16>

## 使用装饰者来清理和简化你的代码

![](img/28a484aa18b71d46513bc18c6540fca0.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当你用 JavaScript 写一个类的时候，你可能不得不向类中的方法添加更多的特性。但有时它们看起来又脏又乱。

如何能让过程更优雅？在这篇文章中，我将谈论一个很有前途的功能，装饰。

# 阅读前

这项功能不包括在最新的 ECMA-262 中，换句话说，是 JavaScript。在你的项目中，你应该经常使用巴别塔。

装修人员的提议目前处于第二阶段。你可以在 [GitHub](https://github.com/tc39/proposal-decorators) 上查看这个提议。

我在这篇文章中附上的例子是用 [JSFiddle](https://jsfiddle.net/) 编写的，采用了巴别塔+ JSX 的配置。如果你想在你的项目中使用这个特性，你应该自己建立 Babel。

# 没有室内装潢师

有一个类`Medium`，在其构造函数中使用作者的名字。有一个函数返回作者的名字。

让我们创建一个`Medium`类型的属性。

```
const medium = new Medium('Jane');const fakeMedium = {
  writer: 'Fake Jane',
  getWriter: medium.getWriter,
};
```

`medium`是使用`Medium`的构造函数创建的，不像`fakeMedium`是一个对象文字。但是和`medium`有着相同的属性。

现在，让我们比较一下`getWriter`的结果。

```
medium.getWriter(); // Jane
fakeMedium.getWriter(); // Fake Jane
```

价值观为什么不一样？

这是因为 JavaScript 的普通函数`this`被绑定到实际调用该函数的对象上。

`medium.getWriter()`被`medium`对象调用，然而`fakeMedium.getWriter()`被`fakeMedium`调用。因此，函数`getWriter`中的`this`从`fakeMedium`中查找值。

[本文](https://medium.com/better-programming/javascript-whats-the-difference-between-normal-and-arrow-functions-74c367324ae1)概述了普通函数和箭头函数的区别。

为了获得与调用`medium.getWriter`时相同的结果，让我们使用`Object.defineProperty`。`Object.defineProperty`所做的是在对象上定义新的属性或者修改对象的现有属性，然后它返回对象。

每当`fakeMedium.getWriter`被调用时，`Access to getWriter`就会被打印两次。但是为什么两次？

1.  当您调用`fakeMedium.getWriter()`时，它的 getter-mode 被检测到并运行定制的`get`方法。
2.  在`get`方法内部，`getWriter`被`medium` — `this.getWriter.bind(medium)`新绑定。这里的`this`是指`fakeMedium`本身。所以和`fakeMedium.getWriter.bind(medium)`一样。这就是它的`get`再次被调用的原因。
3.  但是在函数绑定之前，`isDefining`被设置为 true，所以在 *if-condition* 下的代码不会被执行，直到`isDefining`再次被设置回 false。

但是这种方式真的很讨厌。因为每做一个`Medium`的新实例，都要再做一次这个。

我们不能用更优雅的方式吗？

# 和一个装潢师

任何函数都可以是装饰器。基本上，你可以为一个类或者类中的一个方法使用装饰器。它有三个参数——*目标*、*值*和*描述符*。

```
function decorator(target, value, descriptor) {}
```

1.  `target`指该类或该类的原型。
2.  `value`是类的`undefined`，是方法的方法名。
3.  `descriptor`是一个包含对象上可定义属性的对象——比如*可配置*、*可写*、*可枚举*和*值*。一节课就`undefined`了。

decorator 与 at 符号(`@`)一起使用，带有您将用作 decorator 的函数的名称——正如我刚才解释的，它需要三个参数。

`descriptor.value`是您放置装饰函数的函数名——在本例中，它是`getWriter`本身。

注意，`autobind`的返回值是一个新对象，那么`getWriter`将返回值应用到它的环境中。

使用 decorators 的好处在于它们是可重用的。在定义装饰函数之后，你需要做的只是在函数上写`@autobind`。

下面是另一个将类成员属性设为只读的例子，这甚至更简单。

这一次，通过将`writable`属性设置为`false`来更改属性的描述符，如此而已。非常简单。对吗？

# 完整代码比较

下面是完整代码的对比。

**不带**装饰器

**用**装饰器

自己试试吧！

# 结论

装饰师是非常有用的，强大的，令人惊讶的，非凡的。老实说，我看不出有任何理由拒绝使用这个令人敬畏的功能。

但是，请记住，它仍然处于第二阶段，我在这篇文章中使用的方式更像巴别尔的风格，而不是目前提出的第二阶段。所以，事情可能会有所不同，比如如何使用它或者你实际上可以用它做什么。

所以，我绝对推荐你在你的项目中使用这个特性和合适的 Babel 配置，但是我也想提到在 TC39 中关注这个特性。

# 资源

*   [TC39](https://github.com/tc39/proposal-decorators)中的装饰提案
*   [MDN 中的 object . define property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
*   [Github 上的 auto bind-decorator](https://github.com/andreypopp/autobind-decorator/blob/master/src/index.js)