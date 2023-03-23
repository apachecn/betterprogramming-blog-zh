# 函数式编程技巧:如何在保持引用透明性的同时改变状态

> 原文：<https://betterprogramming.pub/your-code-should-read-like-a-story-how-to-mutate-state-and-have-effect-on-the-world-while-3e3ae808d2d6>

## 你的代码读起来应该像一个故事

![](img/02dbb6be9f0b6ec81ae483ae1b30c9fe.png)

罗马卡夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你在大多数地方读到的关于函数式编程(FP)的东西是“*变异状态和副作用是* ***而不是*** *允许的*”。当你一遍又一遍地阅读它时，你会有这样的印象:FP 是非常有限的，并不是在所有情况下都适用。大多数真实世界的应用程序都涉及到某种形式的突变，通常你需要 IO 或其他操作，这些操作被归类为 FP 中的副作用。

然而，这里面有一个秘密，你需要更深入地挖掘才能发现…状态改变实际上是允许的...令人震惊，我知道！它需要你对设计多考虑一点，你需要满足某些标准，但这是可能的！让我们看看如何能做到这一点。

# 副作用与效果

首先，让我们在**效果**和**副作用**之间做一个微妙但非常重要的区分。例如，一个 IO 操作可以在一个纯程序中使用，在不破坏引用透明性的情况下对外部世界产生影响。这可以通过设计你的程序来实现，让它读起来像一个故事。我这么说是什么意思？在一个故事中，你有演员，每个演员都有自己的一套台词和动作要执行，没有两个演员有重叠的目的，没有一个是多余的，没有一个演员扮演多个角色。

在程序中，类是你的演员，方法是他们角色的抄本。当你设计你的应用程序时，你应该考虑游戏中的所有角色以及他们将要使用的所有词语。当你写的时候，你正在慢慢地创建你自己的领域特定语言(DSL ),甚至一个非程序员也应该能够在一个高层次上理解代码所做的事情。我知道许多开发人员以提出几乎不可读的解决方案而自豪，但是这是危险的，也是不必要的。这是一个很酷的脑力练习，一个脑筋急转弯，但是除非你被迫优化有限的资源(无论是内存、CPU 还是其他特定的限制)，否则最好选择可读性、可测试性和可维护性。

让我们来看一个非常简单的例子，如何通过将副作用推到外层，使其他一切都变得纯粹，并使用更多特定于领域的语言，将一个有副作用的非纯粹函数变成函数式编程中可以接受的东西。

```
fun printHousePriceStats(houses: List<House>) {
    val prices = houses.map{ it.price }
    val msg = "Average price: ${prices.*average*()}, standard deviation: ${prices.*standardDeviation*()}"
    println(msg)
}
```

这是一个过于简化的例子，我试图非常明显地粘贴所有我需要的部分。因此，在代码中编写或重构有效果的函数时，您应该始终将它们视为 3 个独立的部分:

*   向纯核心函数提供输入的函数
*   纯粹的核心功能
*   输出您的结果的函数

在我们惊人的例子中，这将转化为:

```
fun getPrices(houses: List<House>): List<Double> {
    return houses.map{ it.price }
}fun getStatsMessage(housePrices: List<Double>) {
    return "Average price: ${prices.*average*()}, standard deviation: ${prices.*standardDeviation*()}"
}fun printHousePriceStats(houses: List<House>) {
    println(getStatsMessage(getPrices(houses)))
}
```

这些都是一行程序，但是概念是一样的，就好像你想象对`getPrices`和`getStatsMessage`进行复杂的计算。这样，您可以单独测试这两个函数，并将打印尽可能推到最上面，在`printHousePricesStats`函数中不留下任何要测试的内容(您不会测试一个标准的语言库函数)。

要声称你的程序是全功能的，尽管你还需要一个步骤——将带有效果的最终外层包装到一个返回可执行文件的对象中，而不是实际执行动作。

```
interface IO { 
   fun run(message: String)
}
```

然后，`printHousePriceStats`到:

```
fun printHousePriceStats(houses: List<House>): IO {
    return object : IO { 
override fun run(): = println(getStatsMessage(getPrices(houses))) 
    } 
}
```

对于这个超级简单的例子来说，这无疑是矫枉过正了，但是对于一个复杂的情况来说，如果你想符合所有的 FP 原则，但是仍然有效果并且测试它——你甚至可以更进一步，如果你需要或者想在 monad train 上疯狂一下，你可以通过添加执行`map`、`flatMap`和 IO 上的其他必需操作的类。

# 突变状态

