# 科特林基本类型:字符串

> 原文：<https://betterprogramming.pub/kotlin-basic-types-strings-acd37b7121e8>

## Kotlin 中弦乐的工作原理

![](img/162db8d152c315b5e2d95672f94bab65.png)

由[卢肯·萨贝拉诺](https://unsplash.com/@luferlex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/android?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Kotlin 有五种基本数据类型。在之前的帖子中，我们讨论了 Kotlin 中的第一个基本类型，[数字](http://www.evanamargain.com/blog/android/kotlin-basic-types-numbers/)。这一次，讨论将与字符串有关。

在 Kotlin 中，像在大多数编程语言中一样，字符串是组成我们通常用作单词的字符组，尽管这不一定是真的，因为字符串可以有一组没有任何意义的字符，但仍然是同一类型。

字符串的语法很简单，只需将字符用双引号括起来，就会得到一个字符串:

```
val string = "Hello, world!"
```

# 字符串的属性

## 1.它们是不可改变的

在编程中，当我们谈到“不变性”时，我们指的是不能改变一个对象。以下面的字符串为例:

```
var myDescription = "I am a programmer"
```

没有办法改变那个字符串，我们没有一个函数可以告诉代码:“在变量的第六个字符后，插入单词 *great* 。”如果你想改变字符串，你需要重新分配它。

```
myDescription = "I am a great programmer"
```

![](img/b707d81bd0b600763c48e9591e8be58b.png)

重要提示:认为上面的陈述是一种变异是初学者常见的困惑——它不是。

上面一行发生的事情是，我们正在“重新分配”变量，这意味着我们正在销毁它以前的值，并为它分配一个新值，这个新值可能是相似的，也可能是完全不同的。

## 2.字符串可以通过索引进行迭代和访问

Kotlin 中的字符串就像数组一样，可以通过零索引逻辑访问它们:

```
var anotherString = "I like programming in Kotlin" anotherString[3] // This would give the character 'i' as a result
```

另外，您可以用一个循环来检查每个字符:

```
for (letter in anotherString) { 
  println(letter) 
}
```

专业提示:以上述方式迭代字符串在算法问题中非常有用，这在编码面试中非常常见。

## 3.字符串可以连接在一起

这不是推荐的做法，但这是 Kotlin 弦乐的一个特点。如果您使用`+`操作符，您可以连接，这与将两个字符串“连接”在一起是一样的。

```
var name = "Evana" 
var lastName = "Margain" var fullName = name + " " + lastname 
// result: "Evana Margain"
```

![](img/f782b43c860a2443a5b9fa269e78826f.png)

现在你应该在想:“这个看起来很有用，为什么不推荐？”

还有其他方法可以达到同样的目的。实际上，在编程中，大多数任务都可以通过不同的方法来实现。推荐的方法叫做*字符串模板*。

## 4.字符串模板

如果您曾经在另一个上下文中使用过单词 *template* ，您就会知道模板是某种东西的基本格式，在本例中是一个字符串。让我们来看一个字符串模板的例子:

```
val language = "Kotlin" println("My favorite language is $language") val anotherLanguage = "Swift" println("My favorite language is $anotherLanguage")
```

![](img/f6c99fcd1772cd0e8eb9f5e36391c43d.png)

你能猜出上面代码的结果吗？第一行将打印“我最喜欢的语言是 Kotlin”，而第二行将打印“我最喜欢的语言是 Swift”。

如果您仔细观察，将变量插入字符串的方法是在变量名前添加一个美元符号。很简单，不是吗？

如果你想将一个变量添加到另一个变量中，记住你应该总是更喜欢字符串模板而不是连接。

## 5.字符串文字:转义字符串

当您使用字符串时，有时您必须使用可能会使编译器混淆的字符，例如:

```
val famousQuote = ""Be yourself; everyone else is already taken." ― Oscar Wilde"
```

如果计算机试图解释上面的代码，它会认为你在结束字符串，而实际上，你想把双引号作为字符串的一部分。

上面的代码是无效的，但使它工作很容易，只需在字符后面加一个反斜杠。

```
val famousQuote = "\"Be yourself; everyone else is already taken.\" ― Oscar Wilde"
```

现在，编译器将知道您正在尝试添加双引号，并将搜索前面没有反斜杠的右引号。

根据[科特林文档](https://kotlinlang.org/docs/reference/basic-types.html#characters)，可用的转义序列有:`\t`、`\b`、`\n`、`\r`、`\'`、`\"`、`\\`、`\$`。但是你不必学这个，有一个更简单的方法，看看下一部分。

## 6.字符串文字:原始字符串

包含通常属于代码一部分的字符的另一种方法是通过原始字符串。让我们看一个例子:

```
""" 
"Be yourself; everyone else is already taken." ― Oscar Wilde 
"""
```

正如您在上面的代码中看到的，原始字符串以三个引号开始，以另外三个引号结束，只要确保您将引号放在它们自己的一行上。

现在，您可以在它们之间放置任何字符。如果加上休息，也是尊重。这是许多编程语言都没有的特性，好好利用它吧！有时候，试图逃离许多角色会变得令人讨厌和沮丧。

恭喜你！你可以看到，Kotlin 是一种非常简单的语言，但是“能力越强，责任越大”，所以不要放弃你的 Kotlin 学习之路！如果你想在这个话题上了解更多，我推荐[这篇关于 Guru99.com 的文章](https://www.guru99.com/kotlin-tutorial.html)，它为你的学习之路提供了更多的资源。

下次见！