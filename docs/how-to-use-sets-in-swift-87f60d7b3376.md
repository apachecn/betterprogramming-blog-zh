# 如何在 Swift 中使用器械包

> 原文：<https://betterprogramming.pub/how-to-use-sets-in-swift-87f60d7b3376>

## Swift 套件完全指南

![](img/6004655c82630b520741f647ec406ac9.png)

[davisuko](https://unsplash.com/@davisuko?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

集合基本上是没有顺序但独一无二的项目的集合。让我们回顾一下 Swift 在器械包方面的不同操作/功能。

# 创建集合

## 初始化空集

Swift 中的集合是通用的，因此它们可以保存任何类型，只要它符合`Hashable Protocol`。为了简单起见，我们将使用`String`和`Int`。

```
**var** fruits = Set<String>()
```

## 用元素初始化集合

我们也可以用一些默认元素初始化一个集合。

```
**var** fruits:Set<String> = ["apple","mango","guava"]
```

## 初始化具有容量的集合

我们可以为最小数量/容量设置预分配的空间。

```
**var** fruits = Set<String>(minimumCapacity: 2)
```

# 检查器械包

## 检查是否为空

由于`Set`符合`Collection Protocol`，我们可以对`isEmpty`进行检查。

```
**var** fruits = Set<String>()fruits.isEmpty //true
```

## 数数

通过访问`count`属性，我们可以很容易地获得`Set`中元素的数量。

```
**var** fruits:Set<String> = ["apple","mango","guava"]fruits.count //3
```

## 容量

该属性返回不分配任何额外存储的情况下`Set`可以包含的元素数量。

```
**var** fruits:Set<String> = ["apple","mango","guava"]fruits.capacity //3
```

## 包含

这个函数在检查一个集合是否包含一个元素时非常有用。这个函数返回一个布尔值。

```
**var** fruits:Set<String> = ["apple","mango","guava"]fruits.contains("grape") // falsefruits.contains("apple") // true
```

# **添加元素**

## 插入

我们可以使用`insert`函数向 Swift 中的集合添加一个元素。只有当元素不存在时，才会插入。

```
**var** fruits:Set<String> = ["apple","mango","guava"]fruits.insert("grape")  //(inserted true, memberAfterInsert "grape")print(fruits)  //["mango", "apple", "guava", "grape"]
```

如果您注意一下`insert`函数的输出，我们会得到一个返回的元组，它给出了关于元素是否被插入的信息。元组中的这个值对于检查插入是否真的发生非常有用。

## 储备能力

这保留了足够的空间来容纳我们想要的元素数量。

```
**var** fruits = Set<String>()fruits.reserveCapacity(5)
```

# 移除元素

## 过滤器

由于`Sets`符合`Collection Protocol`，所以我们可以非常容易地使用类似过滤器的高级操作。

```
**var** numbers:Set<Int> = [1,2,3,4,5,6,7,8,9,10]**let** evenNUmbers = numbers.filter {**return** $0%2 == 0}evenNUmbers //{6, 10, 4, 2, 8}
```

## 去除

此函数移除从集合中传递的元素。

```
**var** fruits:Set<String> = ["apple","mango","guava"] //{"apple", "mango", "guava"}fruits.remove("apple")fruits //{"mango", "guava"}
```

## 首先移除

这将从无序集合中移除第一个元素。永远记住，它可以删除任何元素，因为它在一个无序列表中。

```
**var** fruits:Set<String> = ["apple","mango","guava"] //{"apple", "mango", "guava"}fruits.removeFirst()fruits //{"mango", "guava"}
```

## 全部删除

这将清除集合。

```
**var** fruits:Set<String> = ["apple","mango","guava"] //{"apple", "mango", "guava"}fruits.removeAll() //Set([])
```

# 组合集合

## 联盟

这有助于我们合并两组结果。这里我们取两组，一组是偶数，一组是奇数。

```
**let** evenNumber:Set<Int> = [2,4,6,8] //{2, 4, 8, 6}**let** oddNumbers:Set<Int> = [1,3,5,7] // {7, 3, 1, 5}**let** result = evenNumber.union(oddNumbers) //{4, 7, 3, 1, 6, 2, 5, 8}
```

在上面的代码中，`result`集合包含两个集合中的元素。

## 交集

这有助于我们从两个集合中获取公共部分，并返回一个集合。让我们看看下面的例子。

```
**let** numbers:Set<Int> = [1,2,3,4,5,6,7,8] //{2, 4, 8, 6}**let** oddNumbers:Set<Int> = [1,3,5,7] // {7, 3, 1, 5}**let** result = numbers.intersection(oddNumbers)//{5, 1, 7, 3}
```

## 对称差

这个函数与`Intersection`正好相反，它产生一个集合，该集合删除公共元素并返回其他所有内容。

在上面的例子中，角色`a`在`letters`和`vowels`集合中是公共的，所以`result`集合包含了这两个集合中除了公共部分(`a`)之外的所有内容。

## 减去

这个函数有助于从给定的集合中删除元素。

```
**var** numbers:Set<Int> = [1,2,3,4,5,6,7,8,9,10] //{1, 4, 6, 3, 7, 10, 5, 2, 8, 9}**let** evenNumbers:Set<Int> = [2,4,6,8] //{4, 2, 6, 8}numbers.subtract(evenNumbers) //{1, 3, 7, 10, 5, 9}
```

如果你看看上面的例子，从`numbersSet`中减去`evenNumbers`后，我们得到一个只有偶数的集合。

# 查找元素

## 福建话

这给了你集合中的最小元素。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}numbers.min() //1
```

## 最大

这给了你最大的一套。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}numbers.max() //4
```

## 首先(其中:)

这有助于您在集合中找到满足`where`子句中的条件的元素。请注意，由于集合是一个无序的集合，您将通过运行下面的相同代码获得不同的结果，因为它只返回第一个匹配所需条件的结果。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}**let** result = numbers.first { $0%2 == 0}//4print(result) //4
```

# 变换集合

## 地图

由于器械包实现了`Collection Protocol`，我们可以使用类似`map`的高级功能。`Map`基本上按照我们想要的方式转换集合中的每个元素，并给出一个列表。假设我们有一组数字`1,2,3,4`。我们的地图会将它们每一个乘以 2。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}**let** result = numbers.map{$0 * 2}print(result) // [2,4,6,8]
```

## 减少

`Reduce`可用于合并收集的结果。在我们的例子中，我们将添加集合中的元素。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}**let** result = numbers.reduce(0) {$0 + $1}print(result) // 10
```

# 迭代一个集合

## 为每一个

这将帮助我们对集合中的每个元素调用相同的闭包/转换。在下面的例子中，我们只是打印集合中的每一个元素。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}numbers.forEach {print($0)}
```

## 枚举

这是我们迭代`Set`元素的另一种方式。在这种情况下，我们将访问`index`以及元素本身。

```
**var** numbers:Set<Int> = [1,2,3,4] //{2, 3, 4, 1}**for** (index,element) **in** numbers.enumerated() {print("index is \(index) and element is \(element)")}
```

这是输出。

```
**index is 0 and element is 2****index is 1 and element is 3****index is 2 and element is 4****index is 3 and element is 1**
```

# 结论

集合保存无序的集合，但是它们保存唯一值的能力使它们在我们的日常实现中成为非常有用的数据结构。我希望你现在准备好使用`Sets`。

# 参考

[https://developer.apple.com/documentation/swift/set](https://developer.apple.com/documentation/swift/set)