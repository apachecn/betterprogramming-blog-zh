# 不要使用 Java For 循环——考虑 Java 流

> 原文：<https://betterprogramming.pub/dont-use-java-for-loops-consider-java-streams-instead-e439d09940b7>

## 永远不要再编写 for 循环

![](img/bbe8464a3dc92267c23bf322d5bc1382.png)

丹·弗里曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

流是 Java 8 最重要的特性之一。为了找出它们有用的原因，你需要理解 Java 中的流是什么。

*流*是一个数据序列，允许对所有或部分数据进行特殊类型的处理。我们可以创建流或将现有的结构转换成流。流可以代替循环，因为它们允许处理数据序列(类似于循环)。

让我们看一个例子。

我们想创建一个随机数列表:

然后，我们决定将所有大于`5`的数字过滤到一个新列表中:

输出:

```
[8, 6, 8, 6]
```

我总是觉得这种语法相当冗长。让我们试着用流来简化这个代码逻辑。

*注意:请记住，因为数字是随机的，所以您的列表会有所不同。*

输出:

```
[7, 8, 6]
```

请注意，随机性会导致列表大小发生变化。

查看上面的代码，您可以看到我们能够编写更少的代码并获得更简单的结果。

上面的代码首先使用`Stream.generate` *生成一个随机数列表。*使用`limit(10)`这个流被限制为只有十个数字。输出是一个整数流(`Stream<Integer>`)。

接下来，我们的流使用基于所有元素> `5`的`filter`进行过滤。

如您所见，这比使用`for`循环要少得多的代码，也更符合逻辑。

这两个操作甚至可以合并:

*注意:* `*() ->*` *基本都是 Java 版本的匿名函数(Lambda 表达式)。它们是在 Java 8 中与流一起引入的。如果只有一个参数，则不需要括号(类似于* `*e -> e > 5*` *)。它们的实现类似于 JavaScript 的箭头函数。*

当您在编写更大的应用程序时，流会变得非常方便。

让我们从基础开始看。

# 基础知识

如前所述，流是一个数据序列，允许对所有或部分数据进行特殊类型的处理。我们可以创建流或将现有的结构转换成流。

流本质上是一个数据序列。我们可以用几种不同的方式在 Java 中创建流。

## 产生

```
Stream<String> streamGenerated =
        Stream.*generate*(() -> "value");
```

通过引用`Stream`类的`.generate`方法，您可以生成由字符串`“value”`组成的流。

流是延迟加载的(你可以说流是一个延迟加载的集合)。这意味着直到需要它们时才进行计算。这导致了一个有趣的性质。即使`Stream<String>`被声明，*也没有操作*发生。

这是必要的，因为`Stream.generate`生成无限多的值。在这种情况下，`“value”`被生成了无限次。但是，因为它是懒加载的，所以不进行计算。

让我们通过添加`.limit`方法将这个流从无穷大限制到 20 个数字:

```
Stream<String> streamGenerated =
        Stream.*generate*(() -> "value")**.limit(10)**;
```

流遵循一种构建器模式——因此您可以将操作符相互链接起来。`.limit`返回一个`Stream`。其他返回流的运算符称为非终结符。他们本质上延续了这个链条。

对于要计算的流，需要添加一个终端运算符。它将流转换成我们的程序可以使用的实际类型(如 int、float、double)或集合(数组、hashmaps)。

最常见的就是把这个变成一个`List`之类的集合。

让我们把我们的流变成一个列表。这可以通过`.collect`方法完成:

```
List<String> streamToList = streamGenerated.**collect(Collectors.*toList*())**;
```

`.collect`方法采用一个`Collector`。根据`Collector`的类型，您可以控制如何将流缩减为什么。在这种情况下，流将被简化为一个列表。

其他`Collector`操作的简单示例包括:

*   `.toSet()`
*   `.toMap()`
*   `.joining()`

`streamToList`的输出:

```
[value, value, value, value, value, value, value, value, value, value, value, value, value, value, value, value, value, value, value, value]
```

现在，我们有了名单。

## 重复

这类似于`.generate`，但是它允许你创建一个类似于`for`循环的迭代流。这也生成了一个无限流。

```
Stream<Integer> streamIterated = Stream.*iterate*(0, i -> i + 2).limit(10);
```

第一个参数是起始数字，第二个参数是希望它“如何”迭代。

输出:

