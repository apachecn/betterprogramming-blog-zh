# 探索 Jetpack Compose 的热重新加载功能

> 原文：<https://betterprogramming.pub/exploring-jetpack-composes-hot-reload-feature-3fdb5fa43ba1>

## 在 Jetpack Compose 中实现热重装

![](img/5a05fe0ee48bf38b7fc609598e9c40b5.png)

由[miko aj](https://unsplash.com/@qmikola?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我一直非常羡慕 Flutter 中的热重装功能。或许谷歌也感受到了这种羡慕，去年年底推出 Compose 的时候，也推出了热重装功能。这是 Android 编程中第一次热重装出现。

热重装是一个显著的优势，因为它可以缩短开发时间，我认为这个优势足以启动 Jetpack Compose。如果是，如何在 Compose 中实现这种热重载？在这篇文章中，我们将了解它。

先说答案，所有文字都被提取为`MutableState`，热重载是通过跟踪这个`State`实现的。因为所有文件中的文字都被提取，所以所有与 Compose 无关的文件中的文字也用`MutableState`提取。我们来看看是怎么提取的。

下面是一个使用“再见世界”文字的地球函数:

上面的函数在编译过程中是这样转换的。

我们来分析一下上面转换后的代码。

1.  创建一个名为“LiveLiterals${filename}”的对象(第 5 行)
2.  创建一个接收初始值的变量(第 6 行)
3.  创建一个`MutableState`对象来保存文本(第 7 行)
4.  创建一个导入文字的函数(第 8–19 行)

它由 4 个步骤组成。每个变量和函数的命名是通过结合相应文字的位置和所用函数的签名来唯一确定的。

到目前为止，只有`Int`、`String`、`Color`、`Dp`和`Boolean`支持这种转换，并且由于只支持文字，所以准确地说，它被称为 live-literal 而不是 hot-reload。

让我们再来看看上面转换后的代码的第 4 步。如果你看第 4 步的第 10 行到第 17 行，如果没有初始化的`MutableState`对象，就用一个叫`liveLiteral`的函数初始化，然后返回`MutableState`。这里用于初始化的`liveLiteral`函数是什么，文字值更新在哪里？

我们先来看一下`liveLiteral`函数。这是在位于 Android x . compose . runtime . internal 的 LiveLiteral.kt 文件中定义的。

它是通过将键和值放入接收`String`和`MutableState`的`HashMap`变量中来实现的。这样，`liveLiteral`功能实现起来相对简单。

更新文字值是通过`updateLiveLiteralValue`函数完成的。

为了调用这个函数，必须从 Android Studio 编辑器接收输入，并用相应的值调用它。换句话说，在上面转换后的代码中没有使用(不能使用)这个函数，而是用编辑器中输入的值直接从 Android Studio 中调用。

这样，当`State`被 LiveLiteral 改变并且重组继续进行时，Android Studio 使用`findEffectiveRecomposeScope`函数仅重组使用该文字的最小范围。

有了这个功能，我们可以优化使用 LiveLiteral。我猜有时候 LiveLiteral 不行的原因是上面的函数因为某种原因返回 null。

## 性能优化

如果 LiveLiteral 只应用于使用 Compose 的部分就好了，但是由于它应用于所有不使用 Compose 的文件，所以从这么多文件中提取文字作为`State`。如果有 100 个字面值，这对性能会有非常严重的影响，因为所有这 100 个字面值都会被`State`跟踪。因此，为了优化性能，应该对不使用 LiveLiteral 的文件关闭 LiveLiteral 功能。

我们可以通过简单地注释`@NoLiveLiterals`来禁用 LiveLiteral。

此外，如果在发布模式下构建，将对所有文件禁用 LiveLiteral。这就是我们在进行基准测试时应该使用发布模式的原因。

有一个组合插件选项，即使在调试模式下也可以禁用所有文件的 LiveLiteral。如果`liveLiterals`选项设置为 false，LiveLiteral 被禁用，但会弹出一个错误，说这是一个重复的插件选项，所以我就跳过它。一旦成功，我会尽快更新这篇文章。

相关问题:[https://stack overflow . com/questions/73172453/how-can-I-use-the-liveliterals-plugin-option](https://stackoverflow.com/questions/73172453/how-can-i-use-the-liveliterals-plugin-option)

在本文中，我们研究了 Jetpack Compose 如何实现热重载(live-literal)特性。感谢阅读。

```
[[View in Korean]](https://sungbin.land/%ED%94%8C%EB%9F%AC%ED%84%B0%EC%97%90%EC%84%9C%EB%A7%8C-%EB%90%98%EB%8D%98-hot-reload-%EC%BB%B4%ED%8F%AC%EC%A6%88%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EA%B5%AC%ED%98%84%ED%96%88%EC%9D%84%EA%B9%8C-2ab37c45ed68)
```