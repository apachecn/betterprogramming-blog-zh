# 4 个鲜为人知的 Swift 功能

> 原文：<https://betterprogramming.pub/4-lesser-known-swift-features-ddfbc9268aa9>

## 你知道如何解决字典冲突吗？

![](img/e71025f134071d23cbc83eb921743c2f.png)

照片由[迈克尔·泽兹奇](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

许多开发人员使用标准技术，并且经常不知道友好的语言和库下隐藏的许多突出的特性。这些特性对一些读者来说可能已经很熟悉了，但是对我来说这是一个小发现。

# 尾部递归优化

许多开发人员知道递归可能是一个不完美的工具，因为函数调用堆栈可能会不受控制地溢出，并由于分段错误而崩溃。一种类型的递归是尾递归，即函数在函数本身的末尾调用自己。

```
**func** tailRecursion(n: Int) {
  **guard** n != 0 **else** { **return** }
  print(n)
  tailRecursion(n: n-1)
}
```

与通常的递归相比:

```
**func** usualRecursion(n: Int) {
  **if** n > 0 {
    usualRecursion(n: n-1)
  }
  print(n)
}
```

Swift 使用尾部递归优化，不在内存中的调用栈中添加方法调用，而是跳转到函数的开头。它消耗的堆栈内存要少得多。

在前面的例子中，`usualRecursion(n: 300000)`因“分段故障”而崩溃，而`tailRecursion(n: 1000000)`正常运行。

实验是在我的电脑上进行的；这些参数的结果在其他计算机上可能会有所不同。
你可以在生成的汇编代码中看到优化。

`xcrun swiftc -O -S File.swift > main.asm`

即使你不懂汇编语言，你也可以在 main.asm 文件中看到，其中一个' jamp '命令(以' j '开头的函数)是为尾部递归而执行的，而标准的 callq 函数 call 命令调用通常的递归。

基于这种优化，我们可以得出结论，如果有可能在函数本身的末尾递归，那么最好就这么做。

# 存储负数

在许多编程语言中，有符号的数字存储为一组位，其中第一位是数字的符号(0 是正数，1 是负数)。

剩余的位代表该值。例如，在存储 1 个字节的类型中，00000001 是数字 1，10000001 是数字-1。

但在 Swift 中，存储系统针对快速操作进行了优化，数字以一种称为二进制补码的方法存储。要在这个系统中描述一个负数，你需要把这个数写成一个比特表示，然后把比特反转，加 1。例如，如果这个数字是-15:

*   写出十进制 15: 0001111 的位数
*   反转位数:1110000
*   添加号码 1: 1110001
*   如前所述，在演示开始时，您需要添加第 1 位

最终结果将是 11110001。

如果我们想将 11110001 的位表示转换成十进制格式，那么我们需要

*   反转位:0001110(我们应该省略第一位，因为它代表数字的符号)
*   添加 1: 0001111
*   转换为十进制格式:15
*   并加一个标志:-15

这种数字表示允许在低级别更快地执行算术运算。

```
// The way to get inner representation of a negative number:
String(UInt8(bitPattern: Int8(-15)), radix: 2)
```

# 一个功能可以以不同的速度工作

Swift 提供各种收集协议，这些协议通常共享相同的功能。乍一看，它们的工作方式是一样的，但实际上，这些功能的操作会因协议的不同而有很大差异。

例如，Sequence 协议上的后缀函数以 O(n)复杂度工作，而 RandomAccessCollection 协议上的相同函数(继承自 Sequence；符合该协议的结构的一个例子是规则数组)已经以复杂度 O(1)工作。在大量的数据中，这种差异会很明显。因此，请注意您想要使用的协议。

```
// define an array
**let** array = 0...100_000print("start array suffix \(Date())")
// suffix(5) works O(1)
array.suffix(5)
print("end array suffix \(Date())")// define a sequence
**let** seq = sequence(first: 0, next: { $0 < 100_000 ? $0 + 1 : **nil**})print("start sequence suffix \(Date())")
// suffix(5) works O(n), iterates every element of the sequence
seq.suffix(5)
print("end sequence suffix \(Date())")
```

# 字典中的冲突解决

我们知道，在字典中存储元素可能会发生冲突。不同元素的哈希值可以匹配，这是特别解决的典型情况(为此，key 元素必须实现`==`操作)。

冲突存储的一个典型的理论实现经常被听到，例如，在对候选人的面试中，当存储项目在一个链表中排列时，也称为分离链接。

当你通过键搜索一个元素，但是字典已经有了其他元素通过 hash 的时候，我们会有下面的情况。一个典型的算法遍历链表并测试元素的相等性，直到它遇到一个等价的元素或检查所有具有相同散列的项目。这种方法很好，解释起来也相对简单，但是 Swift 在现实中有不同的实现。

简单地说，所有记录都存储在一个数组中，通过一个键散列来访问。如果发生冲突，您应该将一个新元素写入到同一个数组中，但是要与发生冲突的项保持一定的距离。如果这个小区也忙，则检查相同距离内的下一个小区，依此类推。这些检查可以从数组的末尾绕到它的开头(一个逻辑环)。冲突解决方法的名称是采用线性探测的开放式寻址。在这个解决方案中，所有项目都存储在一个共享空间中，不需要额外的内存来保持 LinkedList 运行。

你可以在这里看到实现。

# Swift 是开源的

您可能不知道，Swift 是一个开源项目，您也可以参与其中。您可以参与标准库的实施，并提供您的想法，这些想法可能会在未来新版本的 Swift 中出现。在链接[https://github.com/apple/swift/tree/main/stdlib/public/core](https://github.com/apple/swift/tree/main/stdlib/public/core)中你可以很容易地找到 Swift 的常用类型的实现

此外，您可以与社区一起开发几个库，其中包括:swift-markdown、swift-algorithms、swift-numerics、swift-collections、swift-atomics。