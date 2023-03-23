# 在 Swift 中查找并返回给定字符串的所有出现的范围

> 原文：<https://betterprogramming.pub/find-and-return-the-ranges-of-all-the-occurrences-of-a-given-string-in-swift-2a2015907a0e>

## 串东西

![](img/ed7c03f1e11015ec8f41ad631be1c74a.png)

[Raychan](https://unsplash.com/@wx1993?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

当讨论字符串模式匹配识别问题时，您脑海中首先想到的可能是 Knuth-Morris-Pratt 算法。不过，我想介绍一个基于`range(of:options:range:locale:)`的更直观的方法，它是 Swift 标准库的一部分。

# 多次返回第一个匹配项以收集所有匹配项

如果你查阅[苹果开发者文档](https://developer.apple.com/documentation/foundation/nsstring/1417348-range)，你会发现`range(of:options:range:locale:)`函数的描述:

> 根据给定的选项，使用指定的区域设置(如果有)，在给定的字符串范围内查找并返回给定字符串的第一个匹配项的范围

是的，它只返回第一个匹配项。因此，为了实现我们的目的，我们需要构造一个`while`循环来迭代接收器中给定字符串的所有出现。参考代码片段类似于下面的代码块。请注意，还添加了一个偏移量来减少每次迭代中的范围长度。因此，实际上，每个循环的位置基本上是基于前一次迭代的上限:

# 将索引转换为范围

有时，返回所有索引不足以在文本编辑中执行查找并替换所有内容的操作。因此，一个`transform`助手可以帮助生成一个`range`数组，使得处理子字符串更加容易:

# 从 Swift 4 之前的范围创建 NSRange

如果你已经把你的 Xcode 迁移到 9 版了，Swift 4 的 Swift 标准库提供了一个直接从`Range<String.Index>`转换到`NSRange`的方法。这绝对是许多开发人员期待的一个特性，因为有时当某些函数调用还没有完全迁移到原生 Swift 时，处理`NSRange`仍然是不可避免的。下面是一个`NSAttributedString`的例子:

为了更顺利地迁移到 Swift 的当前版本，我们可以实现一个语法兼容的功能，从`Range`创建`NSRange`:

# 在操场上测试上面的功能

基本上，上面的代码包含了我们需要查找并返回给定字符串所有出现的范围的元素。让我们在下面的代码片段中测试并查看结果:

在操场上，日志控制台会是这样的:

```
[Int] : [12, 31, 50, 69, 88]
`Range` : playground
`Range` : playground
`Range` : playground
`Range` : playground
`Range` : playground
`NSRange` : playground
`NSRange` : playground
`NSRange` : playground
`NSRange` : playground
`NSRange` : playground
```

# 结论

感谢阅读！