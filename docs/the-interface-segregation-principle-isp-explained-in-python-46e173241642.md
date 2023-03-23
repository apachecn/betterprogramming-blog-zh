# Python 中解释的接口分离原则(ISP)

> 原文：<https://betterprogramming.pub/the-interface-segregation-principle-isp-explained-in-python-46e173241642>

## 看看接口以及如何用坚实的原则 4/5 编写干净和可维护的代码。

![](img/aa7c22a9cdd7312131f80c383dc9f5c5.png)

伊恩·帕克 / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 摄影

这篇文章是关于[坚实](https://en.wikipedia.org/wiki/SOLID)原则系列文章的第 4 部分。
你可以在这里找到 3 号岗位[，在这里](/the-liskov-substitution-principle-lsp-explained-in-python-6ab92b29d0b8)找到 2 号岗位[，在这里](/the-open-closed-principle-explained-in-python-f5517488f990)找到 1 号岗位[。](/the-single-responsibility-principle-explained-in-python-622e2d996d86)

在这篇文章中，我们来看看 **SOLID** 缩写中的 **I-** ，它代表接口分离原则。我们从一个定义开始:

> 不应该强迫客户依赖他们不使用的方法。
> 来源:[敏捷软件开发；罗伯特·马丁；](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwizz73Eu8v2AhWGr4sKHWc7D7kQFnoECAQQAQ&url=http%3A%2F%2Fstg-tud.github.io%2Fsedc%2FLecture%2Fws16-17%2F3.2-ISP.pdf&usg=AOvVaw3NunCPzJ05HTwWHeRekwRB)

基本上，这个原则类似于[单一责任原则](/the-single-responsibility-principle-explained-in-python-622e2d996d86)，在这个意义上，类应该只实现它们实际使用的方法。

假设我们有一个实现基类中所有方法的子类。但是对于一个特定的方法，函数体会引发一个异常，因为我们根本不想让这个方法被调用。那个特定的方法会违背类的*行为*定义，技术上不应该实现。这将违反 ISP 原则，需要重构。

你可能会想为什么子类要实现一个它不需要的方法？你看，这个原理处理的是接口。当一个接口被继承时，所有存在的方法都必须被实现。

在继续下一步之前，让我们快速看一下接口和 duck typing。

## Python 中的接口、抽象类和鸭子类型

接口是一种软件构造，它定义并强制一个类的对象应该拥有什么方法。在 Python 中，我们利用 [duck typing](https://en.wikipedia.org/wiki/Duck_typing) 来创建一个接口。我们简单地用一个抽象类来表示一个接口。Duck typing 声明:

> 如果它走路像鸭子，叫声像鸭子，那么它一定是鸭子
> 来源:[鸭子打字—维基百科](https://en.wikipedia.org/wiki/Duck_typing#Example)

换句话说，如果我们定义的类*的行为*像一个接口，我们可以像使用一个接口一样使用它。如果你没有完全掌握这一点，也不要担心。这里的关键点是，我们将抽象类视为一个接口(通过空方法体和声明方法抽象，我们将在后面看到这一点)。
这是给你的鸭子🦆

别再说鸭子了。是时候写点代码了👩🏾‍💻

## 密码

接口可以被认为是一个完全抽象的类。因此，我们用`@abstractmethod`注释抽象类中的所有方法。

我们再次重用前一篇文章的场景，考虑一辆汽车🙂

我们定义一个抽象基类`Car`。这也可以被认为是一个接口，因为这三个方法都被定义为抽象的。

接下来，我们创建两个子类，即`RegularCar`和`SportsCar`。这两个类继承了`Car`中定义的方法。但是请注意，`RegularCar`没有 turbo，所以如果在`RegularCar`对象上调用`turboAccelerate`，我们会引发一个异常。

我们可以测试`__main__`函数中的代码，并看到对`turboAccelerate`的调用确实引发了异常。其他一切都像预期的那样工作。

现在你可能会想，如果不打算使用`RegularCar`函数，为什么要为它定义`turboAccelerate`函数。没错。这就是 ISP 的原理。如果不支持的话，我们不应该强迫一个`RegularCar`去实现`turboAccelerate`。从本质上说，这是我们车型的设计缺陷。

另外，第二个问题是代码维护。假设我们需要在`turboAccelerate`函数中改变一些东西。假设我们需要在函数定义中包含一个额外的参数。如果我们对接口做了这样的修改，我们也必须对`RegularCar`做修改，即使函数只是抛出了一个异常！换句话说，我们被迫做出无用的改变*只是为了让*代码编译。

修复非常简单。我相信你已经看到了😎

## 解决办法

以下是代码的重构:

所以。我们的目标是将`turboAccelerate`从`RegularCar`中移除。但是我们不能这样做，因为接口包含方法，并且接口中的所有方法都必须由子类实现。所以，解决的办法就是做两个接口。一个叫`NoTurbo`，另一个叫`WithTurbo`。
我们只是让`turboAccelerate`成为`WithTurbo`中的函数，而不是`NoTurbo`中的函数。普通车实现`NoTurbo`而涡轮车实现`WithTurbo`。

问题解决了！

您可能已经注意到，我们并没有创建两个独立的接口。相反，我们用`WithTurbo`(继承)扩展了`NoTurbo`。
这样做只是为了减少代码冗余，因为`WithTurbo`中只有一个额外的函数。

太好了！正如你现在看到的，`RegularCar`和`SportsCar`没有实现任何他们不需要的无用功能。

## 包裹

在这篇文章中，你学习了接口分离原则。基本上，这个原则声明一个类不应该被强制实现它不使用的函数。

如同所有坚实的原则一样，ISP 有助于使代码更易于管理和健壮。尤其是在必须对接口进行更改，并向下传播到实现它的所有子类的情况下。如果没有 ISP，被更新的功能也必须在子类中更新，即使它们没有实现任何业务逻辑。

这个帖子到此为止。希望你学到了一些有价值的东西！

下周，我们将到达终点，看看最后的坚实的原则。
敬请期待！

*原载于*[*https://haseebkamal.com*](https://haseebkamal.com/p/5bb14e35-827c-46fb-9e6a-a8a3631c7a12/)*。*