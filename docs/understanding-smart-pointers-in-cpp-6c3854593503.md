# 现代 C++:智能指针的近距离观察

> 原文：<https://betterprogramming.pub/understanding-smart-pointers-in-cpp-6c3854593503>

## 有效的 C++

## “理解智能指针”的续篇

![](img/4c306fb441faee36552ca7c9958e9641.png)

克里斯·里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 目录

```
· [Recap](#93ec)
  ∘ [C++ Smart Pointers](#6030)
· [Toy Class](#cb53)
· [In a nutshell](#dd0f)
  ∘ [Why unique pointers](#f45d)
  ∘ [Why did auto_ptr not work?](#8ed3)
  ∘ [Issues](#871d)
  ∘ [Move Semantics](#e14b)
  ∘ [What is std::unique_ptr ?](#4370)
  ∘ [Unique Ownership of unique pointer](#2aa4)
  ∘ [Creating an empty unique_ptr object](#9bdd)
  ∘ [Check if a unique_ptr<> is empty](#cdc6)
  ∘ [Creating a unique_ptr object with a raw pointer](#6c16)
  ∘ [Resetting a unique_ptr](#0c7f)
  ∘ [Transferring the ownership of the unique_ptr object](#90a4)
  ∘ [Releasing the associated raw pointer](#e5a2)
· [Source Code](#c7bd)
· [Source Code II](#3770)
· [References](#bb0f)
```

# 概述

RAII: *资源获取是初始化*，这是一种使用包装类来处理资源管理的 C++编程哲学——这一概念体现在*智能指针*中。我们将从停止的地方继续探讨这个问题(参见第一部分)。通常，要取得最有效的进步，需要付出后退几步的代价来换取以后前进的许多步。因此，我们接下来的总结不仅总结了之前学到的经验，还通过扩展我们对第一版智能指针的理解提供了更多的见解(例如，`auto_ptr`及其缺点，后来通过引入`unique_ptr`得到了改进)。让我们开始吧！

[](/smart-pointers-in-cpp-708486276526) [## 理解 C++中的智能指针

### 更聪明地工作，而不是更努力

better 编程. pub](/smart-pointers-in-cpp-708486276526) [](/understanding-smart-pointer-iii-909512a5eb05) [## 通过直观的视觉效果解释 C++智能指针

### 我的 C++指针系列的第 3 部分

better 编程. pub](/understanding-smart-pointer-iii-909512a5eb05) 

## C++智能指针

智能指针是一个对象，它存储一个指向堆分配对象的指针。

*   智能指针的外观和行为类似于普通的 C++指针
*   通过超载`*`、`->`、`[]`等。
*   这些可以帮助你管理记忆。
*   智能指针会在适当的时候删除所指向的对象，包括调用对象的析构函数。
*   这取决于你使用哪种智能指针
*   通过正确使用智能指针，您不再需要记住何时通过`new`分配`delete`资源！

如前一篇文章所述，作为现代 C++标准库的一部分，提供了*智能指针*:`unique_ptr`、`shared_ptr`和`weak_ptr`。

[](/smart-pointers-in-cpp-708486276526) [## 理解 C++中的智能指针

### 更聪明地工作，而不是更努力

better 编程. pub](/smart-pointers-in-cpp-708486276526) 

接下来我们将仔细观察一个`unique_ptr`:对现在已经废弃的`auto_ptr`的改进。参见[理解 C++中的智能指针](/smart-pointers-in-cpp-708486276526)了解每一个的介绍；继续前进，看看`unique_ptr`到底是如何提高`auto_ptr`的。

# 玩具类

