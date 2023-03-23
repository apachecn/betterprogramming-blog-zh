# 如何在 Python 中合并两个列表

> 原文：<https://betterprogramming.pub/how-to-merge-two-lists-in-python-bc39ce1858fb>

## 使用列表串联或 splat 运算符

![](img/f64eb757d39bba7d38e62660849b8286.png)

照片由[约翰·马可·阿诺](https://unsplash.com/@johnmarkarnold?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data-set?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

当您想要获取两个数据集(在我们的例子中是一对列表)时，将它们合并成一个序列是一个常见的任务。这也是一项任务，可以根据不同的情况以不同的方式完成。

我们将演示两种不同的方法来获取两个现有的列表，并创建第三个列表，它是现值的组合。

# 使用列表串联

第一种方法是将两个列表连接在一起。concatenate 这个术语的意思是将事物连接或链接在一起。我们经常在字符串中看到这种情况。

例如，将名和姓连接在一起，中间有一个空格:`first_name + " " + last_name`。在这个例子中，加号是我们的连接操作符。

虽然并非在所有语言中都是如此，但在 Python 中，加号也允许将列表连接起来。

```
a = [1,2,3,4,5]
b = [6,7,8,9,0]c = a + b
print(c) # [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
```

# 使用 Splat 运算符

串联的一种替代方法是在列表理解中使用 splat 操作符`*`。

你可能见过在`*args`或`**kwargs`中使用的星号——那些不是特殊的 Python 变量，它们是带有 splat 操作符的[标准命名变量。](https://medium.com/better-programming/what-are-args-and-kwargs-in-python-6aaf9e3cad73)

该操作符将“解包”复杂的数据类型，如列表或字典。为了实现我们的合并列表，要合并的列表将在一个列表理解中被“分散”。

```
a = [1,2,3,4,5]
b = [6,7,8,9,0]c = [*a, *b]
print(c) # a = [starter,2,3,4,5]
b = [6,7,8,9,0]c = [*a, *b]
print(c) # [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
```

# 那么，哪个更好呢？

嗯，那要看情况。就代码的简单性而言，串联操作符占了上风。没有比在其他数据类型中也很常见的单个操作符更清楚的了。

然而，对于少量的额外语法，使用列表理解作为高级需求，结果是 splat 操作符，将产生显著更高的灵活性和控制。

例如，如果我们也有一个想要添加到合并列表中的文字呢？

```
a = [1,2,3]
b = [5,6,7]c = a + 4 + b
print(c) # TypeError: can only concatenate list (not "int") to list
```

我们不能轻易加入我们的合并。事实上，字面上的`4`需要被包装成一个列表，这种技术才能工作。

相反，有了列表理解和 splat 操作符，我们就没有问题了。

```
a = [1,2,3]
b = [5,6,7]c = [*a, 4, *b]
print(c) # [1, 2, 3, 4, 5, 6, 7]
```

你更喜欢哪种技术，为什么？加入下面的对话，留下你的评论和反馈！