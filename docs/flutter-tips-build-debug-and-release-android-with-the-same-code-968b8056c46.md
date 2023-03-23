# 颤动提示:用相同的代码构建调试并发布 Android

> 原文：<https://betterprogramming.pub/flutter-tips-build-debug-and-release-android-with-the-same-code-968b8056c46>

## 让你的 Flutter 项目对外部贡献者友好

![](img/bfde476891fd9283b182d355ae7a92ce.png)

照片由[布伦丹·丘奇](https://unsplash.com/@bdchu614?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/direction?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# Android 市场通常需要代码签名

包括谷歌 Play 商店在内的大多数移动应用市场在提交应用时都需要代码签名。

代码签名增加了额外的安全层(在你的开发者帐户密码之上),以防止黑客将恶意内容偷偷带入应用程序。

根据官方的 Flutter 文档，要给 Android 添加代码签名，我们需要给`android/app/build.gradle`添加以下代码:

# 代码签名后 Android 调试版本中断

看着上面的代码，有些人可能已经嗅到了危险:如果`keystorePropertyFile`不存在，脚本就会中断。

不幸的是，这可能发生，因为`key.properties`不应该在版本控制中。此外，代码签名凭据不应对外部参与者可见，因此如果不注释掉代码签名脚本，他们将无法在本地调试应用程序:

然而，这是不方便和危险的(有人可能会忘记恢复并将其推入版本控制)。

# 解决方案:使用 ENV 来控制调试/发布

当我们想在不修改代码的情况下在几个配置之间切换时，使用环境变量是更好的选择。

在我们的例子中，我们可以使用环境变量`ANDROID_DEBUG`在调试和发布 Android 版本之间切换。

## 步骤 1:从 Gradle 文件中检索环境变量

在`android/app/build.gradle`中，我们可以用`System.getenv('ANDROID_DEBUG')`检索`ANDROID_DEBUG`环境变量。

## 步骤 2:有条件地绕过凭据文件

由于构建脚本具有区分调试和发布的信息，当`ANDROID_DEBUG`出现以下情况时，我们可以绕过代码签名凭证的检索:

## 步骤 3:有条件地在调试版本和发布版本之间切换

类似地，我们也可以使用环境变量在调试和发布签名配置之间切换:

## 第四步:试一试！

修改完成后，我们现在可以用`ANDROID_DEBUG=true flutter run`在调试模式下启动 Android Flutter 应用，用`flutter run`在发布模式下启动。

由于调试和发布之间的切换只是一个命令行选项，我们可以轻松地与其他贡献者协作，而不会拿我们的证书冒险。

注意:我从 [Mini Donkey](https://medium.com/minidonkey101) 那里借来了脚本，这是一个加密的隐写应用程序，用于保护社交媒体上的隐私。要查看它是如何工作的(或者在 CI/CD 中)，请查看[库](https://github.com/tianhaoz95/photochat)。