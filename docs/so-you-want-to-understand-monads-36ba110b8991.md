# 你想了解单子吗？

> 原文：<https://betterprogramming.pub/so-you-want-to-understand-monads-36ba110b8991>

## 对一个不必要地令人困惑的函数式编程概念的直观解释

![](img/e93101f036f4e207a738ccbb9f1c7a0d.png)

[帕卡塔·高](https://unsplash.com/@pakata?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

我刚刚读了一篇文章，文章中一个可怜的灵魂非常努力地试图向读者解释单子是什么，但悲惨地失败了，因为他自己并没有真正理解它。出于帮助他的愿望，我认为写一篇文章让人们先理解单子背后的直觉是有意义的，而不必进入疯狂的函数式语言语法。

我是否成功地做到了这一点，这要由你来判断，但是让我们马上开始吧。

我第一次遇到单子是在 Eugenio Moggi 将它们作为一种构建编程语言语义的方式引入的时候。这个公式非常简洁，并且清楚地给了一个非常大的编程语言构造类一个很好的数学模型，在此之前，这个类只是被孤立地处理。

莫吉在那篇论文(如果你能遵守数学的话)中提出的是对计算的深度理解，这在一代人中只会出现一次。没错，是一篇*深刻*直观的论文，将计算机科学的直观映射到坚实的数学基础上。在这里，我将把大量的数学知识从论述中剔除，但我确实希望传授直觉。

先说一个很简单的表达:`getc()`。c 程序员可能认为这类似于从文件中获取一个字符的函数。这里不是一个文件，假设它从键盘上获取一个字符。这是一个产生单个字符的简单函数。您可以在任何可以使用字符表达式的地方使用这个函数(也就是说，它名义上有类型`char`)。例如，如果我们想检查用户是否按下了键 *q* ，我们可以做`(getc() == ‘q’)`。

但是从计算上来说，`getc()`绝不是简单的`char`。它涉及到与程序外部环境的一系列复杂的交互，包括操作系统、硬件和用户。当我们考虑程序时，传统的智慧(继承自λ演算)是只对值进行推理。当你处理完全在程序内部的实体时，这很好。但是`getc()`在这里，我们不得不面对这样一个事实:在这个项目之外，还有一个我们必须应对的世界。它带来的事实是 I/O 操作可能会失败，或者它们并不总是产生相同的值，以及用户可能根本不会按键的事实。

这就是莫吉天才的第一部分。他凭直觉认为，价值与产生价值的计算有质的不同。换句话说，`getc()` 是一个*计算*产生一个*`char`*不应该把*和一个`char` *混为一谈。**

*因此，与其给`getc()`赋予类型`char`，不如给它赋予类型`T[char]`，这是产生`char`的计算 **T** 的简称。起初，这似乎是一种倒退。现在我们不能在所有可以使用`char`的地方使用`getc()` ，所以`(getc() == ‘q’)`不再正确，因为`==`两边的类型不同。我们现在该怎么办？*

*给大家介绍一对新词:*注射*和*投射*。当我们获取一个类型为`char`的值并将其嵌入另一个类型(如`T[char]`)时，这被称为*注入*。喷射的逆过程称为*投影*。这是我们从嵌入中提取注入值的地方。换句话说，我们将`char`从`T[char]`中投影出来。一般来说，注入和投射总是被设计成彼此相反的:它们组成了恒等函数。*

*那么，投影算子现在会允许我们从`T[char]`中得到`char`吗？这个投影看起来像什么？对于像`getc()`这样的计算过程，我们通过等待 I/O 调用终止来“投射*`T[char]`，给出值，然后处理值，就像使用另一个 char 和从它派生的任何其他值一样使用它。然而，一旦我们脱离了计算领域，进入了价值领域，我们现在已经完全掩盖了这样一个事实，即有一整套计算产生了价值，并将我们推回到起点(即，无法抓住`getc()`将我们拖入的外部世界)。)**

**这就是莫吉天才的第二部分发挥作用的地方。他提出了一个有点受限的投影算子，他称之为`let`，用它我们可以像这样重写上面的简单测试(这不是最终的形式，但它是一个很好的起点):**

```
**let c = getc() in (c == ‘q’)**
```

**让我来解释一下。这里，`c`的类型是`char` —而不是`T[char]`。**在这种情况下，投影意味着等待`getc()`返回一个字符。****

