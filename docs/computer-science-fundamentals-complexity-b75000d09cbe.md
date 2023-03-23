# 计算机科学基础:复杂性

> 原文：<https://betterprogramming.pub/computer-science-fundamentals-complexity-b75000d09cbe>

## 时间与空间的复杂性等等

![](img/183ca1809f6de88c36d5b4c4041e8fa9.png)

克里斯蒂安·兰伯特在 [Unsplash](https://unsplash.com/s/photos/fibonacci?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

几乎每个写代码的人都必须以这样或那样的方式考虑性能。但是，算法的可执行性意味着什么呢？在这篇文章中，我想向你介绍一些计算机科学家通常学习的关于计算复杂性的基本原理，以便对他们的程序和系统进行推理。从这些方面考虑性能可能有助于您编写更好的代码，更一般地说，有助于您更好地解决问题。

小免责声明:我绝不是这方面的专家。我在这里的目标只是写一个简短的介绍。如果你发现错误或不清楚的地方，我将非常感谢你说出来！

![](img/ab5fb4d91dc5fbedae7b77284b34938c.png)

时间与空间。

# 时间与空间

计算机科学家通常谈论时间复杂性和空间复杂性。前者指的是算法运行需要多长时间(或者更准确地说，算法需要多少步骤来解决一个问题，因为这些步骤的实时持续时间取决于所使用的计算机硬件)，而后者描述的是在这个过程中占用了多少内存。

为了谈论这些，计算机科学使用了描述符，如 [Big-O 符号](https://en.wikipedia.org/wiki/Big_O_notation)，通俗地说，指的是最糟糕的情况。例如，要检查一个未排序的数组是否包含一个特定的元素，最坏的情况是该元素位于最后检查的位置，因此这个检查算法的时间复杂度为 *O(n)* 。还有其他符号(Little-O，Big-Omega，Small-Omega)描述稍微不同的情况，例如一般情况，但 Big-O(通常缩写为 O)是迄今为止最广泛使用的一种。

说明这一点的一个很好的问题是[斐波那契](https://en.wikipedia.org/wiki/Fibonacci_number)序列。如果你不熟悉，斐波纳契数列中的一个数字是这样计算的:

*   第一个数字是 0。
*   第二个数字是 1。
*   后面的每一个数字都是前两个数字的和。例如，第三个数是第一个加第二个(0+1=1)，第四个数是第二个加第三个(1+1=2)，依此类推。按照这种模式，我们得到一个看起来像这样的序列:0，1，1，2，3，5，8，13，21，等等。

这很容易用一点递归来编写代码，这非常类似于问题的陈述方式或人类对问题的思考方式。对于任何示例，我都将使用类似 Java 的伪代码，但是您可以随意用您选择的任何语言来尝试。

```
Fib(int i) {
  if (i < 2) return i;
  return Fib(i-1) + Fib(i-2);
}
```

观察随着输入数量的增加，需要完成的指令数量会发生什么变化:

*   如果`i`是 0 或 1，我们只需一步就能返回。这是基本情况(对于递归函数非常重要)。
*   如果`i`为 2，第一个条件检查失败，下一行进行两次`Fib`调用，每次都要走一步返回。总共有四步。
*   如果`i`为 3，则进行两次呼叫(`Fib(1)`和`Fib(2)`)。后者导致了两个调用(`Fib(0)`和`Fib(1)`)。有了条件检查，就有九个步骤。请注意，对`Fib(1)`的调用是重复的。我们稍后将使用它来优化这个算法。

这种幼稚的斐波那契实现具有*【o(2^n】*的时间复杂度。也就是说，需要`2`的`i`次方个指令才能完成，因为对于每个数字≤输入(除了基本情况)，都要进行两次`Fib`调用。要计算第十个数，需要 100 条指令。虽然计算机越来越快，但这并不是一个非常好的运行时。根据您使用的语言，无论计算机的硬件如何，运行时间都会从几秒钟到几分钟到几小时甚至更长。就空间复杂度而言，这也是 *O(n )* 由于每个对`Fib`的递归调用都分配了一个额外的调用栈。总的来说，我敢打赌，随着内存变得越来越便宜，时间复杂度通常比空间复杂度优先。除非您在特定的限制或约束下编码(比如在嵌入式或物联网设备上)，否则速度通常是限制因素。

一个算法的精确复杂度可能是类似于 *O(3n)* 的东西，但是像这个例子中的 3 这样的常数通常被排除在外，因为所有这些的重点更多的是对一个算法的复杂度进行分类而不是定义它的精确运行时间。另一件要注意的事情是，恒定的时间或空间复杂度(即复杂度不随输入大小而变化)被表示为 *O(1)* 。这方面的一个例子是数组查找。不管数组有多少个元素，访问它的第一个、第二个或第 100 个元素只需要一个(高级)步骤。

![](img/57e4776025b08256ab83710aafbaf082.png)

你好，我是尼克🍌打开[防溅](https://unsplash.com/s/photos/sonic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)。

# 得快点

有多种方法可以解决上述功能的性能问题。一种以额外内存为代价降低时间复杂度的流行方法是一种叫做[记忆化](https://en.wikipedia.org/wiki/Memoization)的技术。这通过存储(即记忆)先前计算的数字来节省时间，就像人类写下他们计算的数字一样。在我们的 Fibonacci 函数的情况下，我们可以在地图中跟踪`Fib`结果，并且只计算一个新的结果，如果它不在地图中的话。

```
Map memory = new HashMap<int, int>();
memory[0] = 0;
memory[1] = 1;FibMem(int i) {
  int result = memory[i];
  if (result == null) {
    result = FibMem(i-1) + FibMem(i-2);
  }
  return result;
}
```

用非常少量的附加代码，我们实现了 *O(n)* 的时间复杂度。计算第 N 个斐波那契数现在需要 *N* 步，而不是 *N* 步。代价是我们现在使用了更多的内存。

另一个隐含的权衡是，这个函数可能比上一个函数更复杂。过早的优化是万恶之源——俗话说得好。在分析计算复杂性时，我们永远不应该忘记人的因素。性能和可读性之间的权衡并不容易，但这超出了本文的范围。

我们可以更进一步，将空间复杂度降低到 O(n) 。为此，我们不使用递归，而是使用一个简单的 for 循环和一些局部变量。因为我们只更新局部变量，不进行递归调用，所以内存消耗保持不变。

```
FibOptimized(int i) {
  if (i < 2) return index; int a = 0;
  int b = 1;
  for (int j = 0; j < i; j++) {
    int temp = b;
    b = a;
    a = a + temp;
  }
  return a;
}
```

现在你有了:一个时间复杂度为 O(n) 和空间复杂度为 O(1)*的 Fibonacci 实现。我还想再一次指出，在我看来，这比简单的斐波那契实现可读性差得多。有一些实现可以进一步降低时间复杂度，是的，这些实现确实变得更加复杂。*

作为练习，我将留给你以下问题:给定一个整数数组`[a, b, c, …]`，写一个时间复杂度为 *O(n)* 的函数返回一个数组`[b*c*…, a*c*…, a*b*…]`，使得输出数组的每个元素都是输入数组的所有元素的乘积，除了具有当前索引的元素。例如，输入`[1, 2, 3, 4]`将产生输出`[24, 12, 8, 6]`(即`[2*3*4, 1*3*4, 1*2*4, 1*2*3]`)。一个 *O(n )* 解决方案相当简单，但是一个 *O(n)* 解决方案也可以实现。

![](img/0870c5486b96385dcb37a5cd2d78dec6.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照。

# 超越大 O

计算复杂性是一个广阔的领域，要学的东西比这里强调的众所周知的冰山一角要多得多。你可能想查的东西有[动态规划](https://en.wikipedia.org/wiki/Dynamic_programming)、[旅行推销员](https://en.wikipedia.org/wiki/Travelling_salesman_problem)问题、[分而治之](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm)方法、[贪婪算法](https://en.wikipedia.org/wiki/Greedy_algorithm)设计和其他[启发式](https://en.wikipedia.org/wiki/Heuristic_(computer_science))以及 [P 对 NP](https://en.wikipedia.org/wiki/P_versus_NP_problem) 问题。其中一些可能在本质上比其他的更学术，你可能永远不会被要求为你工作中的代码写一个正式的证明，但是所有这些对于算法优化和计算机科学整体来说都是非常重要的。