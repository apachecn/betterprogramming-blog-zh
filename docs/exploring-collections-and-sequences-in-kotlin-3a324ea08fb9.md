# 在科特林探索收藏和序列

> 原文：<https://betterprogramming.pub/exploring-collections-and-sequences-in-kotlin-3a324ea08fb9>

## 学习如何根据上下文处理集合

![](img/1baa7c7ff18970725db5a6d3e6d038c3.png)

Clem Onojeghuo 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

对于任何平台上的开发人员来说，处理集合都是一项常见的任务。Kotlin 提供了许多扩展函数来处理集合。主要来说，它提供了两种处理集合的方法:急切地处理集合，懒散地处理序列。请继续阅读，了解它们。

我不打算评判两者中哪一个最好。相反，我将用一个简单的例子来解释它们是如何工作的，这样你就可以根据你的环境选择最好的一个。

# 准备抢劫

为了让事情变得有趣，让我们考虑一系列抢劫。`Heist`是一个有四个变量的数据类，分别是数据类型为`String`、`Int`、`Boolean`、`Crew`的`place`、`year`、`execute`、`crew`。看一看:

```
data class Heist(val **place** : String,
                 val **year** : Int,
                 val **execute** : Boolean,
                 val **crew** : Crew)
var **lishOfHeists** : ArrayList<Heist> = ArrayList()
```

crew 是另一个数据类，在这个数据类中，我们将船员的数据作为一个带有`CrewMember`类型的数组列表。看看`Crew` 和`CrewMember`数据类:

```
data class Crew(val **crewList** : ArrayList<CrewMember>,
                val **total** : Int)data class CrewMember(val **name** : String,
                val **expertise** : String)
```

准备工作到此为止。让我们开始真正的任务吧。在`lishOfHeists`中，我在第 5 位找到了一件 2020 年的抢劫物品。我们的任务是在 2020 年实施抢劫。

# 用收藏品执行抢劫

首先，让我们用集合来热切地做它。看一看:

这里，我们有两个操作:`map`(中间转换)和`first`(终端操作)。

当`map` 内部触发`listOfHeists` 时，会创建另一个列表。一旦列表中的所有对象都在`map`中迭代完毕，就会进入终端操作`first`。

无论列表中有 10 个还是 100 个项目，当您执行集合时，所有 100 个项目都将在`map`中急切地运行。然后将执行终端操作。

如果我们要搜索的物品位于第五个位置，就像我们的抢劫一样，地图将执行所有 100 次抢劫，因为只有在中间转换中完成所有迭代后,`terminal`函数才会执行。

问题是中间转换发生在所有项目上，不必要地浪费了宝贵的资源。

# 用序列执行抢劫

让我们用序列懒洋洋地做它。看一看:

与集合不同，序列不会立即执行中间转换。它将缓慢地等待，直到`terminal`功能被触发，然后它将依次执行`intermediate`和`terminal`功能。

这样，在迭代中，`intermediate`和`terminal`函数将依次执行。所以在位置 5，迭代将在条件满足时终止。这解决了不必要地迭代列表中所有项目的问题。

# 结论

当您处理小列表并且转换不超过两到三次时，集合可能就可以了。但是当你处理大型列表和复杂操作时，我建议使用序列。务实一点，选择适合自己的。

感谢您的阅读！