**如果`let`正在执行一个标准的投影操作，这个表达式将在最后产生一个布尔值。然而，整个表达式*仍然*有一个 I/O 操作(即一个计算)参与产生这个布尔运算。这意味着这个表达式并不真正代表一个布尔值，而是一个产生布尔值的*计算*。所以正确的做法是给整个`let`表达式赋予类型`T[boolean]`，这意味着`let`的主体必须返回类型`T[boolean]`。**

**在 Moggi 的公式中，这种类型对于所有投射的`let` 表达式都是*必需的*，并且由类型检查器强制执行。为了确保我们能够满足这个需求，我们引入了一个操作符`η`，它的惟一目的是将一个`τ`类型的值注入到`Τ[*τ*]` **中。它迫使我们记住这个值是通过计算产生的。所以我们把上面的`let`改写成这样:****

```
**let c = getc() in η(c == ‘q’)**
```

**现在，这个表达式的类型是`T[boolean]`。一般来说，这意味着从计算中产生一个值的事实永远不会被忘记。如果我们必须在其他地方使用这个`let`产生的值，我们必须记住它是一个`T[boolean]`。要提取这个布尔值，我们需要另一个`let`，其主体是`T[something]`等等。如果你已经理解了这一段，并想向你的朋友炫耀，把“单子的结合律”这几个字扔过来。**

**由此得出逻辑结论，任何使用计算的程序最终总是产生类型为`T[x]` 的答案，而不是类型为`x`的值。单子肮脏的小秘密是，我们需要做最后一个投影来从`T[x]`中提取`x`。在实践中，每个`T`总是伴随着这样的投影操作，但是它必须只用于非常明确的区域，在这些区域中我们不再寻求保留`T[]`的计算属性。**

**类型`T[]`连同伴随的`η`和`let`是单子(有时也被赋予有趣的名字“Kleisli triple”)。实际的名字来自于一个范畴理论结构，这个结构用比大多数人需要知道的更多的数学来形式化这个想法。就是这样。虽然在我们的例子中,`T[]`是涉及 I/O 的计算的替身，但是它也可以代替我们将在下面看到的各种不同类型的计算。**

**单子只有这些了。这个公式的目的是，它清楚地将不良的计算世界划分为`let` 的绑定(在`in`之前的`let`部分，其将名称绑定到从计算中投影出来的值)和`let`主体中的良好的功能世界。绑定内的世界充满了与绑定相关的不确定性，但是主体内的世界是干净的，并且保留了值是从计算中获得的事实。**

**所以，即使我的程序中有“效果”(这是我们给这些计算起的名字)，我也可以对程序的大部分内容进行推理，而不用过多考虑效果，因为它们现在已经被很好地打包在`T[]`中了。`let`的规则不要让我忘记什么时候我在处理一个计算，什么时候我在处理这个计算产生的一个值。**

**可以这样处理的计算范围很大。I/O 就是上面的一个例子。另一种常用的计算类型是“不确定性”，即一个值可能存在也可能不存在。例如，您可能在哈希表中查找一个可能找到也可能找不到的键。这种类型的计算被打包到`Option` monad 中。考虑这一点的方法是，如果`let`在从计算中提取值时遇到不存在(即哈希表没有我们要找的键)，整个`let`将不存在。否则，当该值存在时，`let`的体也将有一个值。在`Option`中，使用 Scala 中的`Some`或 Haskell 中的`Just`进行值(`η`)的注射。其他原语(例如哈希表中的`get`，在这种情况下，类似于`getc`)会自动在`Option`中产生一个值。**

**另一种常用的计算类型是“异步”在这种情况下，计算是异步发生的——可能是在另一个系统中(比如数据库)——并且这个计算将生成感兴趣的值。这种类型的计算通常被打包成`Future`单子。`Future`代表一种计算，它将产生我们最终将得到的值。`let`中的绑定等待计算产生这个值，只有当值准备好时才评估主体。此外，`let`现在作为一个整体代表了一个计算，它将在某个时候在`let`的主体中产生值。与我们对`let`工作方式的理解一致，`let`表达式本身就是一个`Future`。**

**这是一种非常简洁的方式来构建包含效果的程序。莫吉用它来定义像可变状态、延续等效果的语义。但是是 Phil Wadler 把它们的使用扩展到了函数式编程。与西蒙·佩顿·琼斯和其他人一起，他领导他们加入哈斯克尔。随后，它们被并入许多其他编程语言，包括 Scala 和 Swift。**

