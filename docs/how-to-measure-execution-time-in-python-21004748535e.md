# 如何在 Python 中测量执行时间

> 原文：<https://betterprogramming.pub/how-to-measure-execution-time-in-python-21004748535e>

## 利用 Python 内置的 timeit 模块来检查代码的性能

![](img/c5a78898ebceece1fbb02d26ec0c22f8.png)

由 [Aron 视觉效果](https://unsplash.com/@aronvisuals?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/time?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

通过阅读本文，您将了解如何在 Python 中测量代码的性能和执行时间。根据[官方文件](https://docs.python.org/3/library/timeit.html),`timeit`模块:

> “…提供了一种简单的方法来为小部分 Python 代码计时。它既有命令行界面，也有可调用界面。它避免了许多测量执行时间的常见陷阱。”

让我们进入下一部分，开始测试 timeit 模块。

# 命令行界面

`timeit`有自己的命令行界面，允许我们测试一些简单的代码片段。让我们试着测量一个列表理解的执行时间:

```
python -m timeit "'-'.join([str(i) for i in range(100)])"
```

您应该得到以下输出。执行时间和循环次数可能因机器而异:

```
20000 loops, best of 5: 19.6 usec per loop
```

您可以指定以下选项:

*   `-n`——处决人数
*   `-r` —计时器的重复次数。默认值为五。
*   `-s` —最初将执行一次的初始语句。
*   `-p` —设置为测量过程时间，而不是挂钟时间。
*   `-u` —输出的时间单位。您可以选择 nsec、usec、msec 或 sec。
*   `-v` —打印原始计时结果。

让我们尝试在同一语句中添加一些选项:

```
python -m timeit -n 20000 -u sec -v "'-'.join([str(i) for i in range(100)])"
```

您应该在命令提示符下得到`raw times`输出。根据您的机器，时间会有所不同。有五次，因为`repeat`参数被默认为五次。

```
raw times: 0.395 sec, 0.392 sec, 0.383 sec, 0.384 sec, 0.383 sec
```

当您测试简单的代码片段时，命令行界面非常方便。如果您正在处理无法用几行代码表达的代码，强烈建议使用 Python 接口。

让我们继续下一节，开始写一些 Python 代码。

# Python 界面

## 导入

在 Python 文件的顶部添加以下导入声明:

```
import timeit
```

## 时间

我们将使用`timeit`函数复制之前测试过的相同代码片段。它接受以下输入参数:

*   `stmt` —包含测试用例代码片段的字符串。
*   `setup` —将被执行一次的初始代码。
*   `timer` —计时器实例。它默认为 `time.perf_counter()`，返回一个浮点数。
*   `number` —要执行的执行次数。默认值为 1，000，000。
*   `globals` —指定在其中执行代码的名称空间。

大多数时候，您将只使用`stmt`、`setup`和`number`参数。在 Python 文件中追加以下代码:

```
result = timeit.timeit("'-'.join([str(i) for i in range(100)])", number=20000)
```

然后，打印出结果:

```
print(result)
```

运行它时，您应该会看到以下结果。根据您机器的规格，时间可能会有所不同:

```
0.4323697
```

## 重复

如果需要多次重复相同的测试，您可以使用`repeat`功能。它接受与`timeit`相同的参数，外加一个`repeat`参数。默认值为五:

```
result = timeit.repeat("'-'.join([str(i) for i in range(100)])", number=20000)print(result)
```

当您运行 Python 文件时，您应该会在控制台上看到一个不同小数的浮点数列表。

## 功能

如果您正在测试包含在一个函数中的代码，您可以将该函数包装在一个多块字符串中，并将其作为一个`stmt`输入参数传递。但是，更好的解决方案是将其作为设置参数导入。让我们在函数中定义相同的代码片段:

```
def test():
    return '-'.join([str(i) for i in range(100)])
```

下一步是从`main`定义中导入它:

```
if __name__ == '__main__':
    result = timeit.timeit("test()", setup="from __main__ import test", number=20000) print(result)
```

当您运行它时，您应该得到大致相同的执行时间。这种解决方案的一个大问题是，如果存在多个函数，您将很难编写 import 语句。

## 全局

另一个选项是设置`globals`名称空间，这将导致代码在当前的全局名称空间中执行。这个解决方案要方便得多，并且不会影响任何底层代码:

```
result = timeit.timeit("test()", number=20000, globals=globals())
print(result)
```

# 结论

让我们回顾一下你今天所学的内容。

我们首先简要解释了 timeit 模块提供的命令行界面。我们在一个简单的列表理解代码片段上测试了它。

之后，我们尝试了 Python 接口，它带有两个强大的内置函数:`timeit`和`repeat`。我们还探索了如何通过`setup`或`global`参数导入要执行的功能。

感谢阅读这篇文章。希望下一部能再见到你！