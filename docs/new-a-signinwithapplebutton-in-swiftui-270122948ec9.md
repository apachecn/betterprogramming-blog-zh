# 新:SwiftUI 中的 SignInWithAppleButton

> 原文：<https://betterprogramming.pub/new-a-signinwithapplebutton-in-swiftui-270122948ec9>

## 苹果公司已经使得使用苹果功能实现新的登录更加容易

![](img/02cbd107697f0736544f6a5f37b340c6.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Yoal Desurmont](https://unsplash.com/@yoal_des?utm_source=medium&utm_medium=referral) 拍摄的照片。

正如在 WWDC 2020 上所展示的，苹果已经使苹果登录变得更加容易。只需调用`struct`，你就能让苹果在你的屏幕上登录。

> *“您添加到您的界面以允许用户使用其 Apple ID 登录的控制。”—* [*苹果的文档*](https://developer.apple.com/documentation/swiftui/signinwithapplebutton)

# 先决条件

要学习本教程，您需要了解以下方面的一些基本知识:

*   迅速发生的
*   至少 Xcode 12+

*注意:这只支持 iOS 14+。*

# SignInWithAppleButton

您可以表明您是想注册还是想登录。在这里，您将进行登录:

1.  在请求范围内，您可以指定希望从用户那里收集哪种信息。目前，您仅收集全名和电子邮件地址。
2.  认证成功。在这里，您将能够获得存储在数据库中的凭证和信息。
3.  如果身份验证失败，您将能够通过提供的描述识别错误。

![](img/01defbaebb6f536f59e356f5197c5f2f.png)

如果您想注册，只需将`.signIn`更改为`.signUp`，然后运行项目:

![](img/fc7125c43bb809f3d013e3cad7be7cd7.png)

还有`.continue`:

![](img/5752187ff7942587087c1f10602b7fd6.png)

感谢阅读！