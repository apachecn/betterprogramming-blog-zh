# 使用 PHP 生成器的快速性能优化指南

> 原文：<https://betterprogramming.pub/a-quick-performance-optimization-example-using-php-generators-9e71aac810e0>

## 使用生成器减少执行时间和内存使用

![](img/3d96ece3e31258bb91abcfd279205626.png)

来自 PHP 官方网站:

> 生成器提供了一种简单的方法来实现简单的[迭代器](https://www.php.net/manual/en/language.oop5.iterations.php)，而没有实现实现[迭代器](https://www.php.net/manual/en/class.iterator.php)接口的类的开销或复杂性。
> 
> 生成器允许您编写使用[*foreach*](https://www.php.net/manual/en/control-structures.foreach.php)*来迭代一组数据的代码，而无需在内存中构建数组，这可能会导致您超出内存限制，或者需要大量的处理时间来生成。相反，您可以编写一个生成器函数，它与普通的[函数](https://www.php.net/manual/en/functions.user-defined.php)相同，除了不是[返回](https://www.php.net/manual/en/functions.returning-values.php)一次，而是生成器可以根据需要[产生](https://www.php.net/manual/en/language.generators.syntax.php#control-structures.yield)多次，以便提供要迭代的值。*

# *使用生成器减少内存使用*

*让我们举下面的例子:*

*这个简单的脚本使用一个`foreach` 循环来计算从 0 到`THRESHOLD`的所有数字的总和。此外，我还包含了一个内存使用打印函数，在遍历执行期间和结束时，在循环结束后调用一次。*

*对于第一个版本，这些是我在尝试不同的`THRESHOLD`时得到的值:*

*当生成整数数组时，它被临时存储，直到`foreach`循环执行完成。这解释了为什么当我们增加`THRESHOLD`值(我们增加数组大小)时，内存使用在数组遍历期间会增加。然而，这似乎对脚本结尾的内存使用没有影响。*

*让我们修改一下 *myIntegers* 函数，以便利用 PHP 生成器:*

*通过利用 PHP 生成器，事情是这样的:*

*当使用生成器时，PHP 只跟踪遍历的当前状态(我们用`yield`返回的值)，而不需要存储被遍历的整个集合。因此，循环执行期间的内存使用比第一种方法低得多。*

# ***用生成器减少执行时间***

*对于第二个示例，让我们来看看下面的代码片段:*

*这个简单的脚本遍历一个项目数组，每个项目需要 2 秒钟的时间来获取(因此，模拟一个场景，我们从一个远程资源，例如一个外部文件或 API 获取项目)。我包含了两个不同的计时器来测量我们应该等待的时间，直到第一个项目在循环中可用，以及总的执行时间。*

*对于第一种方法，由于 PHP 将在进入`foreach`循环之前收集所有项目，我们将不得不等待 6 秒。直到第一个项目准备好在循环中使用。这与获取所有项目所花费的时间相同:在获取所有项目之前，我们无法开始处理第一个项目。当然，总执行时间也是 6 秒。*

*通过使用一个简单的生成器向`foreach`循环提供每个项目，我们可以在仅仅 2 秒钟内准备好第一个项目:*

*这个例子的有趣之处在于，它允许您从正在遍历的集合的第 N 项开始工作，而不必担心第(N+1)项。当然，可能有一些用例需要处理第(N+1)或第(N-1)项以及第 N 项，对于这些场景，可能需要进一步的分析。*

```
*>php test.php
Took 6 seconds to get the 1st item
Execution finished in 6 seconds
>
>php test_2nd_approach.php
Took 2 seconds to get the 1st item
Execution finished in 6 seconds*
```

*现在，让我们假设我们可以在依赖于该项的特定条件下停止迭代，就像这样:*

*通过对两个版本执行这段代码(在开始循环之前获取所有项目，而不是按需一次获取一个项目),我们得到:*

```
*>php test.php
Took 6 seconds to get the 1st item
Execution finished in 6 seconds
>
>php test_2nd_approach.php
Took 2 seconds to get the 1st item
Execution finished in 4 seconds*
```

*同样，第一种方法在开始循环之前必须等待每个项目被获取，而第二种方法引入了“按需”行为模式。因此，记住“cat”项是 items 集合中的第二项，一旦满足条件并且`foreach`循环中断，就不需要获取第三项，这使得脚本在最终执行时间上节省了 2 秒。*

# *结论*

*PHP 生成器是一个强大的特性，已经证明它在性能和优化改进方面非常有用。它们不仅允许我们减少内存使用，还可以帮助我们处理缓慢的算法和执行时间相关的问题。*

*虽然本文没有涉及，但是当使用迭代器和创建自己的迭代器扩展时，您应该能够从生成器中获得同样的好处。迭代器是一种更面向对象的选择，通常意味着通过实现所有的*迭代器接口*的方法来创建一个更复杂的解决方案。*

*在一些特殊的场景中，应用生成器可能会很棘手，并且可能会导致更复杂的实现。这些例子包括当遍历集合时，必须访问第 N 个项目以及第(N+1)个或第(N-1)个项目；或者必须处理在同一集合上迭代的嵌套循环。我建议从实现解决方案时最简单的方法开始。*

*然后，一旦你做了一些测量，你肯定有性能问题，尝试应用生成器和/或其他可能有效的替代方法，以重构的形式。不要试图开始使用生成器作为第一种方法，因为事情可能会变得复杂，代码本身可能会隐藏其真实意图。*