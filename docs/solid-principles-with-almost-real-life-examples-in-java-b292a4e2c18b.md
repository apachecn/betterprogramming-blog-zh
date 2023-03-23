# 坚实的原则和(几乎)真实的 Java 实例

> 原文：<https://betterprogramming.pub/solid-principles-with-almost-real-life-examples-in-java-b292a4e2c18b>

## 对 5 个坚实原则的不同看法

![](img/4f3444c7ffcd3df249831f381c0dcac2.png)

Adobe 股票图像

坚实的原则是软件世界中一些最古老的规则。它们使我们能够编写可维护、可读、可重用的代码。在这篇文章中，我试图完成一个有点真实的例子，遵守坚实的原则。

# 1.单一责任

每个类应该只有一个唯一的目的，不要充满过多的功能。考虑下面的例子:

顾名思义，这个类是为散列密码而实现的。它不应该负责将它们保存到数据库中。每个班级都应该有一个要完成的责任。

不应该有“[god classes”](https://herownhelloworld.medium.com/heres-all-one-should-know-about-god-class-in-java-e318acbb9717)有各种各样的功能，有太多的事情要完成。相反，我们应该尽可能模块化地编写我们的类。在另一个类中实现保存操作。

# 2.开闭原理

类应该对扩展开放，对修改关闭。

换句话说，您不应该为了实现新特性而重写现有的类。

让我们继续我们的密码哈希例子。假设我们希望我们的类能够使用多种算法选项进行哈希运算。

如果我们用这种方式实现，我们会把 O 破坏得很糟糕。每实现一个新的算法，我们都需要修改现有的类，看起来很难看。

感谢 OOP，我们有了抽象。我们应该使我们的初始类成为一个接口/抽象类，并在具体的类中实现算法。

这些类的排序可能会很奇怪，但不幸的是，当把它们放在一起时，gist 是按字母顺序排序的。

这样，我们可以在不触及现有代码库的情况下添加新算法。

# 3.利斯科夫替代原理

子类应该能够实现其父类的每个特性，并且可以被视为其父类。

为了演示我们的例子，让我们创建模型(数据)类来使用我们的散列算法。

我们为其他编码实现了同样的功能…

为了满足 Liskov 规则，Hashed 的每个其他扩展都应该使用散列函数的有效实现并返回一个散列。

例如，如果我们使用一个名为“NoHash”的类来扩展 Hashed 类，该类使用一个返回完全相同的密码而没有任何编码的实现，这将违反规则，因为 Hashed 的一个子类应该具有密码的散列值。

# 4.界面分离原理

接口不应该强迫类实现它们不能做的事情。大的接口要分成小的。

考虑我们给接口增加解码功能。

这将打破这条定律，因为我们的算法之一 SHA256 实际上是不可解密的(它是一个单向函数)。相反，我们可以向适用的类添加另一个接口来实现它们的解码算法。

# 5.从属倒置原则

组件应该依赖于抽象，而不是具体化。

我们有如下的密码服务:

我们违反了原则，因为我们将`Base64Hasher`和`PasswordService`紧密耦合。

让我们将它们解耦，让客户端注入构造函数所需的哈希服务。

好多了。我们可以很容易地改变散列算法。我们的服务不关心算法，由客户选择。我们不依赖于具体的实现，而是依赖于抽象。

## 参考

https://www.baeldung.com/solid-principles

[https://www . digital ocean . com/community/conceptual _ articles/s-o-l-I-d-first-five-principles-of-object-oriented-design](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)