引用透明的一个更有用的概念实际上允许你在一个表达式中局部使用变异状态**，只要你能保证程序的其他部分不能“看到”它。**

**局部变异是必须的算法的一些例子是矩阵转置、插入排序、快速排序或者只是在适当的位置反转数组的顺序。**

**如果这种变异只包含在一个函数的范围内，而没有向调用者公开任何有关实现的细节，那么它就被认为是纯的。还不确定吗？让我们来看看 RT(参照透明性)和纯度的定义:**

> **如果一个表达式在程序中的所有出现都可以用它的计算结果替换，而不改变程序的含义，那么这个表达式就是引用透明的。**
> 
> **如果一个函数对于所有引用透明的自变量都是引用透明的，那么它就是纯函数。**

**任何调用者都不可能知道这里的`reverseArray`正在使用另一个改变其状态的函数。即使`swap`不是纯的，`reverseArray`仍然是引用透明的，因为程序的其余部分实际上永远无法获得可变数组。**

```
fun reverseArray(arr: IntArray): IntArray {
    val n = arr.size
    val revArr = arr.*copyOf*(n)

    fun swap(x: Int, y: Int) {
        val tmp = revArr[x]
        revArr[x] = revArr[y]
        revArr[y] = tmp
    }

    for (i in 0..(n/2-1)) {
        swap(i, n - i - 1)
    }

    return revArr
}
```

**任何函数都可以在内部使用副作用，并且仍然保持对其调用者的纯外部接口。我们可以对此进行论证并口头证明，但这是一种非常容易出错的方法——如果新来的人以暴露`revArr`数组的方式更改了代码，该怎么办？**

**为了执行这些规则，理想情况下，我们应该让其他开发人员清楚地看到……以及我们自己，什么时候一个变化打破了这些规则。我们需要借助编译器的帮助来设计我们的程序，如果违反了 FP 原则，它甚至不能编译。**

**为此，我们可以回到故事类比。假设我们正在整理一些 Int 类型的 id，id 数组实际上是产品的集合，所以我们可以将反转函数包装在一个对象`ProductIdReverser`中，函数本身可以接受一个`ProductIds`类型或`Array<ProductId>`，这取决于什么更适合整体设计。正如你可能看到的，我们正试图围绕原语和内置类型构建一种语言，这种方式将使我们的程序不仅易于阅读，而且难以破解，因为许多错误可能在编译时被捕获。**

**如果您试图将遗留代码移植到功能更强的代码中，这可能是一个很好的开端——从提取方法、变量和定义类型开始。在某些情况下，甚至对于在字符串周围定义简单的包装器也有争议。想象一下，在一个项目中，您要处理多个实体，每个实体都有一个 ID，例如，现场搬运产品的机器。在那里，您必须为机器、站点位置和产品使用 id，并且您需要所有这些来为正确的机器发送正确的命令。在这种情况下，您可能会看到这样的内容:**

```
fun doX(machineId: String, locationstartId: String, productId: String, locationEndId: String) {
    // build and send off the command
}
```

**乍一看，这似乎是明智的，也是可行的，它将完成这项工作。然而，这是一个等待发生的噩梦。即使这是有据可查的，调用者也总是只能看到预期的 4 个字符串，并且很容易意外地以错误的顺序传递它们。即使经过测试，想象一下在代码中有数百个地方有类似的东西…错误肯定会出现。有几种方法可以避免这种情况，但是没有一种方法比 id 的类型层次结构更健壮:**

```
sealed class MachineId(id: String) {
    class MovingMachineId(id: String): MachineId(id)
    class PackingMachineId(id: String): MachineId(id)
    class LoadingMachineId(id: String): MachineId(id)
}

sealed class ProductId(id: String) {
    class SmallProductId(id: String): ProductId(id)
    class MediumProductId(id: String): ProductId(id)
    class BigProductId(id: String): ProductId(id)
}

sealed class LocationId(id: String) {
    class StationLocationId(id: String): LocationId(id)
    class DockLocationId(id: String): LocationId(id)
    class PathLocationId(id: String): LocationId(id)
}
```

**当然，这并不适合所有的情况，但是可以帮你省去很多麻烦……在测试、调试和人员入职方面节省很多时间。**

**马可·维穆伦和其他人写了一本很棒的关于 Kotlin 中的*函数式编程的书。例子是在 Kotlin 中，但它深入了许多关于一般理论的细节，所以如果你不是 Kotlin 专家，甚至不是 kot Lin 的粉丝，你仍然可以全部了解并学到很多东西。***

**感谢阅读，重构愉快:)**