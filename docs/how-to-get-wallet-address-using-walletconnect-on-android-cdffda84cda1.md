# 如何在 Android 上使用 WalletConnect 获取钱包地址

> 原文：<https://betterprogramming.pub/how-to-get-wallet-address-using-walletconnect-on-android-cdffda84cda1>

## 使用 WalletConnect SDK 从 Android 应用程序获取加密钱包地址

![](img/c041440c39c8dbdbf1e407310db60228.png)

由[泰佐斯](https://unsplash.com/@tezos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/crypto-wallet?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

区块链技术的发展如此迅速，以至于即使那些没有听说过加密货币或不知道其工作的人也在寻求投资和探索这一领域。根据《独立报》的一项研究，11%的受访英国人表示，如果他们对加密货币或区块链技术更加熟悉，他们会对其感兴趣。随着加密货币的热潮继续在全球蔓延，越来越多的初创公司每天都在开发使用区块链技术的新方法，难怪越来越多的人对投资这个领域感兴趣。

在这篇短文中，我们将学习如何使用 Jetpack Compose 和 WalletConnect 将加密钱包连接到我们的 Android 应用程序。

# **目录**

*   什么是加密钱包？
*   什么是 WalletConnect？
*   什么是 Jetpack Compose？
*   将钱包连接到 Android 应用程序

## **什么是加密钱包？**

当我们谈到加密货币时，有一个最重要的东西叫做加密钱包。CrypoWallets 旨在存储数字资产和验证交易。钱包使用用于签署交易的私钥来保存秘密信息。加密钱包是存储和管理硬币和代币的安全方式，但市场上有几种不同类型的钱包:

1.  热门钱包
    —桌面钱包
    —网络钱包
    —手机钱包
2.  冷钱包
    —五金钱包
    —纸质钱包

## **什么是 WalletConnect？**

[WalletConnect](https://docs.walletconnect.com/) 是一个开放的协议，用于在钱包和 dapp(web 3 应用)之间进行安全通信。该协议使用桥接服务器来中继有效载荷，从而在两个应用和/或设备之间建立远程连接。这些有效载荷通过两个对等体之间的共享密钥对称加密。

连接由一个显示 QR 码或具有标准 WalletConnect URI 的深度链接的对等方发起，并在对方批准该连接请求时建立。它还包括一个可选的推送服务器，允许本机应用程序通知用户已建立连接的传入有效负载。

## **什么是 Jetpack Compose？**

Jetpack Compose 是一个 Android 现代工具，用于创建由 Google 开发人员开发的原生 ui。Jetpack Compose 被比作 iOS 应用程序开发中使用的 SwiftUI。这种类比基于更简单的设计、更少的代码和更多的业务逻辑。

Jetpack Compose 简化了 Android 应用程序中的 UI 开发，并实现了更快的应用程序开发。Jetpack Compose 的座右铭是更少的代码、强大的工具、直观的 Kotlin APIs 和快速的应用程序开发。

## **将钱包连接到安卓应用**

在本节中，我们将把 Wallet 连接到我们的 Android 应用程序，并使用 [Wallet Connect SDK](https://docs.walletconnect.com/quick-start/wallets/kotlin) 获取用户地址。

## **设置依赖关系**

在`build.gradle`(模块)文件中，你需要添加下面的实现。

在这之后添加`kapt`和刀柄插件到`build.gradle`文件的顶部。

将类路径添加到`build.gradle`(项目)文件

并且不要忘记将下面一行添加到你的`build.gradle`文件中。如果你正在使用 Android Studio Canary，你需要将这一行添加到你的`settings.gradle`文件中。然后单击“立即同步”按钮。

```
maven **{** url 'https://jitpack.io' **}**
```

## **设置刀柄模块**

为了连接`WalletConnect`，我们需要一个文件和几个网络对象。为了创建这些对象，我们将使用依赖注入。下面的代码是我们的钱包连接模块。

## **设置 BirdgeServer**

要将我们的应用程序连接到 Wallet，我们需要一个套接字服务器。下面是如何生成这个服务器的代码。

## **创建钱包连接实例**

创建`WalletConnectViewModel`并注入 Moshi、OkHttpClient 和 WCSessionStore。我们需要几个项目`BridgeServer`、`Session.Callback`、会话、`SessionConfig`。生成对象并编写所需函数。

就是这样！当用户点击任意按钮时，调用`connectWallet`函数。如果连接成功，将调用`sessionApproved`函数并更新用户界面。

# **结论**

在本文中，我们研究了如何从 Android 应用程序获取加密钱包地址。为此，我们使用了 WalletConnect SDK。欲了解更多信息，请参见[官方样品应用](https://github.com/WalletConnect/kotlin-walletconnect-lib/tree/master/sample/app)和[样品代码](https://github.com/Enes-Kayiklik/Wallet-Connect-Example)。

**有用的链接**

*   [钱包连接](https://docs.walletconnect.com/)
*   [钱包连接示例代码](https://docs.walletconnect.com/2.0/quick-start/wallets/kotlin)
*   [钱包的种类](https://101blockchains.com/types-of-crypto-wallets/)
*   [什么是加密钱包](https://medium.com/stably-blog/what-is-a-crypto-wallet-7363d3118ce5)
*   [喷气背包撰写](https://developer.android.com/jetpack/compose)
*   [Halil zel 制作的喷气背包是什么](https://halilozel1903.medium.com/jetpack-compose-nedir-b369fb79b957)