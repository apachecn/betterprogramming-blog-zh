# 学习其他技能来转换您的代码

> 原文：<https://betterprogramming.pub/learn-other-skills-to-transform-your-code-34aa4ae72561>

## 用这些不可能的技能打开一个全新的编码世界

![](img/f3f45bdd06f058c10b0ee0c8b538fcbe.png)

约书亚·厄尔在 [Unsplash](https://unsplash.com/s/photos/sunset?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

写伟大的代码不仅仅是一遍又一遍地写代码。看到他们的解决方案并感到惊奇，并不是老人们的专有权利。然而，在我们的行业中，一个普遍的想法是，在我们的代码获得最重要的桂冠之前，一个人必须已经为许多公司工作了几十年:干净的*。*

我专业写代码已经五年了。我离 greybeard 的地盘很远，但我为自己在那段时间里构建的许多解决方案感到自豪。以下是在我写这些解决方案的过程中帮助我的基本技能。有些并不明显，但它们都对我的工程生涯产生了深远的影响。

# 学习五种不同的编程语言

如果你想写出伟大的 [Java](https://www.oracle.com/technetwork/java/index-138747.html) ，你只需要学习 Java。如果你想写伟大的代码，你需要学习更多。这是有区别的，而且很微妙。不同的语言提供了不同的视角，但它们都试图实现相同的结果:让计算机做一件事。

当您编写 Java 时，诱惑往往是:

*   用一个物体来表示问题
*   将一些状态和功能附加到对象上
*   创建利用该对象的类，并一起工作来解决问题

这些模式是编写优秀 Java 的典型方式。但是如果您想有效地解决这个问题，您需要的不仅仅是 Java 方法。例如，如果你要学习 Golang，你可能会:

*   用数据创建一个简单的结构
*   将你的功能分解成一系列可组合的、无状态的功能
*   创建一个应用程序，将对这些函数的一系列调用联系在一起

## 同样的问题，不同的想法

不同的语言迫使你训练不同的想法，就像健身房里不同的运动或机器使用不同的肌肉群一样。如果你想健身，你需要做广泛的活动。如果你想写优雅的代码，你需要练习许多不同的技巧。

我见过有人痴迷于 Java 范式，这导致了这样的畸形:

```
private void doAThing() {
  AbstractFactoryBuilder afb = AbstractFactoryBuilder.instance();
  Factory f = afb.buildFactory(config);
  Object o = f.build();
}
```

当我看到这样的代码时，我想知道是否有更好的方法，而且经常有。

## 为什么是五个？

我必须承认，这个数字有点乱。如果你时间不够就学三个，如果你真的很投入就学十个。想法是看到新的地平线。如果你是 Java 专业人士，学一点儿 Clojure 或 T2 Haskell。如果你是一个 Javascript 专家，试着用一点 Ruby。一旦你有了五个，你就已经看到了很多不同的解决问题的哲学，你的头脑也拥有了很好的理解技巧。

# 练习写作

这个看起来有点奇怪，但是听我说完。我们将从 Martin Fowler 关于你的代码的目标受众的一句简单的话开始:

> “任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”

当你写代码时，你不是在解决问题。你在向编译器描述如何着手解决这个问题。当您部署了代码，并且它为目标用户正确运行时，问题就解决了。

## 而这种描述往往是缺乏的…

这些年来我收到的一些最好的写作建议直接适用于我的代码。

*   不要使用不必要的复杂语言。
*   不要使用复杂的句子结构；保持事情简单。
*   在你说之前，确保你明白你想说什么。
*   写多份草稿，并确保定期检查你的工作。

甚至是看似具体的建议，比如“把你的文章分成几个段落”以前听过吗？见过这样的函数吗？

```
private void doAThing() {
  System.*out*.println("Initial log");

  List<String> words = Arrays.*asList*("hello", "world", "something", "is", "in", "here");

  for(String s : words) {
    System.*out*.println("I am doing something with this word");
    System.*out*.println(s);

    doSomething(s);
    doSomethingElse(s);
    thenDoSomething(s);
    finallyDoThis(s)
  } for(String s : words) {
    System.*out*.println("I am doing something with this word");
    System.*out*.println(s); doSomeOtherStuff(s);
    andSomeMoreStuff(s);
    // This is the last but not the last
    okayLastThingIPromise(s);
    finallyDoThis(s);
  }
}
```

在没有任何代码知识的情况下，通过一些基本的写作建议，可以无限地改进这些代码。

*   把它分成更小的段落(方法)。
*   确保你明白你想说什么(即，优雅地写出解决方案)。
*   不要重复你自己(例如，为什么像这样在相同的数据上有两个循环？).
*   把你的想法说清楚(比如“持续但不持续”是什么意思！？).

福勒的建议一次又一次地被证明是正确的。我见过很多很多工程师绞尽脑汁让他们的软件屈服，用混乱和笨拙的代码花几个小时来解开。真正的大师，即感觉派，写得简单而有效。我们如何培养这种技能？

## 补救措施

学一点写字。阅读一些基础知识，比如[风格要素](https://www.amazon.com/Elements-Style-William-Strunk-Jr/dp/194564401X):简单明了的书籍，旨在提高你的散文水平。这样，你就学会了如何更有效地交流你的书面想法，避免了许多在你之前的工程师的明显缺陷。

然后，开个博客！关于技术或文学、假期或冲突的博客——任何你内心渴望的东西。熟能生巧，你会培养出一种敏锐的感觉，即简洁明了的描述能充分准确地反映你的精彩想法。提醒你，没人需要读它。这只是为了让你尝试一些你学到的新技能。

![](img/f169a57eed61b84f042bb198da03edaf.png)

任何有抱负的作家的主要读物

记住，编码是向编译器和你的工程师同事表达思想的行为。不要偷工减料，永远不要低估你职业中的创造性。

# 不要超载的话

您知道吗,`Order`可能表示客户想要支付的一组商品，或者它可能代表列表的某种排序逻辑，或者它可能是一个数字的幂。当有人来阅读你的代码时，他们会带着自己所有的行李，作为一个好的作者，你的工作是确保你使用的语言反映了你的意思。

```
private void printAThing(String a) {
  System.*out*.println("I am printing: " + a);
  sendToPrinter(a);
  System.*out*.println("I am writing in print: " + a.toUpperCase());
}
```

在这种情况下，打印可能意味着将它写到屏幕上，在纸上创建一个物理副本，或者全部用大写字母书写。对于双重含义来说，英语是一种糟糕的语言。

## 开发一个词汇表

这是[领域驱动设计](https://dddcommunity.org/learning-ddd/what_is_ddd/) (DDD)的核心组成部分。当你跳过你的行业摆在你面前的问题时，一种新的语言将开始形成。这是自然发生的——我们不能一直覆盖同一领域。然而，如果没有人跟踪这种新语言，这可能会变得无法控制。

DDD 提倡建立一个专门针对你的组织或领域的术语表。例如，`Order`总是一个客户想要的商品集合。这份文件创造了一个承诺，即`Order`只意味着一件事，没有其他意义。

# 不要成为狂热分子

该行业经常处于现状和颠覆性之间的战斗中，新的想法正在被引入。科技发展速度惊人。今天一个叛逆的概念，明天就会成为久经考验的最佳实践。

这种思想的流动意味着一件事。把你的自我放在这些原则中是停滞和无意义争论的秘诀。简单来说，不做。相反，把每个范例都看作是你腰带上的一个工具。

![](img/f54ba485f5c9cbb0cf4ec0793fefd6fb.png)

汉斯-彼得·高斯特在 [Unsplash](https://unsplash.com/s/photos/jigsaw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果您有一个需要转换大量数据的应用程序，函数式编程技术将是您的好朋友。如果有许多领域逻辑融入了各种概念，您可能希望在解决方案中混合一些面向对象的元素。这些方法并不互相排斥，一个伟大的程序员在必要的时候会适当地使用每一种方法。

# 测试，测试，测试

这个我相信你已经知道了。测试不仅仅是一种确保代码工作的机制。它不仅仅是一个安全网，你可以在上面安全地重构。测试提供了另一个层次的信心:你的代码是可测试的。

![](img/baa5d1c0aedb883123a0291c3287395e.png)

在 [Unsplash](https://unsplash.com/s/photos/testing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[科学高清](https://unsplash.com/@scienceinhd?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

## TDD 并不总是答案

这通常是人们提倡[测试驱动开发](http://agiledata.org/essays/tdd.html) (TDD)的地方，这是使用测试来帮助改进代码设计的实践，导致高度可测试的结构。我不会那么做的。TDD 很棒，如果你想要一种创建高度可测试代码的机制，那就去做吧。

同样，坐下来，拿起纸和笔，起草你的解决方案，寻找可以模仿的地方，决定你的系统的边界在哪里，也会给你一个非常清晰的层次。TDD 不是唯一的游戏，不要相信任何人告诉你的其他事情。见上:不要做狂热者。