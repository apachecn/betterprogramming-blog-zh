# 探索 Android 中的自动填充框架

> 原文：<https://betterprogramming.pub/exploring-the-autofill-framework-in-android-672221ccdc6a>

## 使用自动填充框架填补论坛和用户数据之间的空白

![](img/1b19b0805c34642b43ab1b18103590f3.png)

由[克里斯蒂安·威迪格](https://unsplash.com/@christianw?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

填写表格是一件既费时又乏味的工作。大多数人对长表格或忘记密码感到沮丧。为了填补论坛和用户数据之间的空白，Android 团队引入了 autofill 框架，该框架在 Android 8.0 (API level 26)及更高版本中可用。

当你在手机上使用应用程序时，你可能会看到密码管理应用程序自动填写登录信息。填充其他应用程序视图的应用程序被称为*自动填充服务。*

通过为自动填充优化您的应用程序，您可以提供更复杂的用户行为，这有助于他们避免输入错误并节省时间，因为他们不需要多次输入相同的输入。

# 成分

当我们从顶层看到自动填充框架时，我们主要有三个组件:数据源、目的地和中介。

*   **自动填充服务:**密码管理器等应用程序充当数据源，存储可在应用程序间使用的数据
*   **自动填充客户端:**这些是要填充数据的目标应用程序
*   **Android 系统:**这是帮助上述两个组件协同工作的中介

# 自动填充提示

## 为什么我们需要实现自动填充提示？

正如我所说的，数据的来源是一个自动填充服务——他们试图使用试探法来确定视图。因此，如果他们改变行为，自动填充也会改变。为了确保自动填充服务与视图一起正确工作，我们需要提供自动填充提示。

## 如何实现自动填充提示

我们可以在 XML 布局或 Kotlin 类文件中实现它们。

首先，让我们看看如何使用属性`[android:autofillHints](https://developer.android.com/reference/android/R.styleable#View_autofillHints)`在 XML 中实现一个简单的提示。下面的代码演示如何在 XML 中实现密码提示。看一看:

XML 中的自动填充提示实现

现在，我们可以通过在视图上使用`setAutofillHints`在 Kotlin 类文件中实现同样的功能。看一看:

```
edtPassword.**setAutofillHints**(View.AUTOFILL_HINT_PASSWORD)
```

自动填充框架不会验证提示常量。以下是一些预定义的提示常量:

*   我们可以在创建帐户或首次登录时使用`[AUTOFILL_HINT_NEW_USERNAME](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_NEW_USERNAME)`和`[AUTOFILL_HINT_NEW_PASSWORD](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_NEW_PASSWORD)`
*   我们可以使用`[AUTOFILL_HINT_CREDIT_CARD_NUMBER](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_CREDIT_CARD_NUMBER)`、`[AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_DAY](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_DAY)`、`[AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_MONTH](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_MONTH)`、`[AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_YEAR](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_CREDIT_CARD_EXPIRATION_YEAR)`和`[AUTOFILL_HINT_CREDIT_CARD_SECURITY_CODE](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_CREDIT_CARD_SECURITY_CODE)`等提示来自动填充卡片相关的详细信息
*   我们还可以使用以下常量自动填充与地址相关的字段:`[AUTOFILL_HINT_POSTAL_ADDRESS_STREET_ADDRESS](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_STREET_ADDRESS)`、 `[AUTOFILL_HINT_POSTAL_ADDRESS_EXTENDED_ADDRESS](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_EXTENDED_ADDRESS)`、 `[AUTOFILL_HINT_POSTAL_ADDRESS_LOCALITY](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_LOCALITY)`、 `[AUTOFILL_HINT_POSTAL_ADDRESS_REGION](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_REGION)`、 `[AUTOFILL_HINT_POSTAL_ADDRESS_COUNTRY](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_COUNTRY)`、 `[AUTOFILL_HINT_POSTAL_CODE](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_CODE)`和 `[AUTOFILL_HINT_POSTAL_ADDRESS_EXTENDED_POSTAL_CODE](https://developer.android.com/reference/androidx/autofill/HintConstants#AUTOFILL_HINT_POSTAL_ADDRESS_EXTENDED_POSTAL_CODE)`

# 自动填充的重要性级别

我们可以通过使用`[android:importantForAutofill](https://developer.android.com/reference/android/R.styleable#View_importantForAutofill)`属性**告诉系统层次结构中的视图是否需要自动填充。**默认使用`[IMPORTANT_FOR_AUTOFILL_AUTO](https://developer.android.com/reference/android/view/View#IMPORTANT_FOR_AUTOFILL_AUTO)`。看一看:

自动填充重要性级别

我们还可以通过使用`setImportantForAutofill`函数来设置 Kotlin 类的重要性级别。看一看:

```
tvSample.**setImportantForAutofill**(View.IMPORTANT_FOR_AUTOFILL_NO);
```

默认情况下，它接受五种不同的值，例如:

*   `auto`:这是默认值，让 Android 系统使用它的试探法来确定视图是否对自动填充至关重要
*   `no`:该值表示该视图对自动填充不重要
*   自动填充
*   `yes`:该值表示该视图对于自动填充至关重要
*   `yesExcludeDescendants`:这个视图对于自动填充很重要，但是它的子视图对于自动填充并不重要

# 强制自动填充请求

有时，您需要强制自动填充请求来响应用户的操作。在这种情况下，我们可以使用自动填充菜单项，它出现在长按上。下面的代码示例显示如何强制自动填充请求:

我们还可以使用`cancel()`功能取消自动填充上下文。如果用户清除文本，这将非常方便。

# 奖金

要了解有关 Android 高级开发的更多信息，请阅读以下文章:

*   [“学习如何在 Android 中创建矢量资产”](https://levelup.gitconnected.com/vector-asserts-in-android-f0774ba8bf92)
*   [“如何将 Google Pay 集成到您现有的 Android 应用中”](https://medium.com/better-programming/how-to-integrate-google-pay-into-your-existing-android-app-d75b269cd623)
*   [《安卓产品口味》](https://medium.com/@sgkantamani/android-product-flavors-eb526e35f9f1)
*   [“如何创建和发布 Android 库”](https://medium.com/@sgkantamani/how-to-create-and-publish-an-android-library-f37bf715932)
*   [“Android 中适配器的演变”](https://medium.com/better-programming/evolution-of-adapters-in-android-2e2ff58c0f98)

目前就这些——希望你学到了有用的东西。感谢阅读。