这是整个教程中使用的玩具类。[源代码](#c7bd)提供了全程演示的代码。

# 简单地

使用对象管理资源:

*   防止资源泄漏，使用 RAII 在其构造函数中获取资源，并在其析构函数中释放它们
*   复制资源是编码人员必须考虑的挑战之一

仔细考虑资源管理类中的复制行为

1.  禁止复制
2.  引用对基础资源进行计数
3.  复制基础资源
4.  转移基础资源的所有权

*   复制一个 *RAII* 对象需要复制它所管理的资源，所以资源的复制行为决定了 *RAII* 对象的复制行为。
*   常见的 *RAII* 类复制行为不允许复制执行引用计数，但其他行为是可能的。

## 为什么是唯一指针

程序通常是各种分支和函数调用的构造，这些分支和函数调用被推到*堆栈内存*中。因此，一个程序可能有许多潜在的流动甚至退出方式。因此，每次通过`new`实例化一个对象时，很容易忘记调用`delete`。与 JAVA 中使用的非确定性垃圾收集器不同，C++通过*智能指针*提供了一个更优雅、更可预测的解决方案。C++98 引入了`auto_ptr`，为 c++ 11【2】的`unique_ptr`铺平了道路。好处如下:

*   `unique_ptr`当它超出范围时将删除它的指针
*   因此，unique_ptr 也有助于异常安全
*   `unique_ptr`已禁用其复制构造函数和赋值运算符

不能复制一个'`unique_ptr'`来保持唯一性或*所有权*。

## 为什么`auto_ptr`不起作用？

在 C++98 中引入，在 C++17 中删除:第一次尝试智能指针，将语义作为赋值和复制构造函数。

## 问题

1.通过值传递函数:通过转移所有权，`auto_ptr`假设新的指针代替了被分配者，而另一个被设置为 null。因此，函数调用范围内的指针在从它被转移到的函数返回时是`null`。

2.`std::auto_ptr`总是使用非数组删除来删除其内容。

3.与标准库中的许多其他类不兼容，包括大多数容器和算法。

## 移动语义

在 C++11 中，正式定义了“移动”的概念，并在语言中加入了“移动语义”来区分复制和正确移动。

因此，在 C++11 中，`std::auto_ptr`已经被一堆其他类型的“移动感知”*智能指针* : `std::unique_ptr`、`std::weak_ptr`和`std::shared_ptr`所取代。我们还将探索两个最流行的:`unique_ptr`(直接替代`auto_ptr`)和`shared_ptr`。`unique_ptr`已在本博客中涵盖，而`share_ptr`将是下一个！

## 什么是 std::unique_ptr？

unique_ptr 是 c++11 提供的防止内存泄漏的智能指针实现之一。unique_ptr 对象包装一个原始指针，并负责它的生存期。当这个对象被析构时，在它的析构函数中，它删除相关的原始指针。

`unique_ptr`的`->`和`*`操作符重载，作为普通指针操作。

参见[源代码二](#3770)。

`unique_ptr<MyClass>`对象`myPtr`接受一个原始指针作为参数。当函数退出时，这个对象将超出作用域，调用它的析构函数。在析构函数`unique_ptr`对象中，`myPtr`删除相关的原始指针。

无论函数是否正常退出(即由于某种异常)，都会调用`myPtr`的析构函数。因此，原始指针将总是被删除，并防止内存泄漏。

## 唯一指针的唯一所有权

一个`unique_ptr`总是相关原始指针的唯一拥有者。我们不能复制 unique_ptr 对象；它只是可移动的。

因为每个`unique_ptr`都是原始指针的唯一所有者；它直接删除其析构函数中的相关指针。不需要引用计数；所以，很轻。

## 创建空的 unique_ptr 对象

让我们创建一个空的 unique_ptr <int>对象，即</int>

```
// Empty unique_ptr objectstd::unique_ptr<int> ptr1;
```

`ptr1`没有关联的原始指针。因此它是空的。

## 检查`unique_ptr<>`是否为空

有两种方法可以检查一个`unique_ptr<>`对象是空的还是有一个原始指针与之相关联。

**方法一:**

```
// Check if unique pointer object is emptyif(!ptr1) std::cout<<”ptr1 is empty”<<std::endl;
```

**方法二:**

```
// Check if unique pointer object is emptyif(ptr1 == nullptr) std::cout<<”ptr1 is empty”<<std::endl;
```

## 使用原始指针创建 unique_ptr 对象

要创建一个非空的`unique_ptr<>`,在创建对象时，在它的构造函数中传递原始指针，即，

我们不能通过赋值来创建一个`unique_ptr<>`对象。否则，会导致编译错误。

```
// std::unique_ptr<MyClass> myPtr2 = new MyClass(); // Compile Error
```

## 重置唯一指针

在`unique_ptr<>`上调用`reset()`函数将重置它，即删除相关的原始指针并使`unique_ptr<>`对象为空

`unique_ptr`不可复制。

由于`unique_ptr<>`不可复制，只能移动。因此，我们不能通过复制构造函数或赋值操作符来创建 unique_ptr 对象的副本。

复制构造函数和赋值运算符都在`unique_ptr<>`类中被删除。

## 转移 unique_ptr 对象的所有权

我们不能复制一个`unique_ptr`对象，但是我们可以移动它们。这意味着一个`unique_ptr`可以将相关原始指针的所有权转移给另一个`unique_ptr`。让我们通过一个例子来理解，

创建一个`unique_ptr`对象(参见上面代码示例中的第 1–2 行):

> myPtr2 不为空。

现在将关联指针`MyClass`的所有权转移到一个新的`unique_ptr`对象，即

`std::move()`将`myPtr2`转换为右值参考。从而调用`unique_ptr`的 move 构造函数，相关的原始指针可以传递给`myPtr4`。

`myPtr2`在其原始指针的所有权转移到`myPtr4`后将为空。

## 释放相关的原始指针

在`unique_ptr`对象上调用`release()`将从对象中释放相关原始指针的所有权，并返回原始指针。

# 源代码

> ptr1 为空
> ptr1 为空
> my class::Constructor
> myPtr 不为空
> 23
> 重置 myPtr
> MyClass::析构函数
> myPtr 为空
> MyClass::构造函数
> myPtr3 不为空
> myPtr2 不为空
> myPtr4 不为空
> 55
> MyClass::析构函数
> MyClass::构造函数
> myPtr5 不为空

# 源代码二

# 参考

[1] S .迈耶斯。"有效的 C++: 55 种改进程序和设计的具体方法."*艾迪森-卫斯理专业(*第三版)，2005 年。

```
**Want to Connect?**Follow Dr. Robinson on [LinkedIn](https://www.linkedin.com/company/superannotate/), [Twitter](https://twitter.com/jrobvision), [Facebook](https://www.facebook.com/superannotate), and [Instagram](https://www.instagram.com/doctor__jjj/).Visit my homepage for papers, blogs, email signups, and more!
```

[](https://www.jrobs-vision.com/) [## 人工智能工程师|约瑟夫·p·罗宾逊

### 问候！我是 Vicarious Surgical 的人工智能工程师，致力于推进下一代外科机器人技术。我的…

www.jrobs-vision.com](https://www.jrobs-vision.com/)