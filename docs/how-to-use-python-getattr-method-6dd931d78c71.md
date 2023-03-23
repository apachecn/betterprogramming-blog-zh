# 如何使用 Python getattr()方法

> 原文：<https://betterprogramming.pub/how-to-use-python-getattr-method-6dd931d78c71>

## 无论发生什么，都获取一个实例拥有的任何内容

![](img/c242e015eab362a8f451b56edb0e4bd4.png)

照片由[埃里克·维加](https://unsplash.com/@cocoroto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`getattr()`是 Python 中最酷的内置函数之一。它需要三个参数:

*   一个物体
*   对象属性的名称，但采用字符串格式
*   默认值*(这是可选的)*

它返回:

*   属性的值

让我们定义一个`Book`类作为例子

并创建一个实例:

```
book = Book("Moby Dick", "Herman Melville", 1851)
```

现在，我们可以访问`book`实例的属性。没有什么比使用点符号更自然的了。

```
>>> book.title
'Moby Dick'
```

我们可以使用`getattr()`功能做同样的事情:

```
>>> **getattr**(book, 'title')
'Moby Dick'
```

所以`book.title`和`getattr(book, 'title')`完全是**和**一样的东西！它们是相同的，除了:

*   `title`属性作为字符串(`'title'`)传递给`getattr()`函数。
*   您可以将默认值作为参数传递。

例如:

```
>>> book.language
```

会引发一个错误，因为`Book`类没有这样的属性。

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: 'Book' object has no attribute 'language'
```

相反，我们可以做:

```
>>> **getattr**(book, 'language', 'English')
'English'
```

请记住，如果没有默认值，也会引发错误。

```
>>> **getattr**(book, 'language')
```

输出:

```
Traceback (most recent call last):
  File "<input>", line 1, in <module>
AttributeError: 'Book' object has no attribute 'language'
```

因为`getattr()`和点符号是等价的如果你不传递默认值的话。

如果你喜欢冒险(像我一样)，你可能想知道是否也可以访问一个类的方法。让我们看看:

```
>>> **getattr**(book, 'describe')
```

输出:

```
<bound method Book.describe of <__main__.Book object at 0x103cd7a0>>
```

它返回`describe()`方法而不调用它。记住函数也是对象，你可以把它们赋给变量。所以我们可以通过在末尾添加括号来直接调用它

```
>>> getattr(book, 'describe')**()**
Moby Dick (1851) was published by Herman Melville.
```

或者我们可以存储这个值，以后再调用它。

```
>>> desc = getattr(book, 'describe')
>>> desc**()**
Moby Dick (1851) was published by Herman Melville.
```

到目前为止一切顺利！你可能会问:因为我们已经知道一个类的内容，所以访问一个属性不是很奇怪吗？

事实上，如果您需要在程序已经运行时决定使用哪个属性，那么`getattr()`就派上了用场。例如，在 web 应用程序中，您可能需要根据即将到来的请求动态地获取属性。或者从用户那里获得属性的名称。

假设您编写了一个程序，该程序接收用户的输入并打印出所需属性`book`的值。

```
attr = input()
print(getattr(book, attr))
```

假设用户写下`pub_year`作为输入。那么输出是:

```
1851
```

上面的代码相当于:

```
attr = input()if attr == 'title':
    print(book.title)
elif attr == 'author':
    print(book.author)
elif attr == 'pub_year':
    print(book.pub_year)
```

它确实缩短了我们的代码，对吗？

让我们看另一个例子。现在，您可以从 API 动态获得允许的 HTTP 方法，并根据允许的方法(POST 或 PUT)通过[请求库](https://docs.python-requests.org/en/latest/)发送适当的 HTTP 请求。当然，你需要使用`getattr()`。

这相当于:

如你所见，`getattr()` 让我们的生活更轻松，减少代码重复。

如果您处于类似的情况，并且您的对象有许多属性，使用该函数可以使您不必编写许多 if-elif 子句。

如果你真的因为没有使用`getattr()` 而打算写很长的代码行，并且你认为通过一次又一次的复制粘贴就成功了。嗯……我有坏消息要告诉你。大多数编程错误都是由复制粘贴操作引起的。所以这个函数也保护你免受复制粘贴的潜在错误。

总之，如果你知道属性的名字，总是使用点符号。然而，当属性的名称作为程序流给出时，您应该使用`getattr()`。

感谢您阅读我的文章。如果你喜欢你所读到的，看看下面这个故事:

[](/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569) [## 在 Python 中停止使用“或”来检查多个条件

### 还有更精密的解决方案！

better 编程. pub](/stop-using-or-to-check-multiple-conditions-in-python-404d31f2b569)