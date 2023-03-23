# 编写简洁代码的 5 个有用的 Swift 一行程序

> 原文：<https://betterprogramming.pub/5-useful-swift-one-liners-to-write-concise-code-e63f75337a53>

## 学习一些一行程序来提高代码的可读性

![](img/716efb124b9f615db6f7691e0abe3d52.png)

由[麦克斯韦·纳尔逊](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 1.单行 If-Else 运算符

您知道可以替换这个简单的`if-else`语句吗:

用这个简洁的小一行表达式？

```
money > 0 **?** print("Some money") **:** print("No money")
```

在 Swift 中，这实际上被称为*三元条件操作符*(这在其他编程语言中也是一个常见的特性)。

以下是三元条件句的一般结构:

```
condition **?** true_expression **:** false_expression
```

不过，使用这个的时候要小心。这种简写是替换简单的`if-else`语句的有用方式。然而，过度使用代码降低可读性是不明智的。

# 2.交换两个变量，不需要助手

为了在没有辅助变量的情况下交换两个变量，您可以利用*元组析构*:

```
**var** a = 1
**var** b = 2(a, b) = (b, a)print(a, b)
```

输出:

```
2 1
```

# 3.检查可选值中的 nil

您不需要编写`if-else`语句来检查可选值是否为`nil`。相反，你可以使用一个*零合并操作符*、`??`，只用一行代码就可以达到同样的效果:

```
**var** name: String?print(name **??** "N/A")
```

输出:

```
N/A
```

*Nil 合并*是 Swift 中常用的功能。它通过检查`??`的左侧是否为零来工作。如果是，那么它返回右边的值。否则，它返回左边的值。

换句话说，`print(name **??** "N/A")`是:

# 4.检查一个单词在句子中是否存在

您可以使用简单的一行程序检查字符串中是否存在特定的单词:

```
**let** favorites = ["Banana", "Orange", "Apple"] 
**let** bag = "I packed some Beef, Potatoes, and a Banana"**let** hasFavorite = !favorites.filter({bag.contains($0)}).isEmptyprint(hasFavorite)
```

输出:

```
true
```

# 5.将所有数字相加得到一个数

例如，将 1 到 10 范围内的所有数字相加:

```
**let** sum = (1...10).reduce(0,+)print(sum)
```

输出:

```
55
```

# 额外收获:检查是否所有数组元素都满足一个条件

使用`allSatisfy`方法检查集合中的所有值是否都符合标准:

```
**let** ages = [19, 30, 43, 22, 67]**let** allAdults = ages.allSatisfy { $0 >= 18}print(allAdults)
```

输出:

```
true
```

# 结论

感谢阅读！我希望你觉得这很有用。

请在评论区让人们知道你最喜欢的 Swift 一行程序。

# 资源

 [## Swift.org

### 您可以将此页面上的资源用作 Swift 语言的文档。苹果公司拥有额外的资源…

swift.org](https://swift.org/documentation/) [](https://developer.apple.com/) [## 苹果开发者

### 现在是开发苹果平台的最佳时机。

developer.apple.com](https://developer.apple.com/)