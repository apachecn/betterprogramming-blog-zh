# 9 个可以让任何 Java 开发人员尖叫的 JavaScript 特性

> 原文：<https://betterprogramming.pub/9-javascript-features-that-can-make-any-java-developer-scream-e4160a044fc8>

## 这个参数，对象是哈希映射，对象从对象继承，var 没有块作用域等等

![](img/763f7182033d4bc0147407ca147c163f.png)

Ryan Snaadt 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 和 JavaScript 有时会被混淆，尤其是招聘人员，但它们当然是不同的语言。JavaScript 最初是 Java 的愚蠢小弟，但现在情况不同了。对不对？

让我们找出 JavaScript 的九个特性，它们会让一个 Java 开发人员非常困惑。

# 1.函数不一定是方法

在 Java 中，函数在大多数情况下是方法，并且是在类内部定义的。即使不是方法的匿名函数也必须在类内部定义。

在 JavaScript 中却不是这样。[函数](/10-fundamentals-you-need-to-know-about-functions-in-javascript-8e74579b7a9a)不一定是方法。功能可以是独立的单元；它们不必在一个类中定义。

JavaScript 中的函数通常是在模块中创建的。一个模块基本上就是一个文件。为了使一个函数对其他模块可用，它必须被导出。下面是一些代码:

要使用其他模块中的函数，我们必须首先导入它。

`removeDuplicates`函数不是对象上的方法。它只是一个可重用的独立函数。

# 2.这不一定指向当前对象

在 Java 中，`this`关键字总是指向当前的[对象](https://medium.com/programming-essentials/7-things-you-should-know-about-objects-in-javascript-c0b2f251e160)。

在 JavaScript 中却不是这样，这是许多混乱的来源。函数可以是独立的，但也可以是对象的一部分。当它们是对象的一部分时，它们就变成了方法。在这种情况下，他们需要知道自己属于哪个对象。[这个](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)伪参数对此有所帮助。

混乱来自于这样一个事实，即`this`依赖于函数如何被调用，而不是函数在哪里被定义。也许这是一件微妙的事情，但它很重要。

考虑下面的例子。`show`方法中的`this`参数用于引用同一个类`msg`中的其他成员。

当函数作为方法被调用时，它指向正确的对象。

```
const message = new Message('Hi');
message.show();
//Hi
```

不考虑 1000 毫秒后调用`show`函数的下一个代码。

```
setTimeout(message.show, 1000);
//undefined
```

这次`show`是作为函数调用的，而不是作为方法。[这个](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)在严格模式未启用时指向`undefined`甚至更坏的指向`window`对象。

为了修复[问题](/7-common-javascript-bugs-you-can-now-avoid-27d41c8d81c2)，我们需要将其作为方法调用。

```
setTimeout(() => message.show(), 1000);
//Hi
```

# 3.用 var 声明的变量没有块范围

Java 中的变量无论如何声明都有块作用域。这意味着它们只在创建的块中可用。看看下一个简单的例子:

JavaScript 中用`var`声明的变量没有块范围。

下面是一个在创建变量的块之外访问变量的例子。

更有甚者，用`var`创建的变量被提升到其函数作用域的顶部，所以它们可以在声明前使用。在赋值之前，它们保存未定义的值。

为了避免这种混淆，请使用`const`和`let`声明。

# 4.对象不一定是类的实例，它们实际上更像散列图

Java 中的对象是类的实例。

这与 JavaScript 中的概念不同。这里的对象更像是散列图。

下面是一个不使用任何类创建对象的例子，但是使用了所谓的对象字面语法

# 5.对象从其他对象继承

来自 Java 的你可能习惯于从其他类继承的类。

在 JavaScript 中，对象从称为原型的其他对象继承。

几年前，JavaScript 中加入了类的概念，以使这种原型继承更容易，并为习惯于类模型的开发人员所熟悉。

JavaScript 中的[类](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)在很大程度上是原型系统上的[糖语法](https://medium.com/programming-essentials/these-are-the-features-in-es6-that-you-should-know-1411194c71cb)。在幕后，我们仍然有从其他对象继承的对象。`this`还是要看函数怎么调用等等。

类别会建立新建立的物件所使用的原型。你可以简单地检查一下。

在`Message` 类中定义的所有方法都被添加到了`Message.prototype`对象中。用这个类创建的所有对象实际上都继承自`Message.prototype`。

```
Object.getPrototypeOf(message) === Message.prototype;
//true
```

# 6.变量可以改变它们的类型

在 Java 中，变量总是具有相同的类型。一旦用`var`声明了一个变量并用一个整数初始化，它就永远是一个整数。

变量可以在 JavaScript 中改变它们的类型。

JavaScript 中的值总是具有相同的类型，但是变量可以存储不同类型的值。

这被称为动态类型化。

然而，用`const`声明的变量不能被重新赋值，所以它不能改变它的类型。

# 7.无效的算法或无效的数字转换不会引发异常，但结果是 NaN

在 Java 中，试图将无效字符串转换为数字会引发异常。

```
int no = Integer.parseInt("nine");
//java.lang.NumberFormatException: For input string: "nine"
```

当试图用零除一个整数时，也会发生同样的事情，抛出一个异常。

```
int no = 0/0;
//java.lang.ArithmeticException: / by zero
```

在 JavaScript 中，如果文本不能被解析成整数，结果是`NaN`。不会引发任何异常。当试图将零除以零时，也会发生同样的事情。结果是`NaN`。

记住`NaN`不等于`NaN`。使用`Number.isNaN`实用程序检查值是否为`NaN`。

# 8.函数不能重载

在 Java 中，您可能习惯于用相同的名称但不同的签名(参数的数量、类型和顺序)来声明相同的方法。此外，您可能希望该语言根据您提供的参数选择正确的语言。

下面是一个例子:

在 JavaScript 中，没有所谓的重载系统。你不应该用相同的名字声明两个函数。如果你这样做，语言将使用最后一个。

下面是一个例子:

在这种情况下，使用`sum`函数的最后一个定义。它使用三个参数进行求和，但调用时使用两个参数。当调用一个参数比预期少的函数时，剩余的参数被设置为`undefined`。在`NaN`中有未定义结果的算术。

# 9.函数可以有方法

事实上，在 JavaScript 中函数可以有方法。这怎么可能呢？

在 JavaScript 中，函数实际上是[对象](/did-you-know-that-almost-everything-is-an-object-in-javascript-f06c3f69faf1)，所以像其他对象一样，它们也有方法。

例如，`call`方法可以在所有函数上调用。

如果你是一个 Java 开发者，我可能已经毁了你的这些惊喜。有关 JavaScript 基础知识的更多信息，请查看[了解这些 JavaScript 基础知识，成为更好的开发人员](https://medium.com/programming-essentials/learn-these-javascript-fundamentals-and-become-a-better-developer-2a031a0dc9cf)。

感谢阅读。