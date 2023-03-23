# 如何在 Python 中安全地创建嵌套目录

> 原文：<https://betterprogramming.pub/how-to-safely-create-nested-directories-in-python-333a0ff40db9>

## 使用文件系统可能很棘手，因为存在不确定因素

![](img/ebca265b4849c86765e9b76bb6ba6af3.png)

里卡多·克鲁兹在 [Unsplash](https://unsplash.com/s/photos/directory?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

创建嵌套目录(尤其是从绝对路径创建)是不稳定的，因为该命令的成功依赖于嵌套在其中的父目录。

`pathlib`库及其`.mkdir()`方法提供了一种安全创建嵌套目录的技术。

我们从从`pathlib`导入`Path`类开始。

```
from pathlib import Path
```

创建`Path`对象时，包含要创建的目录路径。

```
p = Path("/nested/directory")
```

接下来，我们将使用`.mkdir()`方法来创建目录。

```
p.mkdir()
```

这将工作，假设目录不存在。

然而，我们需要一种安全的机制来创建目录，而不会导致异常和错误。为此，让我们给`mkdir()`添加`exist_ok`标志，这样如果目录已经存在，它就不会引发`FileExistsError` 。

```
p.mkdir(exist_ok=True)
```

如果我们的目录也需要父目录呢？

类似于`exist_ok`标志，`parents`标志将修改默认行为，根据需要递归地创建目录。

```
p.mkdir(exists_ok=True, parents=True)
```

如果我们想把所有东西打包成一个整齐的函数，我们可以这样做:

```
def makeDirectory(path_to_directory):
   p = Path(path_to_directory)
   p.mkdir(exists_ok=True, parents=True)
```