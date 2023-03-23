# Python 中的上下文管理器

> 原文：<https://betterprogramming.pub/context-managers-in-python-dbfaf568092>

![](img/7f8366b4983a584bdd39f5141d0b59d8.png)

泰勒·达维在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在这篇文章中，我们将深入研究 Python 中的上下文管理器。我会长话短说——少说话，多做事！我希望你喜欢它。

# 为什么是上下文管理器？

上下文管理器用于归还资源。我的意思是:

```
f = open('file.txt', 'w')
f.write('Something to write')
```

我们打开了一个名为`file.txt`的文件，并编写了它。等等——我们没有关闭这个文件，所以我们仍然可以使用`f`实例:

```
f = open('file.txt', 'w')
f.write('Something to write')
print('another work')
f.write('I can still write to file.txt')
```

这是一个问题——资源不是无限的，所以我们用完后必须归还。我们是这样做的:

```
f = open('file.txt', 'w')
f.write('Something to write')
f.close()
```

有了`f.close()`，我们关闭了`file.txt`，所以不能再使用这个`f`:

```
f = open('file.txt', 'w')
f.write('Something to write')
f.close()
print(f.closed) # returns True
```

`print(f.closed)`会打印`True`因为`f`关闭，感谢`f.close()`。

# 我们如何使用上下文管理器？

用一个`with`语句。让我给你举个例子:

```
with open('file.txt', 'w') as f:
    f.write('Something to write')
```

但是等等——哪里有`f.close()`？不要担心，`with`语句会处理这个问题并关闭文件:

```
with open('file.txt', 'w') as f:
    f.write('Something to write')
print(f.closed)
```

看——我告诉过你`print(f.closed)`会打印`True`！

# 太好了！那么，我该怎么写我的呢？

让我来帮你创造你自己的。有两种方法可以做到这一点——我更喜欢用`class`来做，但我会向你展示这两种方法。

## 方法一

```
class OpenFile:
    def __init__(self, file, mode):
        self.file = file
        self.mode = mode

    def __enter__(self):
        self.f = open(self.file, self.mode)
        return self.f

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.f.close()
```

## **方法二**

```
from contextlib import contextmanager

@contextmanager
def OpenFile(file, mode):
    f = open(file, mode)
    yield f
    f.close()
```

## **试试看！**

```
with OpenFile('file.txt', 'w') as f:
    f.write('Something to write to mine')
print(f.closed)
```