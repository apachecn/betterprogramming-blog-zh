# 加速器和并行编程简介

> 原文：<https://betterprogramming.pub/an-accelerated-and-parallel-programming-introduction-2f91420fb4f0>

## 加速器的重要性和使用都在持续增长，这对开发者来说意味着什么？

![](img/767aae0434ecece614a86221a7a89428.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

计算机是达到目的的一种手段。它们让我们能够更快地解决复杂的问题，提供在全球范围内存储和检索信息的能力，为机器人、自动驾驶汽车(某种程度上)和人工智能等卓越的技术提供支柱，并有望改善地球上每个人的生活。随着要解决的问题变得越来越复杂，计算机体系结构、编程语言和编程模型也在不断发展。这导致了硬件加速器和特定领域编程模型的增长。

来自加州大学伯克利分校的 David Patterson 教授(我大学时所有计算机架构书籍的作者)已经[广泛地谈论了特定领域架构](https://hipporeads.com/uc-berkeleys-david-a-patterson-sees-the-future-after-moores-law/)和加速器。今天，当我们谈论硬件加速器时，我们经常谈论 GPU。然而，出现了无数不同类型的加速器来解决各种问题，包括深度学习和人工智能，这些加速器利用专门设计的硬件来执行大规模矩阵运算，这是 DL 工作负载的核心。

此外，传统 CPU 中内置了硬件加速技术，如英特尔高级矢量扩展指令集(AVX)和英特尔高级矩阵扩展指令集(AMX)。

随着新加速器的出现，如何为该加速器编程总是一个挑战。目前可用的大多数加速器都基于并行执行，因此也是某种形式的并行编程。

这是我们将讨论并行编程以及在现代加速器上实现并行编程的各种方法的系列文章的第一篇。

# 并行性概述

并行编程是我们如何编写代码来表达任何代码/算法中的并行性，以使其在一个加速器或多个 CPU 上运行。但是什么是并行呢？

并行性是指程序的一部分可以与程序的另一部分同时运行。通常，我们将其分为两类:任务并行和数据并行。

## 任务并行性

任务并行是指多个功能可以同时独立执行。一个例子是准备一个聚会。一个人可能得到气球，另一个人可能得到冰淇淋，第三个人可能得到蛋糕。虽然没有这三样东西聚会就不完整，但三个不同的人可以独立于其他人做他们需要做的事情，只要他们在聚会前完成并在正确的地方见面。

如果我们将此映射到计算机程序，每个人都类似于一些计算硬件(CPU/GPU/FPGA)，选择各种项目就是要运行的任务。

## 数据并行性

数据并行是指相同的功能可以在几个相同的数据上独立执行。想象一下，上面例子中的那个人去买冰淇淋，但是另外四个人也想买冰淇淋。潜在地，所有五个人可以同时从同一个商店的冰箱里得到冰淇淋。

在这个例子中，我们再次把人比作计算机硬件，每个人的任务都是买冰淇淋。冰淇淋是我们对数据的(美味)类比。

这些例子很简单，但希望有指导意义。并行还有更微妙的类型，尤其是在计算机体系结构领域，但是这些简单的类比可以帮助您理解在进行并行编程时如何考虑并行性。

# 可用资源、可用并行度和争用

关于并行性，需要考虑的一些重要因素是可用资源和给定解决方案空间中的可用并行性。

*   我们可能会受到完成一项任务的资源数量的限制——如果我只有三个人，我一次只能做三项任务。
*   我们可能会受到可用任务的限制——如果我的聚会只需要气球和蛋糕，第三个人执行任务对我没有帮助。
*   我们可能会受到可用数据的限制——如果我有五个人想买冰淇淋，但商店冰箱里只剩下三个容器的冰淇淋，那么两个人将无事可做。

另一个需要考虑的问题是争用。资源通常是有限的，我们试图并行化一个问题时经常会遇到资源访问的问题。

让我们想象一下，我们去商店买冰淇淋，冰柜里有 100 个容器的冰淇淋，但是一次只能有三个人站在冰柜前面。这意味着，即使有 100 个人在那里买冰淇淋，由于进入冰柜的机会有限，买冰淇淋的人数最多也只有三人。

这个类比适用于各种计算机硬件。举个例子:把冰箱的存取想象成一条内存总线。如果它不够宽，不能让所有的数据以足够快的速度流向加速器，那么您的加速器就会停滞不前，等待对数据的访问来完成它需要做的事情。

# 简单的代码示例

为了使这一点更加具体，让我们展示上面的数据并行购物问题的一些代码。我们首先创建一个购物者类。这个类的工作是执行购物的工作。在这种情况下，我使用一个简单的矩阵乘法来计算购物成本。

## 与 [OpenMP](https://www.openmp.org/) 并行

现在，我已经定义了购物行为，让我们看看如何使用 OpenMP 使这段代码并行运行，OpenMP 是一种可移植的解决方案，允许程序员在他们的程序中添加并行性。Pragma 指令被添加到现有代码中，以告诉编译器如何并行运行部分代码。

这个简单的代码由四个简单的部分组成:

1.  第 18 行:创建`65536`购物者
2.  第 21 行:调用`Shop`函数
3.  第 10-12 行:请每位顾客去购物
4.  第 9 行:告诉编译器何时使用 OpenMP 并行运行循环迭代的 pragma

其余的只是计时器，所以我们可以看到并行运行代码的好处。

OpenMP 代码的好处在于，你可以告诉编译器使用/不使用 OpenMP，这样就会产生一个串行程序。

在本文中，我使用的是一台 HP Envy*笔记本电脑，它配备了英特尔酷睿 i7–12700h 处理器、32GB 内存和集成的英特尔 Iris Xe GPU。该系统运行的是 6.0 内核 Ubuntu* 22.04 和英特尔 oneAPI DPC++/C++编译器。

首先，我使用以下命令启用了英特尔编译器环境:

```
> /opt/intel/oneapi/setvars.sh
```

以下命令编译代码:

```
> icx -lstdc++ grocery-omp.cpp -o serial-test
> icx -lstdc++ -fiopenmp grocery-omp.cpp -o omp-test
```

最后一个命令包括`-fiopenmp`标志，告诉编译器使用 OpenMP 启用并行性。运行可执行文件时，我在系统上得到以下输出:

```
> ./serial-test
Elapsed time in milliseconds: 27361 ms
> ./omp-test
Elapsed time in milliseconds: 4002 ms
```

OpenMP 运行利用我的几个 CPU 内核同时完成工作，这导致 OpenMP 运行速度提高了 6 到 7 倍。

## oneAPI/SYCL 的并行性

OpenMP 是一种通过直接方法实现并行的绝佳方式。带有 SYCL 的 C++是 [oneAPI](https://www.oneapi.io/) 规范的一部分，它允许我们使用显式方法来表达并行性。

在这个例子中，SYCL 代码大于前面的代码库。然而，我们有相同的核心代码:

1.  第 31 行:创建`65536`购物者
2.  第 46 行:调用`Shop`函数
3.  第 25 行:让每个购物者去购物

与我们使用指令的 OpenMP 示例不同，SYCL 允许用户通过代码和 C++结构显式定义他们程序的并行行为。这提供了 OpenMP 所不具备的运行时灵活性。

根据您的用例，一种范式可能比另一种更有意义。在这种情况下，我们有办法使用英特尔的 oneAPI SYCL 运行时并使用`SYCL_DEVICE_FILTER`环境变量来获取单个二进制文件并以多种方式运行它:

```
> SYCL_DEVICE_FILTER=host:host:0 ./sycl-test
Running on device: SYCL host device
Elapsed time in milliseconds: 27201 ms
> SYCL_DEVICE_FILTER=opencl:cpu:1 ./sycl-test
Running on device: 12th Gen Intel(R) Core(TM) i7-12700H
Elapsed time in milliseconds: 4197 ms
> SYCL_DEVICE_FILTER=opencl:gpu:3 ./sycl-test
Running on device: Intel(R) Graphics [0x46a6]
Elapsed time in milliseconds: 3988 ms
```

`SYCL_DEVICE_FILTER`的值来自运行 sycl-ls 命令，该命令是[英特尔 oneAPI 基础工具包](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit.html)的一部分。

oneAPI C++ with SYCL 实现的酷之处在于，二进制代码可以将工作定向到多个设备。

# 结论

理解并行性和编写并行代码是一个复杂的问题。这篇初级读物描述了一个简单的例子，说明如何使用 OpenMP 和 C++以及 SYCL 来实现并行性。在创建并行程序时，还有许多其他考虑因素，例如多个计算资源如何共享内存和变量，以及如何在多个资源之间平衡工作。

我将在以后的文章中解决这些问题。但是现在，我鼓励您尝试抓住上面的简单示例，看看它在您的系统上是如何工作的。

感谢阅读！

```
**Want to Connect?**If you want to see what random tech news I’m reading, you can [follow me](https://twitter.com/tonymongkolsmai) on Twitter.
```

*英特尔、英特尔标志和其他英特尔标志是英特尔公司或其子公司的商标。*