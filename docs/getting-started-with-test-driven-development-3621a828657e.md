# 测试驱动开发入门

> 原文：<https://betterprogramming.pub/getting-started-with-test-driven-development-3621a828657e>

## # 100 日代码的第 31 天

## 学习用 TDD 构建坚如磐石的解决方案

![](img/e07cb2e0d6c087bb6886dc984ba81549.png)

在我们开始之前——如果你不熟悉测试驱动开发(TDD)或者 TDD 的三大法则，那么我强烈建议你在开始本教程之前阅读一下。

# 实际例子

我们将编写 TDD 来实现 [Kotlin](https://kotlinlang.org/) 中的`primeFactors`函数:

首先，我们将创建一个名为`PrimeFactorsTest`的类。我们将使用`@Test`注释来指定一个函数作为测试。您将在 IntelliJ 中看到一个绿色箭头，您可以点击它来运行特定的测试功能，而不是运行整个项目。

我们将开始编写我们的第一个测试。我们一写`factorsOf(1)`就会看到一个编译错误。这就是我们遵循 TDD 第二定律编写一些产品代码的时候:创建一个函数，但不要在里面写任何东西。如果你这么做了，你就违反了 TDD 第一定律。

现在我们继续，用“is`(emptyList())`”*完成我们的`@Test`语句，如果我们运行我们的测试，它将失败，因为`factorsOf()`函数不返回任何东西。因此，继续让它返回一个空列表。*

*到目前为止，我们有以下代码:*

```
*package primeFactors

import org.hamcrest.CoreMatchers.`is`
import org.junit.Assert
import org.junit.Test

class PrimeFactorsTest {

    **// Writing first test case**
    @Test fun factors() {
        Assert.assertThat(factorsOf(1), `is`(*emptyList*()))
    }

    private fun factorsOf(number: Int): List<Int> {
        return *emptyList*()
    }
}*
```

*然后我们添加另一个测试，它失败了。*

```
*package primeFactors

import org.hamcrest.CoreMatchers.`is`
import org.junit.Assert
import org.junit.Test

class PrimeFactorsTest {

    // Writing first test case
    @Test fun factors() {
        Assert.assertThat(factorsOf(1), `is`(*emptyList*()))
        **// Adding another test - which will fail
        Assert.assertThat(factorsOf(2), `is`(*listOf*(2)))**
    }

    private fun factorsOf(number: Int): List<Int> {
        return *emptyList*()
    }
}*
```

*在修改我们的函数之后，我们的测试用例通过了。现在我们写一个`factorOf(3)`的测试用例，它仍然通过。不需要修改代码库。*

```
*package primeFactorsimport org.hamcrest.CoreMatchers.`is`
import org.junit.Assert
import org.junit.Testclass PrimeFactorsTest { @Test
    fun factors() {
        Assert.assertThat(factorsOf(1), `is`(*emptyList*()))
        Assert.assertThat(factorsOf(2), `is`(*listOf*(2)))
        Assert.assertThat(factorsOf(3), `is`(*listOf*(3)))
    } **private fun factorsOf(number: Int): List<Int> {
        val result = *mutableListOf*<Int>()
        if (number > 1) {
            result.add(number)
        } 
        return result
    }**}*
```

*如果我们添加了`factorsOf(4)`，它就失败了，所以我们回过头来修改代码。*

```
*package primeFactorsimport org.hamcrest.CoreMatchers.`is`
import org.junit.Assert
import org.junit.Testclass PrimeFactorsTest { @Test
    fun factors() {
        Assert.assertThat(factorsOf(1), `is`(*emptyList*()))
        Assert.assertThat(factorsOf(2), `is`(*listOf*(2)))
        Assert.assertThat(factorsOf(3), `is`(*listOf*(3)))
        **Assert.assertThat(factorsOf(4), `is`(*listOf*(2, 2)))**
    } **private fun factorsOf(number: Int): List<Int> {
        var remainder = number
        val result = *mutableListOf*<Int>()
        if (remainder > 1) {
            if (remainder % 2 == 0) {
                result.add(2)
                remainder /= 2
            }
            if (remainder > 1) {
                result.add(remainder)
            }
        }
        return result
    }**
}*
```

*现在，我们的测试将通过`4`，如果我们增加更多的测试— `5`、`6`、`7`。他们都通过了，直到我们写`8`。*

```
*Assert.assertThat(factorsOf(1), `is`(*emptyList*()))
Assert.assertThat(factorsOf(2), `is`(*listOf*(2)))
Assert.assertThat(factorsOf(3), `is`(*listOf*(3)))
Assert.assertThat(factorsOf(4), `is`(*listOf*(2, 2)))
Assert.assertThat(factorsOf(5), `is`(*listOf*(5)))
Assert.assertThat(factorsOf(6), `is`(*listOf*(2, 3)))
Assert.assertThat(factorsOf(7), `is`(*listOf*(7)))
**// They all pass****// Except this one
Assert.assertThat(factorsOf(8), `is`(*listOf*(2,2,2)))***
```

*将`factorsOf()`函数修改如下后，它将通过。*

```
*private fun factorsOf(number: Int): List<Int> {
    var remainder = number
    val result = *mutableListOf*<Int>()
    if (remainder > 1) {
        **while** (remainder % 2 == 0) {
            result.add(2)
            remainder /= 2
        }
        if (remainder > 1) {
            result.add(remainder)
        }
    }
    return result
}*
```

*现在我们来做`9`。*

```
*Assert.assertThat(factorsOf(9), `is`(*listOf*(3, 3))) // Fails*
```

*所以让我们复制。我们将在它通过后立即重构。*

```
*private fun factorsOf(number: Int): List<Int> {
    var remainder = number
    val result = *mutableListOf*<Int>()
    if (remainder > 1) {
        while (remainder % 2 == 0) {
            result.add(2)
            remainder /= 2
        }
        **while (remainder % 3 == 0) {
            result.add(3)
            remainder /= 3
        } // We know how to optimize the algo further**
        if (remainder > 1) {
            result.add(remainder)
        }
    }
    return result
}*
```

*我们优化我们的算法，得到这个:*

```
*private fun factorsOf(number: Int): List<Int> {
    val result = *mutableListOf*<Int>()
    var remainder = number
    var divisor = 2
    while (remainder > 1) {
        while (remainder % divisor == 0) {
            result.add(divisor)
            remainder /= divisor
        }
        divisor++
    }
    return result
}*
```

*那么，我们是如何得到这个算法的呢？只要遵循 TDD 的三个法则:编写代码通过一个接一个的测试用例。是不是很神奇？*

# *编写坚如磐石的单元测试的额外技巧*

*   ***全面**:涵盖所有场景，包括成功案例、失败案例、边界案例。*
*   *可重复的:不管你运行多少次，结果都是一样的。*
*   ***专注:**应该专注于测试一件且只有一件事，这样你就可以在测试失败的情况下精确定位你的代码。*
*   ***验证行为:**测试应该让您相信系统的行为符合预期。*
*   ***快速:**最重要的是，测试应该运行并立即给出错误，如果有的话。*
*   *简洁:你的测试应该简短，易于阅读和理解，因为它们是极好的文档来源。*

*第 31 天到此为止。31 天写了 31 篇。谢谢你所有的支持，一直激励我粉碎它！*

*我希望你喜欢阅读这篇文章。你美得令人窒息！*