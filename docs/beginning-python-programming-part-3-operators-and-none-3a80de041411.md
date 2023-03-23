# Python 编程入门—第 3 部分

> 原文：<https://betterprogramming.pub/beginning-python-programming-part-3-operators-and-none-3a80de041411>

## 熟悉使用运算符和 none

![](img/b7d910c895ae869adb31670d0d66e810.png)

照片由[安托万·道特里](https://unsplash.com/@antoine1003?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你只是偶然看到这个系列，一定要检查一下零件的 [1](https://medium.com/@broebling/beginning-python-programming-part-1-variables-constants-and-types-1199da1572c8) 和 [2](https://medium.com/@broebling/beginning-python-programming-part-2-reference-types-pointers-and-collection-types-a806ef6b92cf) 。对于其他人，今天我们将讨论运算符和`none`。

[](https://medium.com/@broebling/beginning-python-programming-part-2-reference-types-pointers-and-collection-types-a806ef6b92cf) [## Python 编程入门第 2 部分—引用类型、指针和集合类型

### 之前我们讨论了变量、常量和类型。

medium.com](https://medium.com/@broebling/beginning-python-programming-part-2-reference-types-pointers-and-collection-types-a806ef6b92cf) 

虽然我们仍在讨论基础知识，但好消息是，从今天起，你将能够编写一个程序来解决数学问题，无论是幼儿园的问题还是使用公式进行的高级金融计算。

# 初步材料

你需要理解操作顺序的概念(PEMDAS)。

如果数学课对你来说太久了，顺序如下:

1.  括号(从内向外)
2.  从左到右的指数
3.  从左到右的乘法
4.  从左到右划分
5.  从左向右加法
6.  从左到右的减法

就是这样！

# 经营者

只要你上到三年级，你就已经见过编程中的大多数运算符了。

让我们来看看那些你已经知道的:

```
# Math Operators# Addition
sum = 10 + 20               # 30# Subtraction
difference = 100 - 75       # 25# Multiplication
product = 20 * 5            # 100# Division
quotient = 15 / 3           # 5# Floor Division
quotient = 15 // 2          # 7, a single '/' results in 7.5# Exponent
result = 3 ** 5             # 243# Comparison Operators# Greater than
result = 3 > 4              # False# Less than
result = 3 < 4                            # True# Equal to
result = 3 == 4                           # False# Greater than or equal to
result = 3 >= 4                           # False# Less than or equal to
result = 3 <= 4                           # True# Not equal to
result = 3 != 4                           # True# Inverse (minus)
result = -(-100)                          # 100
result = -(100)                           # -100# Logical Operators# And - all elements must be True, not 0, or an empty string.
result = True and False                   # False# Or - any element must be True, not 0, or an empty string.
result = True or False                    # True# Not
result = not True                         # False# Is - Identity (points to the same object)
result = 1 is True                        # True# In - Membership (resides in range, list, or collection)
my_list = ['eggs', 'milk', 'cheese']
result = 'milk' in my_list                # True
```

简单的事情，对不对？现在让我们来看一个你可能不熟悉的例子:

```
# Remainder - also known as modulo
result = 15 % 10                          # 5
```

好吧。所以最后一个很奇怪。这是我最喜欢的，因为它允许我们做各种很酷的数学，通常会引起问题。例如，如果我们想给字母表中的每个字母分配一个数字，我们想要第 28 个字母(等等，什么？).是的，第 28 封。假设我们从头开始，第 28 个字母应该是 B。我们可以用`%`来完成这个任务。我们可以把它写成`value = 28 % 26`。`value`则等于 2，其中字母表的第二个字母是“B”。这就是凯撒密码的制作过程；Vigenère 密码[后来以同样的方式被开发出来。](https://en.wikipedia.org/wiki/Vigenère_cipher)

运算符在编程中经常使用。这就是程序如何知道如何做*事情*。除了数学，我们甚至可以使用加法运算符。例如，我们可以用它来将*串和*串连接在一起。

```
first_name = "John"
last_name = "Doe"full_name = first_name + " " + last_name     # John Doe
```

请注意，我使用了变量名，而我本可以只键入以下内容:

```
full_name = "John" + " " + "Doe"
```

它展示了我们如何使用变量和常量来保存值，所以我们不必一直输入它们。我肯定你注意到了`first_name`和`last_name`都比它们所保存的值长。我可以把这个例子改成姓 supercalifragilisticepialidocious，但是让事情简单一些。Visual Studio 代码和 PyCharm 都有内置的自动完成功能，这绝对是救命稻草，但除此之外，我们并不总是知道`first_name`或`last_name`的值会是多少。当我们在适当的位置使用变量名时，我们总是可以确保名字后面跟一个空格，后面跟一个姓。

加法、减法、乘法和除法的一种简便方法是将运算符附加到赋值运算符上(通过这种方法，您仍然可以使用加法运算符连接字符串):

```
first_number = 1
second_number = 2first_number += second_number               # 3first_number *= second_number               # 2first_number -= second_number               # -1first_number /= second_number
first_number /= second_number               # Guess the valuename = "John"
last_name = "Doe"name += " " + last_name                    # John Doe
```

希望你现在已经记下了，也许已经尝试自己探索了。练习总是好的。

![](img/7b9c6ae39b5b21ec08f14198cbc0f3d0.png)

由[迈克·提尼翁](https://unsplash.com/@m15ky?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 没有人

`None`是特殊的。意思是不存在。如果你来自其他语言，它就是`null`或`nil`的同义词。

`None`不代表 0，代表没有价值。为了说明这一点，让我们考虑你有一个笔记本电脑放在桌子上。这个笔记本可以有任意多的页数。让我们假设它有 200 页。当你做笔记时，你从笔记本上撕下几页。当你用完了所有的页面，笔记本是唯一剩下的。因为里面没有书页，我们把笔记本扔了。

而笔记本可以代表一个`page_count`变量，保存当前驻留在笔记本中的页数。当笔记本翻到第 0 页时，它对我们不再有用，所以我们删除了笔记本，在 Python 中，它看起来像:

```
del page_count
```

我们现在只有一张空桌子，代表我们的计划。我们可以询问以下内容来确定笔记本(`page_count`)是否仍然存在:

```
notebook_status = page_count is not None         # False
```

我们本来可以检查一下`page_count is None`，但是对于一个`notebook_status`来说就没有意义了。通过添加`not`，我们通过询问`page_count`是否存在来使世界正确。

现在我想用一个例子来补充一些关于`None`的重要内容:

```
page_count = None# is NOT the same asdel page_count
```

为什么？`None`是一个名为`NoneType`的类型。这就像将`null`或`nil`赋给一个变量，除了我们仍将保存对该值的引用，因此垃圾收集不会释放内存。虽然这可能是一种改进，通过保持对内存的引用来保持代码的速度，但如果您希望允许垃圾收集来释放内存，则必须使用`del`，因为该方法也会删除对变量的引用。

# 摘要

咻，今天到此为止！深呼吸。你通过了另一部分。希望您对 Python 必须提供的一些操作符有所了解。

我们还了解了 None 类型，为什么当你想释放内存时，我们会使用这个类型和一个替代方法。

# 下一步是什么

在下一篇文章中，我们将讨论决策和循环。这是事情开始凑在一起的地方，我们开始有一点点乐趣。别忘了在课间练习。在编程中，练习大有帮助。

[](https://medium.com/better-programming/beginning-python-programming-part-4-716a40d5636b) [## Python 编程入门—第 4 部分

### How if 语句、for 循环和 while 循环可以帮助您做出更好的决策，并简化重复的任务。

medium.com](https://medium.com/better-programming/beginning-python-programming-part-4-716a40d5636b)