# 如何使用 Apple 的 CryptoKit 在 iOS 应用程序中设置端到端加密

> 原文：<https://betterprogramming.pub/how-to-set-up-end-to-end-encryption-in-an-ios-app-using-apples-cryptokit-e94815652e9c>

## 确保您用户的数据得到适当保护

![](img/d3d4c1b85337fabebb6f464ab595a604.png)

约瑟夫·霍查在 [Unsplash](https://unsplash.com/s/photos/keys?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

对于构建应用程序的公司和开发人员来说，安全性是一个非常重要的问题，尤其是在医疗领域，数据泄露会受到严厉的处罚。这就是端到端加密发挥作用的地方。

在本指南中，您将了解如何使用苹果的 [CryptoKit](https://developer.apple.com/documentation/cryptokit) 框架，在适用于 iOS、macOS、其他苹果产品甚至 Linux 的 Swift 应用中实施基本的端到端加密流程。如果你正在使用一个[聊天 api](https://dev.to/mikeranellone/what-is-a-chat-api-513d) 或者你自己的后端构建一个聊天应用，并且需要保护消息的内容，这是特别有用的。

# 什么是 CryptoKit？

> “CryptoKit 是一个新的 Swift 框架，它使执行加密操作比以往任何时候都更加简单和安全，无论您只是需要计算哈希还是实施更高级的身份验证协议。”— [WWDC19:加密技术和您的应用](https://developer.apple.com/videos/play/wwdc2019/709/)

# 什么是端到端加密？

> “端到端加密是一种通信系统，在这种系统中，只有正在通信的人才能阅读消息。任何窃听者都无法获取解密对话所需的密钥——即使是运行消息服务的公司也不行。”— [黑客词典:什么是端到端加密](https://www.wired.com/2014/11/hacker-lexicon-end-to-end-encryption/)

# 你需要什么

CryptoKit 在以下平台上可用:

*   iOS 13.0 以上
*   macOS 10.15 以上
*   Mac Catalyst 13.0 以上
*   tvOS 13.0 以上
*   watchOS 6.0+版
*   Linux(作为 [Swift Crypto](https://dev.to/cardoso/cryptokit-basics-end-to-end-encryption-1d6d#what-is-swift-crypto)

# 第一步。生成密钥对

加密密钥对是端到端加密的核心:公钥是用来为某人加密数据的，私钥是用来解密为您加密的数据的。应用程序中的每个用户都应该有一个密钥对，他们的公钥在一个可信的服务中可供其他用户获取，他们的私钥[安全地](https://developer.apple.com/documentation/cryptokit/storing_cryptokit_keys_in_the_keychain)存储在他们的设备上。可以使用 [Vapor](https://vapor.codes/) 或您选择的另一种语言和框架在 Swift 中编写可信服务。这只是一种服务，经过身份验证的用户可以存储与他们的 ID 相关的公钥，其他用户可以通过提供相同的标识符来获取它。

我们将首先生成一个私钥，然后从中提取相关的公钥，该公钥将被发送到可信服务。在本指南中，我们将使用 [Curve25519](https://en.wikipedia.org/wiki/Curve25519#Popularity) 算法，但其他算法的工作方式应该类似。

公钥有一个`var rawRepresentation: Data`属性，可用于将其序列化为可信服务的有效负载。

# 第二步。加密数据

要为用户(收件人)加密数据，您需要首先从可信服务获取他们的公钥。

初始化器`Curve25519.KeyAgreement.PublicKey(rawRepresentation: Data)`可以用来反序列化来自可信服务的公钥。

## 步骤 2.1 导出对称密钥

公钥不能直接用于加密数据。它们由通信双方使用，以通过 [Diffie-Hellmann 密钥协议](https://en.wikipedia.org/wiki/Key-agreement_protocol)就加密的对称密钥达成一致。为此，我们将使用发送方的私钥和接收方的公钥来生成一个共享密钥，我们可以使用 [HKDF](https://en.wikipedia.org/wiki/HKDF) 密钥派生函数从中派生出对称密钥。

协议 salt 是一个改变对称密钥派生结果的值。为您的用例选择一个保持不变的，例如:`"My Key Agreement Salt".data(using: .utf8)!`。如果你愿意，你可以存储这个对称密钥，以便以后使用苹果公司推荐的私钥策略。但是，如果任何通信用户的密钥对发生变化，就应该丢弃它。

## 步骤 2.2 用对称密钥加密数据

现在我们终于可以使用对称密钥来执行加密了。这项工作可以由 CryptoKit 支持的密码之一来完成。在本指南中，我们将使用 [ChaChaPoly](https://developer.apple.com/documentation/cryptokit/chachapoly) ，根据[亚当·兰利](https://www.imperialviolet.org/2014/02/27/tlssymmetriccrypto.html)和其他研究人员的说法，它比移动设备中的 AES 快三倍。

现在可以安全地将`encryptedData`发送给我们的收件人了。

# 第三步。解密数据

要解密收到的数据，接收方需要导出用于加密数据的相同对称密钥。但是在我们计算它之前，我们需要发送者的公钥。

## 步骤 3.1 导出对称密钥

为了导出对称密钥，我们将执行与步骤 2.1 中相同的过程，除了现在，我们将使用接收者的私钥和发送者的公钥。这将允许我们重新生成共享秘密，我们将使用它来导出相同的对称密钥。

我们只是在用户之间共享了一个对称密钥，而它从未存在于他们的设备之外。

## 步骤 3.2 用对称密钥解密数据

现在我们可以使用对称密钥来解密数据。

实现了端到端加密。

# 包扎

本指南中描述的过程保证了一件事:加密。这意味着为用户加密的数据只能由该用户解密。

[认证和完整性](https://en.wikipedia.org/wiki/Message_authentication)得不到保证，这意味着你无法确定加密的数据是否来自某个人，也无法确定它是否在传输过程中被修改过。因此，你很容易受到某种形式的中间人攻击。

前向保密也没有保证，这意味着如果一个私钥被泄露，所有为密钥所有者加密的数据都可以被解密，直到他们开始使用新的密钥。提供前向保密的方案具有一次性密钥，如果被盗或被破解，只会危及数据的子集。

为了保证这些特性，我们将在以后的文章中研究 CryptoKit 的其他功能。感谢阅读，敬请关注。