```
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

`.iterate`的结构类似于下面的`for`循环结构:

```
for (int i = 0; i < 20; i = i + 2) {
    // some code
}
```

## **收藏**

Java 中的集合允许你存储和操作数据。所有现有的集合都可以直接转换成流。集合下实际上有三种类型的接口。其中包括:

*   列表
*   设置
*   行列

您可能对`List`界面最为熟悉。`List`接口的不同实现包括`ArrayList`、`Vector`和`Stack`。

假设我们有一个名为`arrayToStream`的整数`ArrayList`。这可以很容易地变成一条小溪。所有这些集合都支持使用`.stream`方法转换成流:

```
Stream<Integer> stream = arrayToStream**.stream()**;
```

就这么简单。

如上所述，有许多特殊的流程可以在流上完成。我们最近使用了`.limit`操作来限制我们的流。这是一个*非终结符*符。非终结符总是返回另一个流。在本文的开始，我们使用了非终结符`.filter`

streams 的主要优势之一是它们提供了过多的非终端运营商。这些操作符提供了一种更简洁的循环方法。

*   `map`
*   `filter`
*   `distinct`
*   `limit`

一些操作符看起来类似于做同样事情的 Python 和 JavaScript 操作。实际上，流与 Python 和 JavaScript 中的生成器非常相似。

让我们看看其中的一些操作符。

## 地图

这用于迭代修改列表中的每个元素。修改可以包括将元素转换成不同的类型或者使用某种逻辑(公式)来转换元素。

比如你有一个从一到十的数字列表:`[1,2,3,4,5,6,7,8,9,10]`。你要把这些数平方(数乘以自身)成`[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]`。这是一个何时使用`map`的例子。

Java 中的映射是通过流支持的。`map`是非终结操作，所以会返回一个流。但是，如果我们想将其转换为实际类型，我们可以使用终端操作符，如`.collect`。

```
Stream<Integer> numbers1To10Stream = numbers1To10.stream()**.map**(num -> num * num);
```

我们需要终端操作符`.collect`将它转换成一个列表。

如果我们在没有`.collect`的情况下打印 `numbers1To10Stream`，它会打印出类似于`java.util.stream.ReferencePipeline$3@3feba861`的东西，因为它还不是我们可以使用的类型。

用`.collect`输出:

```
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

## 过滤器

`filter`顾名思义。您可以根据特定条件过滤列表。

例如，使用与上面相同的数字列表并过滤所有偶数将产生`[2, 4, 6, 8, 10]`。

```
Stream<Integer> numbers1To10Stream = numbers1To10.stream()**.filter**(num -> num % 2 == 0);
```

`filter`方法需要一个函数，该函数接受一个值并评估它是`true`还是`false`。如果选择`true`，该号码将出现在列表中。无论输入什么数字，都会检查它是否能被`2` : `num -> num % 2 == 0`整除。

输出:

```
[2, 4, 6, 8, 10]
```

## 明显的

这个非终结符从一个流中移除所有的重复项。

假设我们有一个数字列表:`[3, 3, 3, 3, 8, 8, 8, 8]`。删除所有重复项会使列表变成`[3, 8]`。

```
Stream<Integer> distinct = duplicatedList.stream().distinct();
```

输出:

```
[3, 8]
```

现在来覆盖一些码头运营商。我们已经通过了`.collect`操作符。有时，我们想要在流上执行一些计算，但是我们想要一个奇异值，比如 int 或 string，而不是 list。

## 任意匹配

如果*流中的任何元素*匹配某个条件，则返回`true`，否则返回`false`。

使用示例`[3,4,7,9]`，让我们检查是否有偶数:

```
boolean anyMatchBool = anyMatchList.stream().**anyMatch**(num -> num % 2 == 0);
```

回想一下，终端操作符返回的是实际类型。在这种情况下，返回一个 ***布尔*** 值。

输出:

```
true
```

# allMatch

如果流中的所有**元素*匹配某个条件，则返回`true`，否则返回`false`。*

*使用示例`[3,4,7,9]`:*

```
*boolean anyMatchBool = allMatchList.stream().**allMatch**(num -> num % 2 == 0);*
```

*上面的例子会产生`false`，因为所有的数字都是奇数。*

*如果列表是`[2,4,6,8]`，结果将是`true`，因为所有的数字都是偶数。*

## *为每一个*

*这允许你遍历列表中的每个元素并对这些元素进行操作。操作运行不应该返回值，但确实有副作用。*

*使用示例`[3,4,7,9]`:*

```
*list.stream().forEach(num -> System.*out*.println(num));*
```

*输出:*

```
*3
4
7
9*
```

*与`map`不同，`forEach`不修改已有的列表。*

*让我们看一些流的用例。*

## *锻炼*

*获得一份有`“Expired”`身份的图书清单，并检查这些图书中是否有任何一本书被乔丹借走；*

*上图中，我们有一个图书馆知识库，它会给出一个图书列表。这些书是所有过期的书的`filtered`，然后检查是否有属于`Jordan`的书。如果其中任何一个属于他，它将返回`true`，否则返回`false`。*