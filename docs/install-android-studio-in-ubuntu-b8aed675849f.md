# 在 Ubuntu 中安装 Android Studio

> 原文：<https://betterprogramming.pub/install-android-studio-in-ubuntu-b8aed675849f>

## 让你开始构建 Android 应用的快速教程

![](img/25879e27b8cfc8645a668c2a8e7789c7.png)

在这篇文章中，我们将介绍如何在 Ubuntu 系统中安装 Android Studio。只需遵循以下步骤，开始构建您的 Android 应用程序！

# **安装 JDK 6 或更高版本**

让我们从使用以下命令安装 Oracle JDK 8 开始:

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
sudo apt-get install oracle-java8-set-default
```

要确认安装成功，请打开终端并键入:

```
javac -version
```

将`JAVA_HOME`环境变量设置为您的 JDK 安装的位置。

([http://www.wikihow.com/Set-Up-Your-Java_Home-Path-in-Ubuntu](http://www.wikihow.com/Set-Up-Your-Java_Home-Path-in-Ubuntu))

# **下载并安装 Android Studio**

[下载适用于 Linux 的 Android Studio 包](https://developer.android.com/sdk/index.html)并解压到某个地方(如主目录)。

要启动 Android Studio，打开终端，导航到`android-studio/bin/`目录，执行`studio.sh`。

```
cd android-studio/bin
./studio.sh
```

选择是否要导入以前的 Android Studio 设置，然后单击确定。

将`ANDROID_HOME`环境变量设置为 Android SDK 的安装位置:

```
sudo gedit ~/.bashrcexport ANDROID_HOME=/home/user_directory/Android/Sdk
export PATH=${PATH}:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
```

# **安装 SDK 平台**

你需要安装一个 SDK，然后才能开始构建 Android 应用程序。

`Click on Configure -> SDK Manager`打开 Android SDK 管理器。

选择最新的 API 以便针对目标版本进行测试，例如 API 19 (Android 4.4.2)以及`Extras`中的 Android 支持库和 Android 支持存储库包。然后安装选定的软件包。