**我们为计算选择的特定的`T[]`通常有自己的运算，所以你可以影响/观察实际的计算本身。例如，您可以在一个`Future`上设置一个超时，而不必改变程序中只处理`Future`值的部分。或者，向计算提供恢复选项(因为您*知道*它是一个计算！).**

**所以，这就对了。单子在概念上非常简单。那么，为什么他们周围会有这些混乱和戏剧性的事情呢？现在请允许我把这些非常简单的想法用真实的语言映射成一些具体的结构，你就会明白为什么会搞砸了。**

**莫吉非常优雅的`let … in …` 构造在 Scala(和 Swift)中被重新表述为不幸命名的`for … yield …`和在 Haskell 中更不幸命名的`do … return …`(它们在语义上也略有不同)。注意，两种语言都选择将*绑定*操作命名为*命令性*操作，我认为这是一个非常令人遗憾的设计决定。任何阅读带有这些单词的程序的人都会立即感到困惑，因为它不是把它们带到一个“绑定”的地方，而是把它们带到一个“循环”的地方，或者更令人困惑的是，强制地从某个地方“返回”。**

**很明显，这背后有多年的历史原因，但深入一点会有所帮助。**

**在随后的[论文](https://ncatlab.org/nlab/files/WadlerMonads.pdf)中，Wadler 注意到并利用了列表和单子共享相似的代数属性的事实，这种相似性可以通过将一些列表函数扩展到一般的*所有*单子来利用。例如，我们有一个列表函数`map`，它接受一个类型为`(a ->b)` 的函数，并将它映射到一个`List[a]`上以产生一个`List[b]`。Wadler 观察到这种相似性可以用于单子，除了用类型`T[a]`和`T[b]`代替`List[a]` 和`List[b]`。因此，对于任何给定的单子类型`T`，我们可以定义:**

```
**map (f: (a->b), m:T[a]) : T[b] = let v = m in η(f v)**
```

**一旦你迈出了这一步，许多其他事情就会接踵而至。你会得到像`flatMap`这样的怪物，它们在列表的上下文中是有意义的，但是完全没有意义的单子符号。对于那些不知道`flatMap` 是什么的人来说，当我们在一个列表上映射一个函数，结果是一个列表的列表时，`flatMap`会将所有那些子列表附加在一起，产生一个平面列表。**

**我知道什么是`Future`，我也知道什么是`flatMap` 。但是`flatMapping`一`Future`呢？啊？那连*是什么意思*，到底是什么意思？不幸的是，在某些语言中(比如 Scala 和 Swift)，它实际上*并不意味着什么。事实上，它是某些东西的名字，是单子最初定义的一部分。它是`bind`操作符，也就是`let`被分解成:`Exp_1`中的`let v = Exp_0`翻译成`bind(Exp_0, λv. Exp_1)`。它在数学上用`‘*’`表示，Haskell 称之为`>>=`。***

*然而，称之为`flatMap`是完全错误的。事实上，我读过的一篇 Swift 文章甚至说，如果你能为一个数据结构定义`flatMap`，它就是一个单子。我都不知道该说些什么。这就是我们把人们的头脑搞得有多糟糕。*

*类似地，列表上的`for`(即循环)在列表世界中是合理的理解(最终基于集合论中常见的集合理解)，在单子上变成了完全无意义的循环(例如，允许我用`Option` **来循环你)。** *嗯？*)。*

*同样令人沮丧的是，并不是所有的列表函数都扩展到单子。例如，我多少能看出`Future`的`head`可能是什么。但是一个`Future`的`tail`是什么鬼？在之后剩下的*一个计算结束了，或者可能是*是*的计算，所以我可以再运行一次？好的好的好的。我知道代数是非常相似的，而且它在数学上是可行的，但是程序是为*阅读*而设计的，它们的意图很重要。任何一个`flatMap`与`bind` 相同的世界都是一个错得离谱的世界。**

*为什么不坚持简单优雅的装订呢？真的有那么难吗？由于过度设计的工具，一代又一代的新手一直在为计算和值的简单分离而奋斗(可能现在仍然在奋斗)。是的，`let`在大多数其他语言中确实有其他含义，但是这个问题很容易通过像`mlet`、`let[T]`(例如`let[Option] c = … in …`)或其他同样有意义的语法来解决。或者冒着稍微复杂一点的风险，坚持使用`bind`操作符，不用语法就能完成同样的任务。*

*因此，如果你对任何语言中的单子都不熟悉，找到你感兴趣的`T[]` 的对等词，确定它所代表的计算和`let … in …`的对等词，这就是你需要的全部。其他的都是噪音。*