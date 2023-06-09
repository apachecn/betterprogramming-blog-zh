# 今天改进代码的技巧

> 原文：<https://betterprogramming.pub/tips-to-improve-your-code-today-2c70b6af7a76>

## 希望在 2020 年成为职业选手的初学者的后续步骤

![](img/387559a764843b7b883b51012d95abd7.png)

萨法尔·萨法罗夫在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

编码是一项需要改进的技能，你不是天生的程序员。只是人们投入了成千上万个小时来提高这一技能。

如果你只是在朝九晚五的工作时间编码，你就不能指望成为一名伟大的程序员。如果你是一个吉他手，你唯一的时间是在音乐会上演奏，你将无法找到新的方法来为你的乐队增加价值。

同样，作为一名程序员，你总是需要找到把事情做得更好的方法，我相信这就是为什么你会在 Better Programming，所以让我们开始吧。

# 结对编程

这个真的很酷。

根据维基百科:

> 结对编程是一种敏捷的软件开发技术，其中两个程序员在一个工作站上一起工作。一个是驱动程序，编写代码，而另一个是观察者或导航者，在输入代码时检查每一行代码。这两个程序员经常交换角色。

你的同事和你都可以互相学习，你可能知道优化事情的方法，而你的同事现在可能知道如何编写更干净的代码，这样，你们都可以互补彼此的技能，完成工作，并互相学习。是不是很酷？

一开始可能会很尴尬，但很有趣，但找到你想配对的人，你不必和每个人都这样。

# 构建项目

这很有趣。

为一切构建一个应用程序！

建立 Twitter，Instagram，Tinder，甚至 Medium，这完全取决于你。

通过这种方式，你将学会如何实现某些复杂的功能，并更好地理解在我们都喜欢的真实应用程序中事情是如何完成的。

# 尝试不同的编程工具

你的编程语言也是一种工具。

乐于尝试你那个时代最热门的编程语言。

用你正在尝试的新编程语言构建相同的应用，无论是 Ruby、 [Go](https://golang.org/) 、Python 还是 [Clojure](https://clojure.org/) 。这样，你可以体验各种语言的优缺点，并在需要时支持它们。

这将是你作为一名优秀程序员的最大优势，也是你可以随着时间的推移而发展的优势。

# 努力提高代码可读性

作为初学者，这是你应该明确的目标。它不需要太多技能，回报也不错。

我们，作为初学者，试图跳入编写最高性能的代码，即使不知道所有将帮助我们实现同样目标的工具。

你打算编写最有效的程序是好的，但是编写更简洁的代码将帮助你提高作为程序员和你的团队的效率，并且不需要太多的努力。

不要缩写变量、函数或类名。使用长的、描述性的名字，清楚地解释你想从函数中得到什么。

下面是一些提示，你可以马上实施来改进你的代码。

# 小心评论

```
int b = 5;
int c = 4;// I am sure you are reading about comments after a long time
// the more comments I write here,
// the longer it will take for you 
// to read this codeblock and make sense of it.
// BTW, it's just a simple addition.int a = b + c;
```

我们有时倾向于注释掉代码，认为:“我以后可能会回来使用它”，而大多数时候，你不会回来。这些代码一直困扰着它的受害者。

当其他人正在阅读你的代码时，那些被注释掉的代码不知从哪里冒出来，*降低了读者的速度。*读者/程序员不能认为它很重要就删除它，这也是它存在的原因。

如果评论具有误导性，那就更糟了！为了了解更多，这里有一个关于评论的[有趣故事](https://medium.com/@devDeeJay/dont-comment-your-code-rewrite-it-a145d655f87b)，值得你花时间去了解。

# 只做对一件事

这里，读这个函数。

```
int addTwoNumbers(int a, int b) {
    **displayToUser**("Adding two numbers");
    **setFontColorTo**(Colors.RED);
    return a + b;
}
```

函数名很无辜，`addTwoNumbers`，而当你读到这个名字的时候，想到了什么？

它应该只是将两个数字相加，但是当你看到它的实现时，它所做的不仅仅是将两个数字相加，这两个数字一开始对用户是隐藏的。

这是我们在编写代码时经常犯的错误。函数式编程教*原子函数*做一件事，而且只做一件事。

这样，你对你的代码非常清楚，不会给错误或意外留下任何空间。了解 SRP(单一责任原则)，它是更干净代码的核心。

当你在为单一的责任而奋斗时，你的职责会变得很短，这正是它们应该有的长度！

# 函数参数

你知道函数应该有多长，但是自变量呢？

数字越少越好。*零是理想的。*

更大数量的实参/参数会使您的代码在多个方面效率低下:

*   理解函数需要更长的时间。
*   变得更难测试。
*   更有可能把事情搞砸。
*   违反 SRP 的几率更高。

悲伤却真实。

# 简化条件语句

你告诉我，哪个更好读？

```
if(person.age > 60 && person.salary > 5000 && person.score > 300 && person.score < 600 && person.retiredYear > 2014) {
    enrollForRetirementPension();
}
```

和...相对

```
if(**isPersonEligibleForPension**(person)) {
    enrollForRetirementPension();
}boolean **isPersonEligibleForPension**(Person person) {
    return (person.age > 60 && person.salary > 5000 && person.score > 300 && person.score < 600 && person.retiredYear > 2014);
}
```

如果函数名对你来说很清楚，你就不必浪费时间去阅读实现，从而提高你的效率，使你的代码非常干净。

因此，将复杂的条件重构为一个函数，并给它一个描述性的名称。干净简单。

# 变量声明

声明它们离使用它们的地方很近。

我们中的一些人(包括 ex-me)可能有在类/文件的顶部声明所有变量名的习惯，即使它可能只在一个地方使用。

如果你像这样声明变量，那么你的类的所有方法都可以访问它们，并且可以修改它们的值，因此增加了一些 bug 的可能性。

因此，只要试着在变量被使用的地方声明变量就行了。

# 那都是乡亲们！

来自我最喜欢的人之一——吉姆·奎克。

> 知识不是力量，而是潜在的力量

因此，尝试实施这些学习，并利用权力。

如果这个故事对你有价值，请告诉我。

![](img/4c96705d54e8fd820a816b50d8c8c190.png)