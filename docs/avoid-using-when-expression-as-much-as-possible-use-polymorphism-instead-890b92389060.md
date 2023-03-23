# 尽可能避免使用“当”的表达方式。请改用多态性

> 原文：<https://betterprogramming.pub/avoid-using-when-expression-as-much-as-possible-use-polymorphism-instead-890b92389060>

## 给 Android 开发人员的提示

![](img/74aeefdec5ead902e18c76c37b73d7bb.png)

照片由[黑客资本](https://unsplash.com/@hackcapital?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`When`语句通常被认为有[代码味道](https://en.wikipedia.org/wiki/Code_smell)，应该避免使用。

假设`Button`需要开发五个预定义的大小:

*   小型(高度= 16dp)
*   中等(高度= 24dp)
*   大(高度= 32dp)
*   巨大(高度= 40dp)
*   习俗

让我们仔细看看最后一个私有函数`getButtonHeight`，了解它有什么问题。

两个问题与代码复制有关:

1.  来自`Int`的重复创作`Dp`。
2.  附加运行时检查。我们已经在`ViewModel`的某个地方决定了应该使用什么样的按钮高度。为什么要进行额外的检查？没用的。此外，它还会产生运行时开销。花在检查上的 Android 手机资源可能会花在其他更有价值的操作上——例如动画。

让我们摆脱检查。我们所要做的就是用多态性替换`when`表达式。

让我们把这个例子变得更复杂。让我们假设按钮的文本和颜色取决于按钮的大小。我们将考虑这两种情况。
比较下面两个片段:

多态的例子

when 表达式的示例

# 多态性的好处:

*   它使代码库变得更小。
*   更容易扩展功能。
*   它消除了代码重复。
*   它减少了运行时开销。