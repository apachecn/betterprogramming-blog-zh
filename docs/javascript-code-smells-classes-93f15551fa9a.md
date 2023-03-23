# JavaScript 类中的代码味道

> 原文：<https://betterprogramming.pub/javascript-code-smells-classes-93f15551fa9a>

## 如何去掉无用的代码，让我们的代码结构更简单

![](img/10c11fd47d0c973242bfb458899e1390.png)

由 [Rich Tervet](https://unsplash.com/@richtervet?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在编程中，代码气味是一段代码的特征，表明可能有更深层次的问题。这是一种主观特征，用于通过查看代码来判断代码是否具有良好的质量。

在这篇文章中，我们将看到一些 JavaScript 类的代码味道，包括懒惰的类，过度使用文字，太多的条件和循环，孤立的变量和常量，以及使用太多的变量而不是对象。

# 懒惰或吃白食类

懒惰或吃白食的类是做得太少的类。如果它没什么用，可能就不应该添加，因为它基本上没什么用。

我们应该想办法把懒惰类中的东西放到有更多东西的地方。只有一两个方法的类可能没什么用。

# 文字的过度使用

过多地使用文字不是一个好主意，因为重复使用它们会带来更多出错的机会。这是因为当我们改变代码时，如果它们太多，我们必须改变它们中的每一个。

文字应该外化到它们自己的文件和脚本中。尽可能将更多的动态数据存储在数据库中。如果需要，这使得本地化变得容易。

例如，如果我们需要在代码中的多个位置放置 Medium 的 URL，我们应该在 URL 中有一个常量，而不是在多个位置重复它。

所以与其说:

```
const mediumRequest = fetch('[http://medium.com'](http://medium.com'));
//...
const mediumJsUrl = '[https://medium.com/topic/javascript](https://medium.com/topic/javascript)';
```

我们应该改为写:

```
const MEDIUM_URL = '[http://medium.com'](http://medium.com');
const mediumRequest = fetch(MEDIUM_URL)
//.,.
const mediumJsUrl = `${MEDIUM_URL}/topic/javascript`;
```

这样更好，因为我们没有在多个地方重复`[https://medium.com](https://medium.com/topic/javascript)`。做出改变变得更加容易。

# 圈复杂度

圈复杂度意味着我们的代码中有太多的条件语句和循环。

复杂性可能以不同的方式出现。循环和条件可能嵌套太深。两层以上的嵌套可能太多了，很难阅读。

也可能有太多没有嵌套的条件和循环。

例如，不要写成这样:

我们应该改为写:

我们消除了嵌套，将一些深度嵌套的代码移到了它自己的函数中。这增加了可读性，并且将代码分离到它自己的函数中使得测试更容易。

此外，除了`return`之外，使用循环控制语句——如`continue`和`break`——有助于控制代码流，而不用在内部嵌套很多行的条件语句。

# 孤立的变量或常量类

这些类拥有一组属于其他地方的常量，而不是属于它们自己的类。

这需要改变，因为把它们放在不用的地方是没有意义的。对任何阅读代码的人来说都不直观。

例如，如果我们有一个包含以下变量的类，我们有以下几个类:

那么`Color`类中的`'red'`更适合于`Apple`类，因为我们只将它用于`Apple`实例。

所以我们可以写:

```
class Apple {
  constructor() {
    this.color = 'red';
  }
}
```

也不用费心去上`Color`课。

![](img/c73444315866d1488efbbf968e8e3fdd.png)

照片由[戴安娜·斯帕塔留](https://unsplash.com/@dianaspatariu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 数据块

数据块是指在程序的不同部分有太多的变量一起传递的情况。这意味着我们应该将这些组合成它们自己的对象，并一起传递它们。

例如，如果我们有一堆变量，我们传递给一个函数，如下所示:

我们应该重写它，这样所有的变量都在一个对象中，并改变函数的签名来接受这个对象。我们可以看到，有六个参数，太多了。在大多数情况下，对于一个函数来说，五个以上的参数可能太多了。

此外，我们必须写出所有的变量，它们是相关的，所以我们可以将它们分组到字段中，并引用整个对象，而不是单独引用每个变量。

这降低了丢失变量引用的可能性，因为将变量分组到一个对象中意味着我们只需要处理一个变量，而不是六个。

我们也重复了很多次`fruit`这个词。

我们可以通过编写以下代码来消除所有的重复，并减少我们必须处理的变量和参数的数量:

现在，我们只需要处理一个对象和参数，而不是几个变量和参数。这样看起来更舒服，也更有条理。此外，我们很难忘记引用代码中的一些变量，因为我们只需要处理一个变量。

# 结论

我们不应该有做很少或孤立变量和常数的类。此外，太多的循环和条件——尤其是嵌套太深的情况——很难阅读和理解。

如果我们有许多相互关联的变量，那么它们应该被分组到对象中，以便于引用和处理。