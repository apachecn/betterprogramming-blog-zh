# 6 个高级 Ruby 循环

> 原文：<https://betterprogramming.pub/6-advanced-ruby-loops-13695c20d012>

## Ruby 的工具箱里有你需要的一切

![](img/0d2e1520946c1b8554114b366b798a31.png)

克莱尔·萨特拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的几年里，我写了很多 Ruby。由于其“[为开发人员的快乐而优化](https://www.artima.com/intv/ruby.html)”的原则，这种语言在我心中赢得了特殊的位置。

这是一种易于阅读、编写、板载、模板和原型的语言，还有其他好处。

它最好的特性之一是为许多常用的过程提供帮助方法。迭代、排序、过滤、转换等都有语言内置的多个helper 方法(在[可枚举模块](https://ruby-doc.org/core-2.7.0/Enumerable.html)中)。

您可以更多地关注您的逻辑，而不是编写遍历可枚举数的助手方法。

这不仅让代码写起来更有趣，也让代码更易读。

这些助手方法抽象了迭代如何发生的细节，并为逻辑本身提供了舞台。由于代码被读取的次数比被编写的次数多得多，这也有助于提高编码效率。

为了分享一些“开发人员的快乐”，我想回顾几个不太为人所知的 Ruby 循环。这些将使您的代码不那么冗长，更容易阅读，更快编写:

1.  `map`
2.  `each_with_object`
3.  `each_with_index`
4.  `partition`
5.  `select/reject`
6.  `any?/all?`
7.  奖金:`find_index`

请注意:为了简单起见，我在这里的大多数例子中使用了一个数字数组。但是所有这些方法都适用于数组和散列。两者都被认为是可枚举的。

# [地图](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-map)

用于从可枚举类型创建数组，并为每个元素应用特定的逻辑。

例如，对数字数组中的每个值求平方。因此，不用使用`each`来迭代并将结果插入到另一个数组中，您可以只使用`map`。

# [每个 _ 带 _ 对象](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-each_with_object)

对于通过迭代可枚举对象来创建对象很有用。

例如，如果我们想从一个数组中产生一个散列值呢？还是来自给定散列的一个`Person`对象？

您也可以使用`[reduce](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-reduce)`方法，但是我发现它的语法令人困惑，所以我更喜欢`each_with_object`。

专业提示:与`map`不同，您可以将`if`添加到块中，并挑选运行逻辑的元素。

# [each_with_index](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-each_with_index)

当除了迭代可枚举对象之外，还需要使用索引时，这很有用。例如，如果我们需要记录正在处理的当前索引，该怎么办？

# [分区](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-partition)

想象一下，你可以在一行中，根据一个条件，把一个数组分成两个数组。现在停止想象，你可以用`partition`做到这一点:

# [选择/拒绝](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-select)

`select`遍历一个可枚举数，只返回回答给定块的元素。`rejects`行为相同但相反，它返回不符合条件的:

对于数组来说，`select`和`reject`也有`!` (bang)风格:`select!`和`reject!`。这些将修改给定的可枚举值(`!`通常用于表示破坏性操作)，所以使用时要小心。

# [任何？/全部？](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-any-3F)

`any?`检查可枚举中至少有一个元素对应于一个条件。`all?`验证所有元素是否符合清单。

# 奖金: [find_index](https://ruby-doc.org/core-2.7.0/Enumerable.html#method-i-find_index)

有时，您只需要找到与条件匹配的元素的索引:

# 最后一次重复这个话题

1.  如果您需要一种特殊的方法来迭代 Ruby 中的可枚举数，很有可能它已经存在了。如果你使用一个框架(比如 Rails)，你可能会有[甚至更多的方法](https://apidock.com/rails/Enumerable)，因为它们包含了在基本集合上扩展的 gem。
2.  上面的助手方法帮助你编写更简洁和更集中的代码，从而使人们更容易阅读。

感谢阅读！