# 使用 Cython 显著提高 Python 代码的速度

> 原文：<https://betterprogramming.pub/make-your-python-code-dramatically-faster-with-cython-2a307253234b>

## 只需几个步骤就可以将 C 语言引入 Python 代码！

![](img/6176bc1a7dcbc7b140d9cad9e1d7a3a2.png)

[Guillaume Jaillet](https://unsplash.com/@i_am_g?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你肯定听过很多人抱怨 Python 太慢了。我看到人们仅在性能方面将 Python 与 C 进行比较，但他们不会在快速开发的背景下进行比较。Python 开发时间这么快，是因为不用处理指针、内存管理等。

这也是我们公司使用 Python 的原因之一。在大多数情况下，开发时间比性能更重要。Python 有一个很好的社区和很好的库，可以满足你大部分时间的需求。也就是说，我将在本文中讨论 Cython，以提高 Python 代码的速度。让我们开始吧！

# cy thon:Python 的 C 扩展

你喜欢 Python 那样的简单语法，C 那样的高性能吗？Cython 是你想要的。可以使用 Cython 为 Python 编写 C 扩展。您的代码将被翻译成优化的 C/C++代码。它将给你带来高性能，你可以在你的 Python 项目中使用它。

## 安装 Cython

请注意，您将需要一个 C 编译器。你可以基于你的操作系统得到一个。然后你需要像下面这样安装 Cython。

```
pip install Cython
```

## 受益于静态类型声明

您可以在变量和函数中添加静态类型声明。它会给你更好的表现。你可以使用所有的 C 类型来声明，比如`int`、`float`、`double`等等。

## 在 Cython 中声明**变量**

```
**cdef** int i = 10
```

如果您从未使用过这种语法，这基本上意味着您正在声明一个变量`i`，它是一个整数。

## 在 Cython 中声明一个**函数**

```
**cdef** int square(int x):
    **return** x ** 2
```

这基本上意味着`square`函数接受一个整数参数`x`并返回一个整数值`x**2`，它是`x`的平方。

`cdef`当您导入模块时，声明的函数将对您的 Python 代码不可见。为了使它可见，你需要使用`cpdef`而不是*。*

## 在 Python 中计算阶乘

让我们编写一个 Python 函数来计算给定整数的阶乘。

# 使用 Cython 加快速度

## 创建一个 pyx 文件

`cpdef long fastfactorial`:这个函数将返回一个长值，所以我们通过在函数名`fastfactorial`前加上`long`来声明它为`long`。

`long n`:我们期待`n`成为`long`。

## 创建安装文件

现在我们需要创建一个`setup.py`文件来将 Cython 翻译成 c。

## 编译代码

为了执行这个编译，我们将运行这个命令。

`--inplace`:该选项在您的工作目录中创建一个共享目标文件。

```
python3 setup.py build_ext --inplace
```

恭喜你！这是你做的。

## Co **比较**

我们来对比一下！我将把执行时间和打印功能放在一起，让您看看不同之处。

看区别:快了差不多 **88 倍**！

# 结论

只需轻轻一点，您就可以让 Python 代码运行得更快。这是一个温和的介绍。您可以根据自己的需要进一步完善它，使它变得更加复杂。注意，示例是为了说明的目的；它们可以被优化。