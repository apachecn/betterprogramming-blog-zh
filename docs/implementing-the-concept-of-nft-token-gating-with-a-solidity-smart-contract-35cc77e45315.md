# 用可靠智能契约实现 NFT 令牌门控

> 原文：<https://betterprogramming.pub/implementing-the-concept-of-nft-token-gating-with-a-solidity-smart-contract-35cc77e45315>

## 了解令牌门控的工作原理

![](img/c23f28009a9b9797f77c31369007704e.png)

照片由 [Shubham Dhage](https://unsplash.com/es/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

令牌门控的概念用于限制访问，并向特定令牌或一组令牌的持有者提供专有内容、权利或成员资格。

# 智能合同

在这个场景中，我们将编写一个简单的智能契约，它实现了为 NFT 社区提供成员访问的相同概念。

在这个社区中，新成员通过从社区购买至少一个 NFT 来加入，这给了他们出售自己的 NFT 的权利以及对任何已发布的 NFT 进行评论的权利。

这个实现来自我在 Ropsten Testnet 上构建和托管的一个项目，它涉及前端和智能合约，你可以在这里找到托管的应用程序 [NFTGate](https://nftgate.vercel.app/) 以及 Github 上的[智能合约](https://github.com/gracelungu/nft-private-market-contract)。

![](img/26cab38c1caed0ffb1e8d0a06e23ab03.png)

[https://nftgate.vercel.app/](https://nftgate.vercel.app/)

# 安装和设置

我们将跳过所有的安装和设置，因为这不是本文的重点，但是要安装的最相关的库是@openzeppelin。

首先，我们将设置 solidity 版本，并从 Openzeppelin 导入两个重要的契约，这对于我们契约中的`ERC721`实现是必要的。

`ERC721URIStorage.sol`是一个 ERC721 令牌，具有基于存储的令牌 URI 管理功能，而`Counters.sol`是一个帮助我们增加令牌 id 的实用程序。

接下来，我们将创建`PrivateMarket`契约并从`ERC721URIStorage`继承，声明计数器和两个主结构来存储令牌数据和消息。

我们声明`_owner`变量以允许合同所有者发布第一组 NFT，并允许社区成员购买并获得初始发布权。

在我们的构造函数中，我们将把`_owner`的值初始化为发布智能契约的地址。

## 映射存储和易用性的价值

创建上述框架后，我们将声明一组变量来保存和映射令牌、令牌数据、所有者以及附加到令牌的消息:

`TokensData`将保存所有令牌的数据，并将返回以在前端列出所有令牌

`ownerByTokenId`是所有者到其 token in 的映射，将用于检索属于某个地址的所有令牌，或者检查给定地址是否拥有令牌，以便授予发布或评论的权利。

## 带修饰符的访问检查

为了验证访问，我们将创建`ownsToken`修饰符，只允许拥有令牌的地址或`_owner`来执行要调用的函数中的动作。

## 铸造新的代币

使用`ownsToken`修饰符，我们现在可以创建我们的契约中最重要的函数之一，它铸造一个新的 NFT 并将令牌分配给发送者地址。

## 购买代币

为了购买代币，发送者将向`purchaseToken`函数进行交易，该函数反过来将代币转移给发送者并向代币所有者进行支付。

为了避免**重入攻击**，我们将首先转移令牌，然后向令牌的所有者付款，但在此之前，我们首先需要检查令牌是否不属于调用该函数的同一个地址。

# 评论一个 NFT

我们将利用`Message`结构并创建一个函数，该函数只允许社区成员(拥有至少一个令牌的地址)添加附加到令牌的消息，并创建一个函数来检索属于令牌的所有消息。

# 支持前端的附加功能

最后，我们将添加一组附加函数来获取与令牌和令牌所有者相关的数据。

# 下一步是什么？

智能契约应该通过自动和手动测试进行良好的测试。使用 HardHat 或 Truffle，可以编写一组测试来确保契约及其功能按预期工作，最重要的是确保契约的安全性很高。