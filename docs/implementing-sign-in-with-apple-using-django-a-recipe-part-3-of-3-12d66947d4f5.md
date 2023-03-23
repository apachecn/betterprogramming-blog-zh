# 使用 Django 实现苹果登录(第 3 部分)

> 原文：<https://betterprogramming.pub/implementing-sign-in-with-apple-using-django-a-recipe-part-3-of-3-12d66947d4f5>

## 将所有这些放在一起，并将功能添加到您的应用程序中

![](img/cc201fc3012bc450277e8e2dc7a6bb60.png)

这是“使用 Django 实现苹果登录:一种方法”系列的第三部分，也是最后一部分，该系列旨在为开发人员提供一条清晰的路径，以便他们在后端使用 Django 实现 iOS 应用程序中的苹果登录功能。

请参考[此链接](https://medium.com/better-programming/implementing-sign-in-with-apple-using-django-a-recipe-part-1-of-3-fc5695e6e9db)阅读该系列的第一部分，并参考[此链接](https://medium.com/better-programming/implementing-sign-in-with-apple-using-django-a-recipe-part-2-of-3-12d673cf6eaf)阅读第二部分。

# 3.在您的 iOS 应用程序中实现 Apple 登录

现在，剩下要做的唯一一步是在 iOS 应用程序本身。在本系列的[第 1 部分中，我们在应用程序的功能中增加了苹果登录功能。如果您还没有这样做，请确保在开始此步骤之前已经完成。请注意，您需要 Xcode 11 或更高版本来实现此功能，并需要 iOS 13 或更高版本来查看此功能的运行情况。](https://medium.com/better-programming/implementing-sign-in-with-apple-using-django-a-recipe-part-1-of-3-fc5695e6e9db)

现在，让我们假设在我们的 iOS 应用程序中有一个名为`ViewController`的视图控制器。首先，我们将通过编写以下代码将`AuthenticationServices`框架导入到视图控制器中:

```
import AuthenticationServices
```

之后，我们准备好利用`AuthenticationServices`框架提供的所有特性。

## **在视图控制器中增加了使用苹果按钮登录的功能**

要使用 Apple ID 登录，用户首先要点按“使用 Apple 登录”按钮。因此，让我们以编程方式将它添加到我们的视图控制器中，因为到目前为止还没有办法使用界面构建器来添加它。我希望苹果也能引入这一功能。

用 Apple 按钮签到可以通过添加`ASAuthorizationAppleIDButton`添加到我们的视图控制器中。它的行为很像一个`UIButton`，但是我们只能修改它的框架、标题类型、圆角半径和样式。

该按钮根据文本的框架自动缩放文本，甚至自行定位。关于这个按钮的更多信息，可以看[苹果的文档](https://developer.apple.com/documentation/authenticationservices/asauthorizationappleidbutton)。不过，说够了。现在，让我们编写代码来添加它。

这里，我们创建了一个`ASAuthorizationAppleIDButton`的实例，并将其存储到一个名为`signInButton`的常量中。这是一个黑色的登录按钮。之后，我们添加了一个动作，`signInButtonPressed`，当点击`signInButton`或触发其`touchUpInside`事件时会调用该动作。

## 轻按“使用 Apple 登录”按钮会发生什么？

我们已经添加了一个按钮和一个动作`signInButtonPressed`，点击它就会被调用。但是我们还没有写出动作内部实际发生了什么。所以接下来我们要做的是编写`signInButtonPressed`动作的实现，并完成整个 iOS 授权过程。

iOS 授权流程包括三个步骤:

*   请求的创建
*   提供演示上下文
*   处理授权响应

**创建请求** 这是我们编写调用`signInButtonPressed`动作时会发生什么的步骤。在这一步中，请求被创建。

**提供呈现上下文** 呈现上下文提供程序需要一个窗口，用于呈现登录模式。为了提供一个表示上下文，我们需要使我们的视图控制器——即`ViewController` ——符合`ASAuthorizationControllerPresentationContextProviding`。(唷！那是一个很长的名字。)我们的视图控制器只需要实现其中的一个方法，那就是:

**处理授权响应** 我们已经完成了请求和表示上下文的设置，这就把我们带到了处理从 Apple 那里得到的响应的步骤。这是通过使我们的视图控制器符合`ASAuthorizationControllerDelegate`来完成的。

在使我们的视图控制器符合它之后，我们必须实现`ASAuthorizationControllerDelegate`的一个函数，也就是`authorizationController(controller:didCompleteWithError:)`，因为响应可能包含错误，并且有必要处理它们。

为此，我们可以通过使用接受一个`NSError`的初始化器来实例化一个`ASAuthorizationError`对象。

或者，我们可以使用`ASAuthorizationError.Code(rawValue: error.code)`直接实例化代码，它将是`Optional`并可能返回`nil`。此处给出了`ASAuthorizationError.Code`的文档[。下面给出了响应处理的代码:](https://developer.apple.com/documentation/authenticationservices/asauthorizationerror/code)

我们为处理响应中的错误做了必要的准备。现在，让我们编写代码来处理成功的响应。

一个成功的响应由函数`authorizationController(controller:didCompleteWithAuthorization:)`处理，它也属于`ASAuthorizationControllerDelegate`。

下面给出了处理成功响应的代码:

在上面给出的片段中，唯一让我们感兴趣的信息是`authorizationCode`。我们不会存储 Apple 提供的任何数据，因为我们希望我们的后端验证代码，自己获取名称和电子邮件，验证或创建用户，并返回 API 令牌。

为了调用后端来交换 API 令牌，需要实现函数`exchangeCode(_:handler:)`。后端会接收代码并发送给 Apple ID API 接收一个`id_token`，里面包含用户的名字和邮箱。该过程已在本系列的第二部分[中描述。](https://medium.com/better-programming/implementing-sign-in-with-apple-using-django-a-recipe-part-2-of-3-12d673cf6eaf)

将所有代码添加到我们的视图控制器后，它看起来会像这样:

这就是我们如何在我们的 iOS 应用程序中实现与苹果的登录。

我们的“使用 Django 实现苹果登录”系列到此结束我希望这个系列对你有用。

# **参考文献**

*   [认证服务文档](https://developer.apple.com/documentation/authenticationservices)
*   [GitHub 上的 truffls](https://github.com/truffls/sign-in-with-apple-using-django)

如果你觉得这篇文章有用，并且愿意支持我，请"[给我买杯咖啡](https://www.buymeacoffee.com/sagunraj)"

[](https://www.buymeacoffee.com/sagunraj) [## Sagun Raj Lage 正在撰写博客和书籍，并免费教授编程。

### 我是一名 iOS 开发人员，有从事各种领域相关项目的经验，如交通和…

www.buymeacoffee.com](https://www.buymeacoffee.com/sagunraj)