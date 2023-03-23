# Python 中你想避免的 3 个常见错误

> 原文：<https://betterprogramming.pub/3-common-mistakes-youd-want-to-avoid-in-python-6210f4d56310>

## 三个问题及其解决方法

![](img/50b8b3fbf3b36983140c2eaf5740a051.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这篇文章中，我将谈论三个你不想犯的常见错误！

# 问题:默认可变对象

嘿，等等 Python 中的可变对象是什么？可变对象是您可以更改的对象。List、set 和 dict 是可变对象的例子。

为了便于论证，我将定义一个非常简单且无用的函数。

```
def add(x, y=[]):
    y.append(x)
    return y
```

在这个`add` 函数中，我们默认将`y`变量设置为一个列表(可变对象)。让我们使用这个函数。

```
def add(x, y=[]):
    y.append(x)
    return y

print(add('halil'))
print(add('yıldırım'))
```

输出是:

`['halil']
['halil', 'yıldırım']`

这是怎么回事？它在覆盖同一个列表。当我们第一次调用`add('halil')`时，默认参数变成了`y=['halil']`，并开始覆盖相同的列表。让我们看看:

```
def add(x, y=[]):
    y.append(x)
    return y

add('halil')
print(add.__defaults__)
add('yıldırım')
print(add.__defaults__)
```

输出是:

`(['halil'],)
(['halil', 'yıldırım'],)`

如您所见，我们正在覆盖默认列表。

# 解决方案:默认可变对象

您应该传递`None`，而不是传递可变对象作为默认参数。示例:

```
def add(x, y=None):
    if y is None:
        y = list()
        y.append(x)
    else:
        y.append(x)
    return y

print(add('halil'))
print(add('yıldırım'))
```

输出:

`['halil']
['yıldırım']`

# 问题:复制

让我们用 Python 创建一个字典。

```
a = {'a': 5, 'b': 4, 'c': 8}
```

让我们将`b`设置为`a`:

```
a = {'a': 5, 'b': 4, 'c': 8}
b = a
```

让我们用键`a`删除第一个元素。

```
del b['a']
```

我们写 a，b，看看是怎么回事。

```
a = {'a': 5, 'b': 4, 'c': 8}
b = a
del b['a']
print(b)
print(a)
```

输出:

`{'b': 4, 'c': 8}
{'b': 4, 'c': 8}`

这是怎么回事？我们只是想删除 b 的第一个元素，而不是 a …

# 解决方案:复制

你说 b = a，基本就是说`b`和`a`指向同一个对象。如果两者都指向同一个对象，那么用`b`或者`a`做一些操作都没关系。两者都会影响这个`{'a': 5, 'b': 4, 'c': 8}`对象。

一个解决方案:

```
import copy
a = {'a': 5, 'b': 4, 'c': 8}
b = copy.copy(a)
del b['a']
print(b)
print(a)
```

输出:

`{'b': 4, 'c': 8}
{'a': 5, 'b': 4, 'c': 8}`

# 问题和解决方案:命名您的文件

不要用你要使用的库来命名你的 python 文件。

例如，如果你把你的文件命名为`random.py` **，**你的程序会对你要做的事情感到困惑。

```
import random

print(random.random())
```

输出:

`File "C:\Users\HalilibrahimYıldırım\Desktop\medium\Python\random.py", line 3, in <module>
print(random.random())
TypeError: 'module' object is not callable`

也订阅我的 youtube 频道，很快就会有惊人的视频[https://www.youtube.com/channel/UC3G_BHW2Sn8EN5ivWE6XcYQ](https://www.youtube.com/channel/UC3G_BHW2Sn8EN5ivWE6XcYQ/featured)

检查我的 [GitHub 呼吸系统](https://github.com/halilibrahim95/python-works)并启动它。你可以找到有用的 python 代码，我会尽可能多地更新这些代码。