# 什么是动态编程，为什么它如此重要？

> 原文：<https://betterprogramming.pub/dynamic-programming-series-1-what-is-it-and-why-is-it-so-important-64675b9f6049>

## 让我们深入研究动态编程

![](img/98472b61b077fe81b37d958b45294e63.png)

克里斯里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

您可能在某个地方听说过动态编程这个术语，并想知道它是什么意思。在动态编程系列的第一篇文章中，我将试图解释它到底是什么以及为什么它如此重要。我肯定需要更多的练习，这就是为什么我开始这个系列，以便我们可以一起学习和掌握它！

# 那么什么是动态编程呢？

简单地说，动态编程是一种将问题分解成子问题，解决这些子问题*一次*，并存储其解决方案的技术。你可能会问，“为什么我们需要存储这些解决方案？”嗯，在计算机科学中，我们把速度快、占用内存少的东西定义为高效。通过存储这些解决方案，如果同样的问题再次出现，我们能够简单地查找它们。这节省了大量的计算时间，因为不需要重新计算解。

但是等等！效率由时间和空间复杂性组成，所以如果我们减少解决问题的时间只是为了增加使用的空间，这又有什么关系呢？这就是为什么理解这一点很重要，在动态编程中，我们最终希望实现的是一个显著更快的计算时间，代价是使用空间的适度增加。

# 方法

基本上有两种方法可以存储问题的解决方案:

1.  记忆化(自上而下的方法)
2.  制表(自下而上的方法)

在自顶向下的方法中，一般的代码结构如下所示:

```
def top-down(n):
if memo[n] is not initialized:
    # the epsilon below represents some computation that you do which is stored in memo
    # usually the computation will involve some recursive call that approaches the base case
    memo[n] = ... 
return memo[n]
```

因为这种方法涉及到通过追溯到基础案例来解决子问题，所以它被称为自顶向下的方法。当我们想要找到第 n 个问题的解决方案时，我们搜索第 *n-1 个*、 *n-2 个*问题的答案，以此类推，直到基础案例。

在自下而上的方法中，情况正好相反。我们首先计算所有可能的基本情况的解决方案，然后从那里逐步得出我们想要的解决方案。制表方法的一般代码结构如下所示:

```
def bottom-up(n):
    for i = 1 to i = n:
        table[i] = ... # computing and storing the solution
    return table[n] # simply looking up the solution and return it
```

现在您已经知道了两种 DP 方法，您应该使用哪种方法呢？就像几乎任何事情一样，这要视情况而定。通常，如果不是所有子问题的解决方案都需要计算，您会希望使用自顶向下的方法。这将节省使用自底向上方法时所需的不必要的计算时间。相反，如果无论如何都需要计算所有子问题的解决方案，您应该使用自底向上的方法，因为它可以防止递归调用开销，而递归调用开销几乎总是会使自底向上的方法更快。

至此，我相信您已经对动态编程有了足够的了解，可以在本系列的下一章解决我们的第一个问题。感谢您的阅读，在此之前！