# 为什么我仍然不喜欢 JavaScript 中的类

> 原文：<https://betterprogramming.pub/why-i-still-dont-like-classes-in-javascript-994132f914c0>

## 这个参数、类的隐私只是几个陷阱。工厂函数可能是更好的选择。

![](img/14042b754908bda83f9dff9b5c5c8e91.png)

作者照片。

几年前，我写过一篇文章，指出了我不喜欢 JavaScript 中的类，而更喜欢使用工厂函数的原因。三年后，我仍然可以说我不喜欢 JavaScript 中的类，我只是在别无选择的情况下才使用它们。

好消息是许多与课程相关的问题已经解决了。这很好，因为仍然有应用程序在使用它们，所以现在需要担心的事情更少了。

# 班级隐私

默认情况下，类属性是公共的。这意味着我们可以访问和修改用类创建的[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)的任何属性。然而，散列(`#`)前缀现在允许我们定义私有属性。

`#`是属性名的一部分。它用于声明和访问这样的字段。

私有字段只能在[类](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)中访问。下面是一个封装了`count`字段的`Counter`类的例子:

一旦[对象](/did-you-know-that-almost-everything-is-an-object-in-javascript-f06c3f69faf1)被创建，内部的`#count`可以仅使用公共方法来改变。

内部私有`#count`字段不能直接修改。

```
stack.#count = 0;
//Private field '#count' must be declared in an enclosing class
```

# 方法内部的内部函数

正如您可能已经知道的，在方法内部定义的内部函数中有一些关于`this`参数的问题。

`this`取决于调用函数的方式，而不是函数的定义位置。例如，如果我们使用函数表达式定义回调，这就会产生问题。

查看下面的`print`方法:

作为回调发送给`setTimeout`实用程序的匿名函数作为一个函数被调用，并且不指向与`print`方法相同的对象。

很高兴我们可以通过使用 arrow 语法创建内部函数来避免这些问题。

箭头函数没有自己的`this`参数。这意味着箭头函数使用父方法中的`this`参数。

然而，一些问题仍然存在。

# 方法可以被重新定义

私有字段不能从[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)之外进行修改，但是公共方法可以修改。

例如，我们可以从客户端代码重新定义公共方法。下面是一个改变`increment`方法的例子。新的`increment`方法没有任何作用:

# 这个参数可以改变上下文

由于`this`参数的工作方式，仍然有边缘情况会导致问题。

下面是一个使用析构赋值语法的例子。因为`increment`现在作为一个函数被调用，`this`不再引用我们的对象，我们得到一个错误:

```
const { increment, decrement } = new Counter();
increment();
//Cannot read property '#count' of undefined at increment
```

下面是另一个例子，其中`increment`作为一个函数而不是方法被调用。由于这个原因，`this`没有引用我们的对象，我们收到一个错误:

```
const counter = new Counter();
setTimeout(counter.increment, 0);
//Cannot read private member #count from an object whose class did not declare it
```

为了避免前面的问题，我们需要确保`increment`作为一个方法被调用:

```
const counter = new Counter();
setTimeout(() => counter.increment(), 0);
```

# 工厂功能

工厂函数提供了构建[对象](https://medium.com/dailyjs/15-fundamentals-you-should-know-on-javascript-objects-90f57cc9d78d)的替代方法，类似于用类创建的对象，但是没有与`this`相关的问题，并且具有冻结的接口。

这个概念是由道格拉斯·克洛克福特首创的。这是它的样子:

`Counter`是一个函数，返回一个只包含函数的冻结对象。

在 JavaScript 中，函数可以在其他函数中定义。内部函数可以从父函数中访问变量。即使在父函数执行之后，它们也可以访问这些变量。从父函数访问变量的内部函数被称为[闭包](https://medium.com/programming-essentials/you-will-finally-understand-what-closure-is-13ba11825319)。

`increment`和`decrement`是闭包。它们都从外部函数`Counter`访问同一个变量`count`。

我们冻结返回的对象，这样客户端代码就不能改变它的方法。

我们甚至可以用`new`操作符:`new Counter()`调用工厂函数。当像函数`Counter()`一样被调用时，它仍然返回相同的对象:

```
const counter = new Counter();
```

`count`变量是私有的。不能从对象外部直接修改它:

```
const counter = Counter();
counter.count = 0;
//Cannot add property count, object is not extensible
```

客户端代码不能像以前使用[类](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)构建对象时那样重新定义对象的公共方法:

```
const counter = new Counter();
counter.increment = function(){};
//Cannot assign to read only property 'increment' of object
```

当析构这些方法并像调用函数一样调用它们时没有问题:

```
const {increment, decrement } = Counter();
increment();
//1
```

当方法作为回调发送并像函数一样被调用时，没有任何问题:

```
const counter = new Counter();
setTimeout(counter.increment, 0);
```

# 最后的想法

对类行为进行了改进。使用`#`前缀我们不可能有隐私。我们可以通过使用箭头函数来避免方法中的`this`参数的问题。

然而，当事情不像预期的那样工作时，我们仍然会遇到令人困惑的问题。由于这个原因，我使用工厂函数。当创建数千个使用工厂函数而不是类似的类构建的对象时，会有内存损失，但是在应用程序中只有少数对象是使用工厂函数创建的——而不是数千个。在这种情况下，差别是微不足道的。以这种方式创建的对象具有私密性，并且没有与`this`参数相关的问题，原因很简单，因为根本没有使用`this`。

如果我们从 JavaScript 语言中移除关键字`this`会发生什么，你可以看看没有“this”的 [JavaScript 看起来像一种更好的函数式编程语言](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)。

感谢阅读。