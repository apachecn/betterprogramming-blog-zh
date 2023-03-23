# Python 中的海象算子是什么？

> 原文：<https://betterprogramming.pub/what-is-the-walrus-operator-in-python-5846eaeb9d95>

## 了解如何使用 3.8 版中发布的新操作符精简代码

![](img/cc7b9b91227977c90de7ceb766705d72.png)

一个新的运算符`**:=**`，被愉快地命名为*海象运算符*——看到眼睛和长牙——被称为赋值表达式。

walrus 运算符的目的是当赋值和表达式都使用类似的语句时，合并赋值语句和布尔表达式。

# 之前和之后:实现 Walrus 运算符

让我们看一个简单的用例，以及我们如何实现 walrus 操作符来提高代码的效率。

我们将检查给定列表的长度，并打印包含该长度的错误消息。

```
my_list = [1,2,3,4,5]if len(my_list) > 3:
   print(f"The list is too long with {len(my_list)} elements")
```

如果您对 print 语句中字符串前的`f`不确定，那就称为格式字符串文字，简称 f 字符串。

现在，walrus 操作符将消除两次调用`len()`函数。

```
my_list = [1,2,3,4,5]if (n := len(my_list)) > 3:
   print(f"The list is too long with {n} elements")
```

操作符的类型(赋值表达式)应该给你一个提示，告诉你这里发生了什么。最内部的语句是赋值语句`n = len(my_list)`，然后`n`的值被用在外部表达式中。

# 不要忘记括号

Python 并不经常需要括号，但是在这种情况下，括号对于语句正确求值是至关重要的。要查看忘记括号的结果，让我们通过打印`n`来比较每一个。

```
my_list = [1,2,3,4,5]if (n := len(my_list)) > 3:
   print(n) # 5 if n := len(my_list) > 3:
   print(n) # True
```

注意，如果没有括号，赋值的是`len(my_list) > 3`的值。

# 你将如何使用这个？

walrus 操作符允许使用复合赋值表达式，这提供了许多新的可能性。

这个操作符的引入也受到了相当多的批评，但对我来说，它只是工具箱中的另一个工具，可以在适当的时候使用。

那么，你会使用海象运营商吗？如果是，你将如何使用它？