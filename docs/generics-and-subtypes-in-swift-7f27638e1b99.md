# Swift 中的类属和子类型

> 原文：<https://betterprogramming.pub/generics-and-subtypes-in-swift-7f27638e1b99>

## 协变性和不变性之间的斗争

![](img/385d2647f8d6ac047360ed6b5585dda4.png)

[Fabian Grohs](https://unsplash.com/@grohsfabian?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

你尝试过混合使用泛型和子类型吗？通过本文充分利用多态性。

我们都爱斯威夫特。它是一种年轻而强大的编程语言，但有时当你感觉已经掌握了它，你会开始与它的一些局限性作斗争。在本文中，我想讨论如何将泛型和子类型混合在一起，以及这种方法的局限性。

本文要求至少具备 Swift 中泛型和子类型的基础知识。如果你不知道它们或者只是想回顾一下，你可以查看苹果官方文档中的这些链接:

*   [Swift 中的泛型](https://docs.swift.org/swift-book/LanguageGuide/Generics.html)
*   [继承](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html)

# 泛型的不变性

想象一下，在美好的一天，你醒来，决定要建一个健身 app。完美！让我们通过写一些代码来弄脏我们的手。

首先，我们需要一个类来代表我们的练习。我们可以称之为`Exercise`，我们可以添加两个方法来使它更真实(在这个例子中我们不需要它们):

*   `perform()`开始练习。
*   `stop()`停止了锻炼。

每个实际的练习都需要这些方法的不同实现，所以我们可以为每个方法创建一个子类。比如我想提高我的二头肌，所以我创建了一个`Pushup`类。

正如您在脚本中看到的，当您的代码需要一个超类时，分配一个子类的实例是没问题的，因为这是几乎所有语言中继承的工作方式。

但是现在试着想象我们的应用有一个叫做`Activity`的结构，它是通用的，因为它可以处理其他类——不仅仅是练习。

等等，发生什么事了？XCode 为什么会引发这个错误？

```
Cannot assign value of type Activity<Pushup> to type Activity<Exercise>
```

原因很简单:泛型是不变的。这意味着即使泛型类型包装了子类型，也不会使它成为包装其超类的泛型的子类型。

但是最奇怪的事情还没发生。

# 内置泛型的协方差

还记得`Array`和`Optional`是引擎盖下的泛型吗？如果你不记得了，没关系。你唯一应该知道的是，你可以写`Array<Int>` 而不是`[Int]`，因为后者只是建立在它之上的语法糖。

所以，让我们再写一段代码:

嘿，发生什么事了？为什么编译器没有显示任何错误？这个例子和前面的完全一样，但是有内置的泛型类型！嗯，正是这个原因。

Swift 内置的泛型类型是协变的，其中允许泛型类型是其他泛型的子类型。这也是为什么如果一个函数需要一个`Int?` *，你可以指定一个`Int`。你想过这个问题吗？*

# 从这里去哪里

我希望你能更好地理解泛型和子类型的工作原理，以及它们的局限性。Swift 是一门年轻的语言，需要不变性来处理当前版本的多态性，但希望将来我们可以为泛型类型提供协方差！

我也希望这篇文章能帮助你更多地关注关于这种语言的小细节，我们通常不会注意到这些细节，因为我们已经习惯了。它们对于真正了解幕后发生的事情非常有用。

感谢您的阅读。我希望你喜欢这篇文章！如果你有任何问题让我知道。