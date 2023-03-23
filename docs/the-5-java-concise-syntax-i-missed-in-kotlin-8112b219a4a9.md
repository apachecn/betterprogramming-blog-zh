# 我在 Kotlin 中错过的 5 个 Java 简明语法

> 原文：<https://betterprogramming.pub/the-5-java-concise-syntax-i-missed-in-kotlin-8112b219a4a9>

## Java 并不总是比 Kotlin 差

![](img/d4b63255e4701d51d679601a14dd0802.png)

由 [Anthony Tran](https://unsplash.com/@anthonytran?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

从 2016 年开始，在 Android 开发中使用 Kotlin 后，我就再也没有真正错过 Java。总的来说，Kotlin 让我对用 Java 开发 Android 感到非常满意。

尽管如此，偶尔我会发现一些我在 Java 中真正错过的东西。下面是其中的 5 个。

# 1.三元运算符

在 Java 中，对于简单的布尔逻辑，我们可以使用如下的三元运算符，而不必编写 if-else

```
selector ? function1() : function2();
```

但是在科特林，我们将不得不求助于传统的 if-else

```
if (selector) function1() else function2()
```

不算太坏。它只是比我喜欢在 Java 中使用的三元组略显冗长。

# 2.静态变量

在 Java 中，当我们想在一个类中声明一个静态变量时，我们可以使用传统的 C 风格，只需要在前面加上一个`static`关键字

```
class SomeClass {
    static int STATIC_INTEGER = 1
}
```

但是在 Kotlin 中，我们需要使用嵌套的`companion object`

```
class SomeClass {
    companion object {
        val static_integer = 1
    }   
}
```

当然，与 Java 中的`static`相比，`companion object`是 Kotlin 的一个更强大的特性，正如这个 [StackOverflow](https://stackoverflow.com/a/38382433/3286489) 所述。

然而，如果我只想在我的类中有一个静态变量，我必须有另一个嵌套层缩进和额外的行，这让我觉得太像样板文件了。

# 3.按位运算符

在 Java 中，[我们有一个简洁的按位运算符](https://www.geeksforgeeks.org/bitwise-operators-in-java/)。例如，我可以写出自 C 语言诞生以来我们所知道的更传统的位操作方式。

```
int twoPowerOf = 2<<x;
int removeTransparent = 0xFF000000|color;
```

在 Kotlin 中，我们可以通过[使用逐位关键字](https://www.programiz.com/kotlin-programming/bitwise)来实现。

```
val twoPowerOf = 2 shl x
var removeTransparent = 0xFF000000 or color
```

还不算太差。只是它不是传统的。

# 4.循环增量浮点数

当我画了一些图形如下，我需要通过浮点数循环。

在 Java 中，我可以很容易地做到以下几点:

```
for (float i = 0; i <= 10; i += 0.1) {
    // Do something with i
}
```

在科特林，我们没有传统的循环能力。我们还是可以做如下，手动分割:

```
(0..100).*forEach* **{** val i = **it**/10f
    *// Do something with i*
**}**
```

或者使用 while-loop，比如这个 StackOverflow 中共享的[。while 循环的缺点是，必须在循环之外设置和递增控制变量。](https://stackoverflow.com/a/59347640)

```
**var i = 0f**
while (i <= 10f) {
    // Do something with i
 **i += 0.1f**
}
```

# 5.多变量声明

在 Java 中，我们可以声明多个相同类型的变量，如下所示:

```
int w = 8, x = 10, y = 12, z = 14;
```

在 Kotlin 中，由于类型有时是推断出来的，我们最好使用分号:

```
val w = 8; val x = 10; val y = 12; val z = 14
```

只是更啰嗦一点。我想我不能抱怨，因为 Kotlin 拥有隐式类型推理的智能。

没有一种语言是完美的。总的来说，我仍然喜欢科特林，因为它非常简洁。这些都是相对较小且可以忽略的挫折。虽然我希望有一天这些能成为科特林的一部分。

如果您在使用 Kotlin 时有任何其他地方错过了 Java，请分享一下。

感谢您的阅读。