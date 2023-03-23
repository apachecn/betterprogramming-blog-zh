# 给 Android 开发者的 8 个有用的 Kotlin 技巧

> 原文：<https://betterprogramming.pub/8-useful-kotlin-tips-for-android-developers-5a76bced73f5>

## 用这 8 个快速、初学者友好的编码技巧来提高你的 Kotlin 技能

![](img/8a4e6a3decb468c0ec5d64169f58353a.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我收集了一系列有用的 Kotlin 编程技巧和诀窍，以提高您的编码技能，打动您的朋友，并提高生产率。我希望你喜欢它！

# 1.交换两个没有辅助变量的变量

你知道你可以很容易地交换两个变量，而不需要第三个辅助变量吗？像这样做:

# 2.Elvis 操作员的零安全性

Elvis 操作符`?:`为您的代码提供了零安全性。如果某个东西是`null`，它可以用来返回一个默认值。这是语法:

```
maybeValue ?: guaranteedValue
```

如果`maybeValue`为空，则使用`guaranteedValue`。如果`maybeValue`不为空，则使用`?:`。例如:

输出:

```
Number is null
```

# 3.不循环填充列表

如果你想填写一个数字列表，你可能会使用一个循环，对吗？嗯，有一种更简洁的方法，只需一行代码就可以完成:

```
**val** list = DoubleArray(4) { 0.0 }.asList()println(list)
```

输出:

```
[0.0, 0.0, 0.0, 0.0]
```

**奖励:**如果您需要一个列表，其中每个数字都比前一个数字大两倍，您可以这样做:

```
**val** powList = List(5, { 2 * it })
println(powList)
```

输出:

```
[0, 2, 4, 6, 8]
```

# 4.使用 Let 检查可空属性

如果您有一个变量是一个`null`或者一个值，您可能会在对它做任何事情之前使用 If 语句来检查值是否是`null`。例如:

但是你知道你可以用`let`做同样的事情吗？：

```
num?.let { println("The number was not null.") }
```

# 5.简短的 If-Else

你可以稍微清理一下你的 if-else 语句。例如，您可以替换为:

通过使用一个`when` 语句，像这样:

或者，使用一个简短的 if-else 语句，可以将所有内容放在一行中。

```
println(**if** (num < 100) "Small" **else** "Big")
```

# 6.为函数提供默认参数

有时在函数中有一个缺省值参数是很有用的。这样，无论有没有参数，你都可以调用这个函数。让我们来看一个例子:

输出:

```
Hello, Michael.
Hello, everyone.
```

# 7.在字符串中添加变量

您可以使用`${}`在字符串之间嵌入数字。例如:

```
**val** nDogs = 5
**val** sentence = "I have ${nDogs} dogs."println(sentence)
```

输出:

```
I have 5 dogs.
```

# 8.使用`Apply`和`With`减少重复

假设您有一个具有默认属性的狗类:

您创建了一只新狗，并希望将默认属性更改为其他属性:

这很好，但是它引入了一些不必要的重复。当您修改`doggie`的两个属性时，您重复了两次名称`doggie`。为了避免这种情况，您可以使用`apply`或`with`函数，如下所示:

# 结论

感谢阅读。我希望这些建议对你有用。

# 资源

 [## 科特林文件|科特林

### 您可以使用在线编辑器开始使用 Kotlin。如果您已经有一个 IDE 或者准备安装一个…

kotlinlang.org](https://kotlinlang.org/docs/home.html)