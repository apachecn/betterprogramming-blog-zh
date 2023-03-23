# 了解 Android 的构建变体、类型和风格

> 原文：<https://betterprogramming.pub/understanding-androids-build-variants-types-and-flavors-66753340e4df>

## 构建白色标签应用

![](img/3e2cc32c32038143a86ed66030c6cd30.png)

照片由[替代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这个指南将帮助你理解术语，为什么我们需要一个应用程序有多种风格，它还将提供一个创建一切所涉及的过程的逐步分解。

有时，我们需要能够构建应用程序的两个版本:开发版本和生产版本。它们中的每一个都有自己的包名，并且知道哪个是它需要连接的正确的后端。此外，开发人员和测试人员将能够在设备上同时安装这两个应用程序。

我上面提到的代表了一个**构建变体。**简单来说:

```
Build variant = build type + product flavor
```

1.  一个**构建类型**要么是**调试**要么是**发布**。当你从 Android Studio 运行应用程序时，你运行的是该应用程序的调试版本。然而，如果你将你的应用程序发布到任何类型的门户网站(Firebase/PlayStore ),你需要生成一个发布版本。你可以从 Android Studio>Build>Generate signed bundle/APK 中完成。您将使用发布证书来签署此应用程序-稍后将详细介绍。
2.  一个**产品风味**的例子可以是我在介绍中提到的:一种风味可以是`dev`，另一种可以是`prod`。还有其他的情况，比如创建一个`demo`版本的应用或者一个`paid`版本的应用。第三个例子:一个类似优步的应用程序可能有一个`driver`版本和一个`client`版本。

在最后一个例子的基础上，你将拥有这个类似优步的应用程序的 4 个版本:

1.  `driverDebug`
2.  `driverRelease`
3.  `clientDebug`
4.  `clientRelease`

在 dev/prod 示例的基础上，我们还可以添加第三种产品风格(姑且称之为`test`)，它将连接到登台环境，并且也将具有不同的包名。这将导致 6 个构建变体:

1.  `devDebug`
2.  `devRelease`
3.  `testDebug`
4.  `testRelease`
5.  `prodDebug`
6.  `prodRelease`

作为开发人员，您将从 IDE 中运行调试变体。您可以从 Android studio 中选择构建变体。如果您是 React 本地开发人员，您可以从命令行提供所需的变体。

# 如何配置口味

转到你的应用程序的`build.gradle`。在`buildTypes`下，你会看到这个:

```
buildTypes **{** debug **{
      ...**
    **}** release **{** ...
    **}
}**
```

默认情况下配置生成类型。我们需要配置我们的自定义风格。在与`buildTypes`相同的层次上，您需要将以下内容添加到您的`build.gradle`文件中:

```
flavorDimensions "default"
productFlavors **{** myApp **{** dimension "default"
  **}** myAppDev **{** dimension "default"
    applicationIdSuffix ".dev"
  **}** myAppTest **{** dimension "default"
    applicationIdSuffix ".test"
  **}
}**
```

风味维度只不过是一组产品风味。你需要一个——否则，你会遇到这个错误:`Error: All flavors must now belong to a named flavor dimension.`。Gradle 需要一个风味维度来指定每种产品的风味。

我们还为你的应用程序的每种风格添加了自定义后缀，这样你就可以在你的设备上同时安装所有 3 种版本。

如果您的基础包名称是`com.example`，那么您将得到`com.example`的产品版本、`com.example.dev`的开发版本和`com.example.test`的测试版本。

# 配置资源

你还需要进入 Android Studio，在 app > src 下创建一个`myAppDev`和一个`myAppTest`目录。在那里，您将从`main`目录复制并粘贴`res`目录。

在`values`中，你只需要`strings.xml`一个——你可能需要为你的应用程序的每个变体定制一个`app_name`。

# 火基分布

您需要为每个构建变体创建一个单独的应用程序。`myApp dev`、`myApp test`和`myApp`。

在这里，您将分发 3 个应用程序的发布版本。对于每个包，确保输入正确的包名(考虑后缀)。

# 结论

我们回顾了在开始配置同一个应用程序的多个变体之前必须掌握的原则。希望这篇教程对你有所帮助。感谢您的阅读，不要犹豫，开始对话吧！