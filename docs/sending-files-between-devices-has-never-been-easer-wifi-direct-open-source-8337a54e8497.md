# 在设备之间发送文件从未如此简单

> 原文：<https://betterprogramming.pub/sending-files-between-devices-has-never-been-easer-wifi-direct-open-source-8337a54e8497>

## Wi-Fi 直接开源

![](img/87f35a06b592257908edd49b3972396a.png)

# 介绍

您是否尝试过在两台或多台设备之间传输数据？通常，我们使用云和其他方式来传输数据，这需要互联网。在 android 版本中，有一个被认为可以改变世界的功能。它叫做 Wi-Fi Direct。不幸的是，这并没有发生，因为算法太难了，而且不够用户友好。我决定通过使用 P2P 技术来改变这种情况。Google Play 或者 GitHub 里有很多信使。

目前，Google Play 中所有帮助你传输文件的应用程序要么只能通过 FTP 在你的本地网络中运行，要么你必须创建一个热点，例如 SHAREit，或者使用 P2P 协议。但是通常会有意想不到的事情发生。我的应用在 [GitHub](https://github.com/YaphetS1/WiFi-Direct-File-Transfer-App) 和 [Google Play](https://play.google.com/store/apps/details?id=com.app.wi_fi_direct) 上。这里有一个关于它如何与各种设备一起工作的演示。

在我们开始之前:

## **推荐阅读**

[总体概述](http://developer.android.com/guide/topics/connectivity/wifip2p.html)
[服务发现](http://developer.android.com/training/connect-devices-wirelessly/nsd-wifi-direct.html)
[功耗](http://www.drjukka.com/blog/wordpress/?p=95)
[更多推荐阅读](http://www.drjukka.com/blog/wordpress/?p=81)

# 我们开始吧

我有以下任务:

*   显示附近设备的列表。
*   管理连接、断开和重新连接设备。
*   在连接到您的设备之间发送消息，以便进行验证(在官方 API Android 中不可用)。
*   在两台连接的设备上共享多个文件。
*   能够可视化您发送和接收的内容，并且能够在不断开的情况下打开接收到的文件
*   负责接收文件和设备信息的服务器自动重启

我将添加我的代码(FileActivity)和注释，在那里你将能够看到我使用的具体方法，因此发送和接收文件和所有其他功能，以及作为一个活动工作的设计。这是一个被迫的行动，因为主要目标是同时发送和接收文件，而无需重复初始化 WifiP2PManager 等。

让我们来看一个名为`FileServerAsynck`的特定任务:

和`TransferData`:

如我们所见，文件传输是同步的。因此，在未来，我不得不改变代码，以便在发送文件前分割文件。

# 需要做什么:

*   将文件的发送和接收从同时和同步更改为不同步。
*   当前版本不允许传输大于 60mb 的文件。
*   大量设备(目前只有两台)之间的连接
*   等等。

完整的代码可以在我的 GitHub 上看到，[wifi-文件-传输](https://github.com/YaphetS1/WiFi-Direct-File-Transfer-App)。