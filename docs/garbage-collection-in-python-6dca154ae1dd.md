# Python 中的垃圾收集

> 原文：<https://betterprogramming.pub/garbage-collection-in-python-6dca154ae1dd>

## 回收内存的过程

![](img/84b214ab87cbe328e5a57a2c4862271f.png)

由[卡特琳娜·林皮索妮](https://twitter.com/ninalimpi)创作的艺术品

如果你已经编程一段时间了，你可能听说过*垃圾收集。让我们更深入地了解它是什么以及它是如何工作的。*

# **什么和为什么**

在现实世界中，我们会清理掉一些东西，例如旧纸币、不再需要的盒子，然后扔进垃圾箱/回收桶。空间有限，我们想腾出空间来存放其他我们想存放的必需品。

类似地，在计算机中，空间——又名*内存—* 是一种重要而有限的资源。因此，垃圾收集器收集不再需要的数据对象并丢弃它们。

根据语言的不同，垃圾收集可以是自动的，也可以是手动的。在大多数高级语言中，比如 Python 和 Java，它是自动化的。因此，这些语言被称为*垃圾收集语言。*其他语言如 C 不支持自动垃圾回收，由程序员负责内存管理。

现在，让我们看看垃圾收集是如何工作的。

# **如何**

有一些技术，但是大多数垃圾收集语言，包括 Python，都使用*引用计数。*在引用计数中，我们跟踪对一个对象的引用次数，当计数为`0`时丢弃一个对象。

对象的引用计数随着指向它的别名数量的变化而变化。当它被赋予一个新名称或被放入一个容器(如列表或字典)中时，计数会增加。当使用`del`命令删除它、它的引用超出范围或者它被重新分配时，计数会减少。例如:

```
sample = 100 # Creates object <100>. Ref count = 1 sample_copy = sample # Ref count = 2\. sample_list = [sample] # Ref count = 3.del sample # Ref count = 2\. Note that this doesn't affect sample_copy and sample_list as they directly point to <100>. sample_copy = 10 # Ref count = 1 as alias was reassigned.sample_list.clear() # Ref count = 0 as list is cleared and doesn't store the alias pointing to <100>. 
```

当引用计数下降到`0`时，引用计数可以立即回收对象。然而，这是有代价的:我们需要为每个对象存储一个额外的整数值来指示它的引用计数(空间和时间的权衡)。

然而，引用计数的一个问题是*引用周期的概念。*如果两个对象 A 和 B 相互引用，它们实际上是在一个气泡中，引用计数总是大于或等于`1`。这在列表、类和函数中很常见。例如，当对象引用自身时:

```
x = []
x.append(x)
```

或者当对象循环引用彼此时:

```
a.attribute_1 = b
b.attribute_2 = a
```

垃圾收集器定期寻找引用循环并删除它们。由于就资源而言这是一个昂贵的过程，所以这是定期进行的并且是预定的。Python 垃圾收集器接口提供了探索垃圾收集执行时间表和阈值的方法。

 [## 垃圾收集器接口- Python 3.8.3rc1 文档

### 这个模块为可选的垃圾收集器提供了一个接口。它提供了禁用收集器的能力…

docs.python.org](https://docs.python.org/3.7/library/gc.html) 

# 结论

希望这篇文章对你有帮助。

参考资料和进一步阅读:

[](https://www.tutorialspoint.com/How-does-garbage-collection-work-in-Python) [## Python 中的垃圾收集是如何工作的？

### Python 会自动删除不需要的对象(内置类型或类实例)来释放内存空间。流程…

www.tutorialspoint.com](https://www.tutorialspoint.com/How-does-garbage-collection-work-in-Python) [](https://stackoverflow.com/questions/9910774/what-is-a-reference-cycle-in-python) [## python 中的引用循环是什么？

### 感谢贡献一个堆栈溢出的答案！请务必回答问题。提供详细信息并分享…

stackoverflow.com](https://stackoverflow.com/questions/9910774/what-is-a-reference-cycle-in-python)