# 从数组中删除重复项:在 Swift 中获取唯一值

> 原文：<https://betterprogramming.pub/remove-duplicates-from-an-array-get-unique-values-in-swift-7a477ef89260>

## 选择最适合您需求的性能选项

![](img/008ef39542fddffcafe66fb5502d82ce.png)

图:[重复内容](https://www.seobility.net/en/wiki/Duplicate_Content) —作者:Seobility —许可: [CC BY-SA 4.0](https://www.seobility.net/en/wiki/Creative_Commons_License_BY-SA_4.0)

移除重复项以从数组中获取唯一值是一项常见的任务。像 Ruby 这样的语言有内置的方法，比如`uniq`，但是在 Swift 中，我们必须自己创建这样的方法。标准库没有提供一个简单的方法来做到这一点。

有很多方法可以达到同样的结果，每种方法都有各自的优缺点。让我们回顾一下，看看哪些方法最适合您的用例。

# 默认情况下，使用集合移除重复项

在我们开始深入研究从数组中删除重复项的扩展之前，最好先了解一下 Swift 中的`set`。默认情况下，一个`set`只包含唯一的值，可以实现你想要的结果。

如果出现以下情况，请选择`set`:

*   顺序并不重要
*   默认情况下，您喜欢拥有独特的元素

```
let array: [Int] = [1, 1, 1, 2, 2, 2, 3, 3, 3]
let set: Set<Int> = [1, 1, 1, 2, 2, 2, 3, 3, 3]

print(array) // prints: [1, 1, 1, 2, 2, 2, 3, 3, 3]
print(set)   // prints: [2, 3, 1]
```

决定是使用`set`还是数组更像是一个代码设计决策。s 还具有更高性能的优势，这使它们成为解决唯一性问题的良好候选。你可以在我的[博客](https://www.avanderlee.com/swift/array-vs-set-differences-explained/)中读到更多关于`set`和`array`的区别。

不需要太多细节上的区别，知道一个`set`没有维持顺序是很好的。如果保持秩序对你来说很重要，你可能不想用`set`。你可以使用`NSOrderedSet`，但是这个类没有给你类型完成，你必须使用`Any`实例。

# 用扩展名从数组中移除重复元素

当元素的顺序很重要时，我们可以继续使用数组，并通过使用自定义扩展来获得唯一的值。

如果出现以下情况，请选择`array`:

*   秩序很重要
*   你不能轻易切换到`set`

我们必须创建一个扩展来过滤掉重复的元素。在创建扩展时，记住性能是很重要的，因为我们很容易以二次时间复杂度`o(N²)`结束。简而言之，这意味着你拥有的元素越多，性能下降的就越多。

下面这段代码依赖于`Hashable`协议来匹配元素，并且具有线性时间复杂度`o(N)`。这意味着 3 个元素需要 3 次迭代，10 个元素需要 10 次迭代，依此类推。

```
extension Sequence where Iterator.Element: Hashable {
    func unique() -> [Iterator.Element] {
        var seen: Set<Iterator.Element> = []
        return filter { seen.insert($0).inserted }
    }
}

print(array.unique()) // prints: [1, 2, 3]
```

下面我们来分解一下这个`unique()`方法:

*   我们创建一个`set`来跟踪看到的物体
*   过滤器用于迭代所有对象
*   `insert(_:)`方法返回一个包含插入布尔值的元组，如果对象被插入，则该元组被设置为`true`,否则被设置为`false`
*   插入的布尔值用来过滤掉数组中的重复项

最终结果是一个具有相同顺序但没有重复元素的数组。唯一的缺点是您的元素需要符合`Hashable`协议，但这应该不是什么大问题。事实上，标准库中的许多类型已经符合该协议，如字符串、整数和布尔值。

`Hashable`协议用于确定一个元素是否等于一个现有的对象，因此需要获得唯一的值。

# 结论

当你想获得一个系列的独特元素时，你可以选择一个`set`或者一个`array`的扩展。

*   如果顺序不重要，请选择`set`
*   保持顺序，并在数组上提取具有扩展名的唯一元素