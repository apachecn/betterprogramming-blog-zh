# 如何用 SwiftUI 和 AWS Amplify 实现登录(第 4 部分)

> 原文：<https://betterprogramming.pub/how-to-implement-sign-in-with-swiftui-and-aws-amplify-part-4-a59144107147>

## 使用电子邮件登录

![](img/97f0d6dd9583cfdbb63695580aa34db2.png)

照片由[亚伦·伯顿](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# **通过电子邮件注册**

在跳转到代码之前，如果您没有用 AWS Amplify 设置您的项目，请检查[第 1 部分](https://medium.com/better-programming/sign-in-with-aws-amplify-and-swiftui-978b01a5cf10)。在这一部分，我完成了项目的设置，还用 Google 添加了一个登录。对于脸书登录，检查[第 2 部分](https://medium.com/better-programming/how-to-implement-sign-in-with-swiftui-and-aws-amplify-part-2-4a7b20bfe8cc)，对于苹果登录，检查[第 3 部分](https://medium.com/better-programming/how-to-implement-sign-in-with-swiftui-and-aws-amplify-part-3-de994e218ed7)。

幸运的是，当用电子邮件登录时，我们不需要配置任何东西，因为 AWS Amplify 会为我们处理一切，所以如果你只是在你的应用程序上使用电子邮件作为提供商，你只需要配置你的 Amplify 项目，忽略谷歌、脸书或苹果的配置。

注册和登录我们需要遵循的步骤是:

1.  输入注册的名称、电子邮件和密码
2.  输入发送到电子邮件的验证码
3.  确认后用电子邮件登录

首先，让我们在`SignInViewController`上实现用电子邮件注册的方法。

`AWSMobileclient`注册方法非常简单，只需要用户名和密码。如果您将 Amplify 项目配置为需要一个电子邮件、一个名字和一张图片，那么您需要在一个数组中传递它们。在这里，我为图片发送一个空字符串，但是如果您有图片的 URL，请随意为您的用户添加它:

更新了通过电子邮件注册的 SignInViewController

由于我们可能希望在使用电子邮件(以及社交提供商)登录时检查令牌，请注意上面对`getToken`方法的重构。

我们还需要更新应用程序设置，以存储我们将发送确认码的电子邮件。这样做是为了即使用户关闭应用程序，我们也会跟踪他们之前输入的电子邮件:

更新的 AppSettings

对于注册，我们将创建一个像其他按钮一样的按钮，但一旦我们按下它，我们希望显示一个文本字段，用户可以在其中输入所需的信息。

我们需要添加几个我们想要跟踪的`State`。我们还需要一个`Bool` `signUpWithEmail`来显示我们按下按钮后的`TextField`。我们还需要为`signUpName`、`signUpEmail`和`signUpPassword`添加`Bindings`。参见下面更新的`ContentView`:

内容视图

注意，我们使用了一个`SecureField`，所以输入的密码不会显示出来。

一旦您按下`Button`，您应该会看到`TextField`并能够调用我们的注册方法。AWS 将向输入的电子邮件发送一个验证码，所以我们需要添加另一个方法来检查它，还需要一个`TextField`来输入代码。

# 电子邮件确认

首先，我们需要更新我们的`SignInViewController`并添加一个电子邮件确认方法:

已为确认更新 SignInViewController

此外，在您的`ContentView`中做相应的更改。这里我们在我们的`if` / `else`上添加另一个条件。如果我们在应用程序设置中存储了一封确认邮件，我们将显示一封确认邮件`View`，让用户输入他们在邮件中收到的代码，并从`SignInViewController`调用方法:

带确认码的内容视图

如果我们运行这段代码，我们应该看到电子邮件被成功确认。这意味着电子邮件已被确认，但尚未登录，所以现在我们需要让用户登录。

# 使用经过验证的电子邮件登录

现在让我们为我们的`SignInViewController`添加一个最终方法，以便能够登录:

已更新 SignInViewController

我们还需要一个 Bool `signInWithEmail`来显示当我们按下按钮时的登录文本字段以及对`signInEmail`和`signInPassword`的绑定。我们还需要向`if` / `else`添加另一个条件，以便它可以显示文本字段:

内容视图

现在你应该能够完成整个过程了:注册一个新的电子邮件，确认该电子邮件，然后用一个现有的电子邮件登录。

如果你想看看整个项目，请看 [GitHub repo](https://github.com/rolisanchez/swiftui-amplify-demo) 。