# 用查找表和哈希表去掉过多的 If-Else 语句

> 原文：<https://betterprogramming.pub/get-rid-of-excessive-if-else-statements-with-lookup-and-hash-tables-c7dbed808996>

## 在所有专业代码库中使用的代码性能实践。通过这些简单的例子学习使用它们

![](img/881fae40008adb811d21bc8441f3904e.png)

马修·费尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

If-else 语句是编程的一个主要部分，自从我们开始我们的旅程，我们就学会了使用它们。虽然它们是很好的工具，但并不总是最好的选择。例如，看看这个愚蠢的 Python 代码示例:

Python 中的 If-else 语句序列。

这个 if-else 序列不仅不必要的庞大，而且非常低效。此外，如果您不得不执行比调用函数更多的操作，这种代码模式会变得非常混乱。

至于执行速度，连续检查所有 if 语句的条件意味着，例如，如果选择的字符是最后一个(龙)，程序将在找到正确的字符之前检查所有其他情况。用[的大 O 符号](https://en.wikipedia.org/wiki/Big_O_notation)来表示，随着输入大小的增长，用于根据速度或空间需求对算法进行分类，if-else 语句序列将被分类为 O(n/2)，或简称为 O(n)。这意味着，随着要检查的案例数量的增加，执行该代码所需的时间呈线性增长。所以，想象一下有更多的角色可以选择。检查所有这些箱子需要多长时间？不必要的长。

# 什么是查找表和哈希表？

查找表和哈希表是可以在运行时用简单的查找代替计算的数据结构，无论是简单的[数组索引](https://en.wikipedia.org/wiki/Array_data_structure#Element_identifier_and_addressing_formulas)还是通过[哈希函数](https://en.wikipedia.org/wiki/Hash_function)。这种表以内存和执行速度为代价，因为它们已经存储了计算结果。

说一下[时间复杂度](https://www.mygreatlearning.com/blog/why-is-time-complexity-essential/#:~:text=Time%20Complexity%20Definition-,Time%20complexity,-is%20the%20amount)，索引一个查找表得分是一个常数 [O(1)](https://en.wikipedia.org/wiki/Lookup_table#:~:text=28%20August%202019.-,Limitations,-%5Bedit%5D) 或者简单的说，不管有多少种情况都有可能，得到结果的时间总是一样的。散列表平均也会得分， [O(1)](https://eng.libretexts.org/Courses/Folsom_Lake_College/CISP_430%3A_Data_Structures_(Aljuboori)/07%3A_Hash_Tables/7.01%3A_Time_complexity_and_common_uses_of_hash_tables) 而在最差的情况下，虽然非常罕见， [O(n)](https://eng.libretexts.org/Courses/Folsom_Lake_College/CISP_430%3A_Data_Structures_(Aljuboori)/07%3A_Hash_Tables/7.01%3A_Time_complexity_and_common_uses_of_hash_tables) 。随着计算量的增加，这种查找和哈希表比一系列 if-else 语句更有效。记住 if-else 序列的时间复杂度是 O(n)。

# 使用案例和示例

那么，这些数据结构能用来做什么呢？让我们回到前一个例子，if 语句的顺序。我们可以用哈希表来代替它们，哈希表在 Python 中也称为字典，在其他语言中称为映射。

如您所见，代码现在更加清晰了。我已经用角色和他们相应的动作创建了一个 Python 字典。然后，我在字典中查找给定的字符，如果它存在，就调用它的函数。

在对 if-else 和 dictionary 方法进行基准测试之后，下面是获得每个字符的结果:

```
If statements: 12700 ns
Hash table:    13000 ns
```

时间差看似微不足道，但如果我们增加可能病例的数量呢？以下是可供选择的 100 和 1000 个字符的结果:

```
# 100 possible cases
If statements: 561996 ns
Hash table:     60057 ns# 1000 possible cases
If statements: 32682664 ns
Hash table:      545019 ns
```

你看到了吗？事例的数量越多，哈希表在执行速度方面就越优于 if 语句。

那么查找表呢？让我们看看另一个代码示例，这次是用 C 语言编写的:

在这里，我定义了一些令牌类型和函数，将给定令牌的类型打印到控制台。这个函数已经很混乱了，但是如果我们要定义许多其他可能的情况，就像在一个成熟的编译器实现中一样，它会很快失去控制。

这就是查找表的用处:

首先，我定义了一个字符串常量数组，它表示要打印的令牌类型的名称。与前面的示例不同，该函数只需检查输入是否在范围内。它利用了枚举的属性，允许我们将它们的元素作为无符号整数使用。这意味着我们可以用枚举元素索引一个数组。不过，重要的是，枚举和对应的数组保持相同的元素顺序。

与 if-else 语句序列的 O(n)相比，数组查找的时间复杂度得分为 O(1)，这是一个显著的改进。在这种情况下，我不会展示基准，但我会更深入地了解这两种方法实际上生成了什么机器指令。感谢[编译器浏览器](https://godbolt.org/)，我们可以毫不费力地获得反汇编 C 代码的用户友好视图。

这里你可以看到 if-else 序列的汇编指令。

如您所见，有许多指令组实际上执行相同的计算。无论是从程序的存储大小还是执行速度来看，这都不是很有效。

下面是查找表方法的组装视图:

很明显，在这种情况下，没有无用的重复。用于`printTokenType()`功能的指令也明显更少，更重要的是，即使我们添加更多的`TokenType`来选择，指令也保持不变。相反，if-else 方法的功能将随着可能情况的增加而线性增长。

# 何时使用 if 语句

If 语句的存在有一个很好的理由:它们简单快捷。如果 if-else 语句序列足够短，它很可能会比哈希表更快，尽管通常不会比数组查找更快。这意味着，在有限数量的可能情况下，if-else 序列优于表。

这种表的另一个必须考虑的特征是，无论是数字、字符串还是任何哈希数据结构(可以通过哈希函数传递的数据结构)，密钥在编译时都必须是常量和已知的。否则，编译器将不知道如何为？因此，函数的结果或用户输入不能用作键，除非表是在运行时动态构建的(与静态表相比，其性能有所下降)。

也有可能将 if 语句与表格结合起来。因为在少数情况下，if-else 序列更快，所以您可以使用 if-else 序列执行最常见的检查，然后将其余的检查留给具有常数时间查找的表，如下所示:

# 结论

在本文中，我们探讨了如何以及何时利用查找表和哈希表，用更省时的算法替换 if 语句。

虽然这些数据结构显然并不总是最好的想法，正如我们在最后一点中所看到的，但它们是供您使用的一个很好的资源。

> 为正确的工作使用正确的工具。

感谢阅读！