# 我发现 KMM 的一些错误

> 原文：<https://betterprogramming.pub/some-errors-ive-found-developing-with-kmm-d7480161f15d>

## 我解决的问题和仍然困扰我的问题的列表

![](img/1da2b37f30d06b558ba8ef9bafd45b0d.png)

因此，您正在尝试编写一个 Kotlin 多平台模块。

在用这个新模块为 Android 项目建立了一个构建 gradle 文件之后，你会相信你会:

*   基于 android 构建。
*   然后运行 pod install 将这个新的预编译模块安装到 iOS 项目中。
*   然后你上 Xcode，运行 build，一切都应该解决了。对吗？

不对！

到今天为止，Kotlin 多平台还没有进入测试阶段。所以在这个过程中出现了一些错误。这是我找到的一些。

# MAC 访问问题

确保这些应用程序能够完全访问文件夹(或者至少是 android 和 iOS 项目所需的文件夹):

终端，Xcode，Android Studio。

# MAC 找不到 PodFile

如果您还没有创建一个，一个蓝图是:

如果您已经有了一个 PodFile，可能会发生一些事情:

*   Android Studio 找不到 build.gradle.kts 中声明的 PodFile，因为它不在那里。
*   Android Studio 没有权限搜索该文件夹。

示例:

在这种情况下，请检查文件是否存在以及路径是否正确。

此外，检查 Android Studio 是否有权访问该路径。

如果没有效果，你可以删除在`build.gradle.kts`上声明 podfile 的那一行，在你构建 android 之后手动运行 pod install。

# 麦克 M1 问题

## 为 M1 安装椰子

看看这个答案:

[如何在苹果硅 M1 电脑上安装 Cocoapods？尼古拉·尼科诺夫](https://medium.com/p-society/cocoapods-on-apple-silicon-m1-computers-86e05aa10d3e)

# 模拟器+ M1

如果你使用的是 Mac M1，并且要为模拟器进行构建，下面是你应该做的。

如果你用的是实体 iPhone，不需要 Rosetta。

## M1 +房间图书馆

您应该[更改您的房间依赖值](https://stackoverflow.com/questions/68884589/caused-by-java-lang-exception-no-native-library-is-found-for-os-name-mac-and-o):

```
From: ext.room_version = '2.3.0' 
To: ext.room_version = '2.4.0-alpha03'
```

## Xcode M1 上的 JAVA_HOME

从 Azul 的 M1 OpenJDK builds 下载 OpenJDK。确保使用的默认版本不高于 11。在构建 Gradle 期间，XCode 的错误大于 11 版本。

这里有更[详细的回答](https://developer.apple.com/forums/thread/666681)。

## 找不到[您的共享模块]。XCode 中的 klib

查看您是否为 Xcode 和终端提供了在 android 文件夹的项目中进行搜索的权限。

从 android 项目中删除`.gradle`。

从 ios 项目中删除`.konan`。

进入 android 项目的 gradle 文件夹，输入

```
chmod -R +x * 
-R means recursive through directories 
+x means add to every file the permission to be executed
* means all files and directories in the current folder
```

全部重建。(搭建 Android，Pod 安装，搭建 iOS)。

现在我发现一些苹果公司就是不喜欢这样。klib 文件，似乎找不到它。我仍在试图为一个特别烦人的 mac 解决这个问题，你可以[在这里](https://stackoverflow.com/questions/70974616/kotlin-multiplatform-error-linkpoddebugframeworkios-failed)检查它是如何进行的。

就这样，总结了一个月来我绞尽脑汁解决 KMM 到目前为止遇到的每一个问题。

💗喜欢我的内容吗？请随意[给我买杯☕咖啡](https://www.buymeacoffee.com/lucasschiavini)。
📩点击这里订阅我的独家电子邮件简讯[。
😡不喜欢我的内容？请在评论中分享你的想法！
卢卡斯·斯基亚维尼](https://lucas-schiavini.com/#/portal) [我的全部内容](https://linktr.ee/lucas.schiavini) | [LinkedIn](https://www.linkedin.com/in/lucas-schiavini/)

*最初发表于*[*https://lucas-schiavini.com*](https://lucas-schiavini.com/some-errors-ive-found-developing-with-kmm/)