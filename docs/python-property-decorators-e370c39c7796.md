# Python 中的@ property 它是什么，怎么用？

> 原文：<https://betterprogramming.pub/python-property-decorators-e370c39c7796>

## 了解如何在 Python 中创建 getters 和 setters

![](img/ceec4e7668951465a27a9551f430b8a8.png)

Michael Dziedzic 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Python 中的`@property`是一个属性装饰器。它使得像访问常规属性一样访问对象的方法成为可能:

```
mass.pounds() ---> mass.pounds
```

您可以使用属性装饰器为类的方法提供 getter、setter 和 deleter 功能。

# 简而言之就是装修工

在深入研究 Python 的属性装饰器之前，我们先来看看什么是装饰器。

在 Python 中，decorators 用`@`标记。

装饰器是扩展函数或类行为的一种方式。

简而言之，这段代码:

```
**def** extend(func):
   **return** func**@extend**
**def** some_func():
    **pass**
```

与此相同:

```
**def** extend(func):
    **return** func**def** some_func():
    **pass**some_func = extend(some_func)
```

它通过`extend()`函数的行为扩展了`some_func()`的行为。

了解更多关于装饰者的信息:

[](/understand-python-decorators-in-3-minutes-ec48fdc8e2cf) [## 3 分钟了解 Python Decorators

### 用 decorators 扩展方法和类的功能

better 编程. pub](/understand-python-decorators-in-3-minutes-ec48fdc8e2cf) 

# Python 中的@property

用`@property`标记类中的方法可以像访问属性一样访问对象的方法:

```
mass.pounds() ---> mass.pounds
```

## 例子

假设你有一个`Mass`类来存储质量，单位是千克和磅。

让我们测试一下它是如何工作的:

```
mass = Mass(100)print(mass.kilos)
print(mass.pounds)
```

输出:

```
100
220.5
```

让我们改变千克的数量，看看磅会发生什么变化:

```
mass.kilos = 500
print(mass.pounds)
```

输出:

```
220.5
```

英镑保持不变。这是因为它没有更新。

您可以通过创建一个`pounds()`方法并去掉`pounds`属性来解决这个问题。这种方法将公斤换算成磅:

现在，您可以更改千克数，磅数将始终保持最新:

```
mass = Mass(100)
print(mass.pounds())mass.kilos = 500
print(mass.pounds())
```

输出:

```
220.5
1102.5
```

这个改动之后，就再也不能不带括号调用`mass.pounds`了。这是因为`pounds()`现在是一个方法——而不是一个属性。

如果在其他地方使用了`Mass`类，这可能会破坏代码。

这就是一个`@property`装饰师的帮助。

如果您用`@property`标记`pounds()`方法，它允许您再次调用不带括号的`mass.pounds`:

让我们来测试一下:

```
mass = Mass(100)
print(mass.pounds)mass.kilos = 500
print(mass.pounds)
```

它非常有效。这个`pounds()`方法现在被称为 *getter* 方法。它不在对象中存储磅数，但是它可以通过将千克转换为磅来获得磅数。

经过这次改动，如果可以这样更新英镑岂不是很有意义？

```
mass.pounds = 1000
```

但是，如果您运行此代码，它会导致一个错误:

```
**Traceback (most recent call last):
  File "main.py", line 9, in <module>
    mass.pounds = 1000
AttributeError: can't set attribute**
```

错误提示您不能设置`pounds`属性。这是合理的，因为`pounds()`仍然是一个不在对象中存储磅数的方法。所以只能得到磅数而不能设置。

但是你可以改变公斤数。因此，您可以使用*设置器*方法通过`mass.pounds`间接改变千克数。

为了能够设置磅数，定义一个 setter 方法。这种方法:

*   接受新的磅数。
*   换算成千克。
*   将千克指定给质量对象。

要将它转换成代码，向`Mass`类添加一个名为`pounds()`的 setter 方法:

```
@pounds.setter
**def** pounds(self, pounds):
    **self**.kilos = pounds / 2.205
```

现在大众类看起来是这样的:

可以测试一下`Mass`类:

输出:

```
100
220.5498.8662131519274
1100.0
```

现在您有了访问和间接修改`pounds`的方法。

但是如果要删除磅调用`**del** mass.pounds`呢？这没有意义，因为`pounds`没有存储在对象中。

但是您可以为磅创建一个*删除器*方法，间接删除千克。

例如，向`Mass`类添加一个`pounds()`删除器方法:

```
@pounds.deleter
**def** pounds(**self**):
    print("Resetting mass!")
    **self**.kilos = 0
```

在这个删除器中，公斤数被设置为`0`，一条消息被打印到控制台。

下面是`Mass`类的最终版本:

它不存储磅数。相反，它有三个`pounds()`方法。一个用于间接:

*   变胖了。
*   设定重量。
*   删除英镑。

这些方法中的每一个都间接修改了公斤，但是在语法上与磅一起工作。

让我们创建一个最终的`Mass`对象并删除`pounds`来看看会发生什么:

```
mass = Mass(100)**del** mass.poundsprint(mass.kilos)
print(mass.pounds)
```

输出:

```
Resetting mass!
0
0.0
```

# 结论

`@property`在 Python 中可以像调用属性一样调用方法:

```
mass.pounds() ---> mass.pounds
```

您可以使用属性装饰器来间接修改对象的属性。为此，您需要定义 getter、setter 和 deleter 方法。

感谢阅读。编码快乐！

# 你可能会发现这些文章很有见地

[](/50-python-interview-questions-and-answers-5230fe2a0db6) [## 50 Python 面试问答

### Ace 您的下一次编码面试

better 编程. pub](/50-python-interview-questions-and-answers-5230fe2a0db6) [](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) [## 10 个有用的 Python 片段，让你像专业人士一样编写代码

### 我每天使用的有用的提示和技巧

better 编程. pub](/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145) 

# 参考

 [## 文件

### 欢迎光临！这是 Python 3.9.6 的文档。

docs.python.org](https://docs.python.org/3/)