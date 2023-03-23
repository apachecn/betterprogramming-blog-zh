# 如何用 3 个步骤编写递归

> 原文：<https://betterprogramming.pub/how-to-write-recursion-in-3-steps-9d512189a94e>

## Python 中的分步示例

![](img/9c782d46a24af0ee03521f3ca2daf285.png)

照片由 [Eddie Tsy](https://unsplash.com/@eddietsy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

递归函数反复调用自己，直到满足某个暂停条件。

这太可怕了。一个函数调用它自己是非常危险的，因为它相对来说不太直观，如果我们不小心对待它，可能会遇到一些运行时错误。我以前觉得这很吓人，但是我可以制定一个三步方法，让你有足够的信心编写自己的无错误递归函数。

# 例子

我们以一个阶乘函数为例。我将使用 Python 来演示它，但是同样的逻辑也适用于其他语言。

## 1.递归情况——流程

首先，我们需要确定递归情况，这就是如何使用阶乘函数定义阶乘函数。我们知道一个事实:

```
n! = n × (n-1) × (n-2) × ... × 3 × 2 × 1
```

这可以用递归方式重写，其中阶乘函数应用于左侧和右侧:

```
n! = n × (n-1)!
```

这可以用 Python 来表达:

```
def factorial(n):
    return n * factorial(n-1)
```

![](img/a0bc9ed47391aeecc9635bf55985a083.png)

[Tine ivani](https://unsplash.com/@tine999?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

## 2.基本情况—停止标准

但是上面的代码有一个 bug。考虑一下`factorial(2)`，它调用`factorial(1)`，它调用`factorial(0)`等等。函数不停地调用自己，这是有问题的。因此，我们需要递归函数的一个基本情况，在这个情况下它停止调用自己。

0 和 1 的阶乘定义为`0! = 1! = 1`。这些是我们的基本案例，我们可以将它们硬编码到我们的函数中。

```
def factorial(n):
    if n in [0,1]:
        return 1
    else:
        return n * factorial(n-1)
```

递归函数在遇到`factorial(1)`时停止调用自己。所以调用`factorial(2)`返回`2 * factorial(1)`，T6 返回`2 * 1 = 2`。

## 3.非故意案例—限制

编写递归函数时，要考虑的最重要的事情是确保函数为每个可能的参数`n`停止。我们确定我们的函数不会永远运行下去吗？

`factorial(-1)`怎么样？那`factorial(1.5)`呢？

![](img/f89d77612726b2c2ca2ececec7af3b05.png)

[Emma Paillex](https://unsplash.com/@emmapaillex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我们有`n = 0`和`n = 1`的基本情况，我们的递归情况用一个参数`n-1`调用函数本身。这意味着，只有当`factorial(n)`以非负整数`n`开始时，函数才会到达基本情况。

例如，`factorial(-1)`调用`factorial(-2)`，T6 调用`factorial(-3)`，我们永远无法到达基本案例。非整数`n`也一样。

让我们防止这种情况发生。虽然有许多不同的方法来实现这一点，但我们将使用 Python 中的一个简单断言方法，如果`n`不是非负整数，该方法将抛出一个错误。换句话说，我们强制参数`n`为非负整数`n >= 0 and int(n) == n`。

# 另一个例子

另一个非常常见的例子是斐波那契数的计算。它由以下递归和基本情况定义:

```
# recursive case
fibo(n) = fibo(n-1) + fibo(n-2)# base cases
fibo(0) = 0
fibo(1) = 1
```

注意，由于`fibo(n)`同时调用了`fibo(n-1)`和`fibo(n-2)`，因此这两种基本情况都需要在函数中进行编码，以避免运行时错误。

最后，约束`n`以避免无意中使用递归函数。简单。

![](img/f3b317f7aa70a6cf71e4cb66983d7454.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 拍摄的照片。

# 警告

使用递归可以使你的代码简洁优雅，因为一个复杂的计算被分解成许多简单的子问题。但是，递归可能计算量很大，并且需要大量内存。

例如，递归斐波那契数函数是一种非常低效的计算方法。想想`fibo(5)`:它调用`fibo(3)`和`fibo(4)`。前者称为`fibo(1)`和`fibo(2)`，后者称为`fibo(2)`和`fibo(3)`。注意`fibo(2)`和`fibo(3)`不必要运行多次。

对于更大的`n`，这个问题变得更加严重。下面可以找到一种更有效的方法来计算斐波纳契数。显而易见，递归更加优雅(尽管有时可读性较差)和紧凑。

# 外卖食品

*   递归的情况是函数的流程。
*   基本情况是停止流程的停止标准。
*   无意的情况可能会导致运行时错误，应该通过限制参数的范围来处理。
*   当心递归函数的内存消耗。

![](img/35ade2f2824842ebb11f92b9644a4319.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

感谢阅读！如果您对提高 Python 技能感兴趣，以下文章可能会有所帮助:

[](https://medium.com/better-programming/stop-abusing-args-and-kwargs-in-python-560ce6645e14) [## 停止在 Python 中滥用*args 和**kwargs

### 他们会回来缠着你

medium.com](https://medium.com/better-programming/stop-abusing-args-and-kwargs-in-python-560ce6645e14) [](https://towardsdatascience.com/4-hidden-python-features-that-beginners-should-know-ec9de65ff9f8) [## 初学者应该知道的 4 个隐藏的 Python 特性

### 如何轻松增强您的 Python 代码

towardsdatascience.com](https://towardsdatascience.com/4-hidden-python-features-that-beginners-should-know-ec9de65ff9f8) [](https://towardsdatascience.com/5-python-features-i-wish-i-had-known-earlier-bc16e4a13bf4) [## 我希望我能早点知道的 5 个 Python 特性

### 超越 lambda、map 和 filter 的 Python 技巧

towardsdatascience.com](https://towardsdatascience.com/5-python-features-i-wish-i-had-known-earlier-bc16e4a13bf4)