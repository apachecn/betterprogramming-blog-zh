# 斯威夫特的 NumPy？

> 原文：<https://betterprogramming.pub/numpy-in-swift-fef63282443>

## Swift Accelerate 和 simd 简介

![](img/30fe90a694598f08f66b7666c0df2e8b.png)

克里斯·利维拉尼在 [Unsplash](https://unsplash.com/s/photos/data-science?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当使用像 Python 这样的语言进行任何种类的数值计算或 ML 时，对于这个问题有许多简单的答案*我应该使用哪个库？*许多人会说 *NumPy，*而其他人可能会说 *PyTorch* 是因为它的 GPU 能力——但是对于 Python 有很多显而易见的答案。

现在让我们转到 Swift。你马上想到什么库是和 Swift 配对的好选择？如果你不得不停顿下来，什么也想不出来，那么你就处于我第一次被提出这个问题时的状态。

仅仅使用数组和列表并不是一个好的解决方案，因为虽然数组是高效的结构，但是您需要构建各种各样的方法来进行乘法、加法、数值计算以及几乎所有其他的事情。因此，很明显，这不是一个完美的解决方案——我们应该使用什么呢？

# 输入加速和 SIMD

如果你从未听说过这些库，你可能不会孤单，所以让我们从这两个库是什么开始。

[Accelerate](https://developer.apple.com/documentation/accelerate) 库被苹果描述为进行“大规模数学计算和图像计算，针对高性能和低能耗进行了优化”——太棒了！我将记下 Accelerate 中的几个关键库以及一些密切相关的库(包括 simd)。

## **内加速**

*   [BNNS](https://developer.apple.com/documentation/accelerate/bnns) 用于神经网络的构建和训练
*   [vImage](https://developer.apple.com/documentation/accelerate/vimage) 包括许多图像处理功能，用于插值、转换和操作
*   vDSP 用于数字信号处理
*   [vForce](https://developer.apple.com/documentation/accelerate/veclib/vforce) 用于对矢量执行功能
*   [稀疏解算器](https://developer.apple.com/documentation/accelerate/sparse_solvers)对稀疏(和密集)矩阵执行线性代数运算

那是一吨！许多人不知道 Swift 可以执行这些领域内的任务，但苹果已经为所有这些领域创建了第一方库。

## **外部加速**

有几个相关的库，但是我主要关注的是 [simd](https://developer.apple.com/documentation/accelerate/simd) 。苹果将 simd 库定义为“一个对小向量和矩阵进行计算的模块。”

如果你认为这听起来非常类似于小向量和矩阵的 NumPy，那你就完全正确了。当我不得不在 Swift 中编写一些算法和数据处理代码时，我最终使用的库是 simd，它完全符合我的所有需求。

simd 也不仅仅是 iOS 或 Swift 特有的东西— *simd* 代表*单指令、多数据。*这是一种有效的硬件优化，允许我们一次执行多个操作。这使我们能够在数组或其他数据类型上获得比普通计算更大的性能提升。

让我们来看几个将 simd 用于向量和矩阵数学的例子。

## SIMD 向量示例

让我们看一个制作向量并对其执行一些基本操作的基本例子。

下面是一个基本 3D 矢量的实例:

```
let vector = SIMD3<Double>(0, 0, 0)
```

我们正在制作一个 3D 向量，这就是为什么我们在`SIMD3`中写了`3`，并且类型是 double(因此有了`<Double>`)。

我们可以进行基本的标量运算和基本的向量乘法，如下所示:

```
let xz = SIMD3<Double>(1, 0, 1)
let scaled = unit * 5
let xy = SIMD3<Double>(1, 1, 0)
let newVector = scaled * xy
```

如你所见，我们可以用常数标量乘向量，也可以用向量乘向量。

您还可以利用库从向量中提取信息。例如，要获得向量的大小，您可以编写以下内容:

```
let vector = SIMD3<Double>(5, 6.5, 9.7)
let mag = simd_length(vector)
```

在上面的代码中，`mag`是矢量的大小。

可能有一些函数可以满足你对向量的大部分需求——例如，取绝对值、最高位的函数，以及许多其他内置的函数。

## simd 矩阵示例

与所有向量示例类似，您也可以在 simd 中用矩阵完成基本上所有您想要的事情。

让我们先演练初始化一个矩阵。

```
let matrix = simd_double3x3(5)
```

上面的例子创建了一个`3x3`矩阵，所有值都被初始化为`5`。有几种不同的方法来实例化一个矩阵，包括传入一个标量，什么都不传入(所有的零都放在矩阵中)，或者传入每一列的向量。

让我们开始执行一些不同的矩阵运算。

```
let matrix = simd_double3x3(5)
let vector = SIMD3<Double>(1, 2, 3)
let result = vector * matrix
```

这里我们将向量(一个`1x3`矩阵)乘以矩阵(`3x3`)。

您还可以获得矩阵的逆矩阵，如下所示:

```
let matrix = simd_double3x3(1)
let inverse = matrix.inverse
```

您也可以使用内置函数获得矩阵的转置:

```
let matrix = simd_double3x3(5)
let transpose = simd_transpose(matrix)
```

不幸的是，我认为 simd 中缺少的一个主要东西是矩阵旋转的内置函数，但苹果公司提供了一个很好的起点。

## simd 四元数

我知道你们大多数人可能没有用过四元数(我知道我直到最近才用过！)，但是除了矩阵和向量库之外，simd 还提供了一个真正全面的四元数库。

```
let quat = simd_quatd(ix: 1, iy: 1, iz: 1, r: 0.5)
```

在上面的例子中，我们制作了一个简单的 double 类型的四元数(为了使它成为浮点数，我们将使用`simd_quatf`)。我们可以用这个四元数来实例化一个矩阵，如下所示:

```
let matrix = simd_double3x3(quat)
```

对于各种各样的物理和计算机图形应用程序来说，还有许多其他的功能[在这里](https://developer.apple.com/documentation/accelerate/working_with_quaternions)。

我希望这些例子和这些库的介绍有助于改进您的 Swift 代码。编码快乐！