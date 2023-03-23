# 如何在 Swift 中实施 Face ID、触控 ID 和本地认证

> 原文：<https://betterprogramming.pub/face-id-touch-id-and-localauthentication-df0e20f212b3>

## 便利和安全

![](img/a2d9c4b8075237ee473fb6524111c656.png)

由 [Szabo Viktor](https://unsplash.com/@vmxhu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

难度:初级|简单| **普通** |挑战

# 先决条件

*   能够发出“你好，世界！”iOS 应用程序([指南](https://medium.com/swlh/your-first-ios-application-using-xcode-9983cf6efb71))

这个项目(首先)使用了我的 [HTTPManager](https://medium.com/@stevenpcurtis.sc/my-basic-httpmanager-in-swift-db2be1e340c2) 和 [Keychain](https://medium.com/@stevenpcurtis.sc/secure-user-data-with-keychain-in-swift-337684d6488c) 以及我的[构建 URL](https://medium.com/@stevenpcurtis.sc/building-urls-in-swift-51f21240c537)的方法。

# 术语

*   **Face ID** :在支持设备上使用原深感摄像头进行安全认证
*   `LocalAuthentication`:使用生物特征或密码对用户进行认证的框架
*   触控 ID :指纹身份传感器

# 与触控 ID/Face ID 公司进行服务器呼叫(生物识别)

这个项目是关于在必要的地方使用生物识别技术进行服务器调用(也就是说，它不是关于二级认证)。我们在这里假设稍后调用服务器时需要这个令牌，并且这个令牌需要存储在设备上。

当用户(第一次)登录应用程序时，可以在钥匙串中调用令牌。

当用户随后登录应用程序时，用户可以使用其设备上的生物特征功能来验证自己，并直接进入应用程序。因为我们有了令牌，所以稍后可以在用户会话中使用它。

# 最初的项目

最初的项目使用了我之前在[文档](https://medium.com/@stevenpcurtis.sc/building-urls-in-swift-51f21240c537)中提到的构建 URL 的方法。它遵循一个使用 [https://reqres.in](https://reqres.in/) 的注册和登录过程，你可以用`Postman`来测试这个过程(这里有一个关于如何做的教程)。

除此之外，该项目只是为了找出你是否有一个令牌。

唯一注册并登录的用户是 eve.holt@reqres.in，密码为“pistol”。

这是一个简单的演示项目，所以我让所有多余的东西远离它！

# 履行

注意:这个项目中的很多文件需要`import LocalAuthentication` 才能允许使用 iOS 中的生物识别功能。

为了测试的目的(所以我们不需要把所有东西都放在视图控制器中！)我创造了一个`BiometricsManager`。

对于`LAContext` 来说，这将符合新的协议(`LAContext`已经符合协议，但是需要以下扩展):

对于本协议:

这允许我们定义以下`BiometricsManager`:

然后，我们从一个初始视图控制器调用它，该控制器要么将用户定向到一个登录页面(如果他们没有令牌)，要么检查他们是否是一个在设备上具有生物特征的注册用户。如果检查失败，他们将进入登录屏幕:

# 测试

## LAContextMock

我们为`LAContext`创建一个模拟:

## 生物计量学管理测试

然后使用`LAContextMock`:

# **注**

`LAError.passcodeNotSet`和`LAError.biometryNotEnrolled`由操作系统处理，并自己创建相关的警报。

当触控 ID 或 FaceID 的身份验证返回时，它可能不在主线程上。

# 结论

这个项目不仅仅是 Face ID 的一个实现，而是一个登录系统的实现，这个系统(几乎)已经过全面测试。

生物识别的实际实现(对于感兴趣的人)包含在`BiometricsManagers`中。`BiometricsManagerTests`测试这些功能，也可以在您的特定应用中使用。

# 回购链接

[](https://github.com/stevencurtis/FaceIt) [## 史蒂夫·库尔提斯/费塞特

### 在 GitHub 上创建一个帐户，为 stevencurtis/FaceIt 开发做贡献。

github.com](https://github.com/stevencurtis/FaceIt) 

# 扩展你的知识

苹果已经为 T11 创建了[文档。](https://developer.apple.com/documentation/localauthentication)