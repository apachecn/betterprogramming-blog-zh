# Python 3.9 带来了一些令人兴奋的新特性

> 原文：<https://betterprogramming.pub/python-3-9-brings-some-exciting-features-a09a02c0fe22>

## 新的更新正在进行中

![](img/12e7ef975275adb440c2fda7e6b110b2.png)

由[麦克斯韦·纳尔逊](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 的新版本来了！很快，它就会出现在我们的机器上。Python 已经发布了 Python 3.9 的测试版(3.9.0b3)，并将很快发布 Python 3.9 的完整版。

新版本引入了一些令人兴奋的功能和新模块。和他们一起动手练习会很有趣。在此之前，让我们了解一下这些特性和模块的功能。

# 引入新功能

## 字典合并和更新运算符

Python 3.9 在 dict 类中引入了合并(|)和更新(|=)操作符。如果你有两个字典 **x** 和 **y，**你现在可以使用这些操作符来合并和更新它们。

```
x = {1: "one", 2: "two"}
y = {3: "three"}
```

您可以使用|来合并这两个词典。

```
z=x|y
print(z)**[Output]:** {1: "one", 2: "two", 3: "three"}
```

如果两个字典有一个公共键，那么输出将显示第二个键-值对。

```
x = {1: "one", 2: "two",3: "3"}
y = {3: "three"}z=x|y
print(z)**[Output]:** {1: "one", 2: "two", 3: "three"}
```

为了更新字典，可以使用下面的操作符。

```
x = {1: "one", 2: "three"}
y = {2: "two"}
x|=y
print(x)**[Output]:** {1: "one", 2: "two"}
```

## removeprefix()和 removesuffix()字符串方法

在 Python 的 str 类中，新的更新引入了新的`removeprefix()` 和`removesuffix()`方法。

您可以使用`removeprefix()`方法删除任何字符串的前缀。

```
'TestHook'.removeprefix('Test')
'Hook'
```

如果字符串不是以输入前缀开头，将返回原始字符串的副本。

```
'BaseTestCase'.removeprefix('Test')
'BaseTestCase'
```

如果字符串以输入后缀结尾，输出将类似于`string[:-len(suffix)]`。

```
'MiscTests'.removesuffix('Tests')
'Misc' 
```

如果输入后缀为空或者字符串不以它结尾，则输出将是原始字符串的副本。

```
'TmpDirMixin'.removesuffix('Tests')
'TmpDirMixin'
```

## 新解析器

Python 3.9 使用了一种新的解析器，这是一种基于 PEG 的解析器。之前 Python 用的是 LL(1)。在构建语言的新特性时，PEG 比 LL(1)更灵活。官方文档称，这种灵活性将在 Python 3.10 和更高版本中出现。

## 类型提示

Python 动态地为变量指定数据类型。对于数据类型的静态分配，使用类型提示。这是在 Python 3.5 中引入的。

您现在可以使用内置的集合类型(`list`和`dict`)作为泛型类型。以前，您必须从`typing`导入资本类型(`List`或`Dict`)。

```
def greet_all(names: list[str]) -> None:
    for name in names:
        print("Hello", name)
```

现在不需要从`typing.List`调用`List`。

# 欢迎新图书馆

## zoneinfo

新的 Python 更新引入了一个新的模块`zoneinfo` **。它将为标准图书馆带来对 IANA 时区数据库的支持。**

```
**>>> from** **zoneinfo** **import** ZoneInfo
**>>> from** **datetime** **import** datetime

>>> dt = datetime(2020, 10, 31, 12, tzinfo=ZoneInfo("America/Los_Angeles"))>>> print(dt)
2020-10-31 12:00:00-07:00>>> dt.tzname()
'PDT'
```

我们创建了一个`datetime` 对象，并在该对象中给出了一个时区参数。如果您现在使用`dt.tzname()` *，*查找时区，输出将是`PDT` *。*

## 图表库

如果你想对图形进行拓扑排序，你可以在你的代码中添加`graphlib`模块。

# 参考

[Python 3.9 新特性](https://docs.python.org/3.9/whatsnew/3.9.html)

[图形库模块](https://docs.python.org/3.9/library/graphlib.html#module-graphlib)

和平！