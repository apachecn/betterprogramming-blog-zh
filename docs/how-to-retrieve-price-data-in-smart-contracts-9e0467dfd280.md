# 如何在智能合约中检索价格数据

> 原文：<https://betterprogramming.pub/how-to-retrieve-price-data-in-smart-contracts-9e0467dfd280>

## 在单个函数调用中获取加密价格

![](img/020b869cb94fe6b9da9e7770144994f5.png)

米洛斯拉夫·hamřík 在 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3990806) 上的照片。

在过去的一个月里，链接令牌的价格几乎翻了一番，在很短的时间内从稳定的 4 美元涨到了惊人的 8 美元。这在很大程度上要归功于 Chainlink Oracle network，它提供了一个平台，可以从智能合约中调用[任何外部 API，以及一个](https://medium.com/better-programming/how-to-call-apis-from-ethereum-smart-contracts-e2f1500198c7)[可验证的随机函数](https://medium.com/coinmonks/how-to-generate-random-numbers-on-ethereum-using-vrf-8250839dd9e2)以确保可靠性。

虽然这两个产品都是非常强大的工具，但是它们遵循*请求&接收*周期，这不是瞬时的。这意味着任何使用它们的智能契约都必须在一个事务中请求数据，然后在另一个事务中等待接收响应。

这很好，但是有一种更快的方法可以在单个事务中检索链外数据。

# 对速度的需求

在撰写本文时， [DeFi 生态系统锁定的价值超过 30 亿美元](https://defipulse.com/)。但是对于我们这些不熟悉经济学的人来说，“锁定价值”意味着什么呢？

本质上，它代表了其他资产的抵押品，如稳定的硬币。任何分散稳定的硬币都有其他资产支持，所以如果有人想以公允价值赎回硬币，他们可以这么做。

## 如何印钞票

让我们看一下 SAI 的例子。

![](img/43fbe8769b96f4eb93b0728f7112a3b4.png)

日本短剑

SAI 由 ETH 支持。这意味着如果你想获得 SAI，你需要将 ETH 存放到 MakerDAO 协议中。一旦存款，协议将根据 ETH 的价格和您存款的金额，为您铸造等值的 SAI。

例如:如果当时 ETH 值 200 美元，而您存入 2 个 ETH，则协议会生成 400 SAI。

存款 1 ETH，铸币 200 SAI。

如果 ETH 的价格发生变化，铸造的 SAI 数量也会发生变化。因此，如果 ETH 值 100 美元，而你存了 1 ETH，它只能铸造 100 SAI。

由于协议取决于 ETH 的价格，因此必须有可靠的数据源来实时跟踪价格，以保持系统的平衡。*请求和接收*周期可能没问题，但是因为它不在单个交易中检索价格，所以它不能成为铸造函数的一部分。它要么必须由一些其他功能来跟踪(这意味着当铸造 SAI 时，协议对 ETH 价格的了解可能稍微过时)，要么铸造过程需要跨越多个交易(这意味着存款人必须等待接收他们的 SAI)。

# 填补空白

幸运的是，有一个解决方案，它以 [Chainlink Price Feeds](https://docs.chain.link/docs/using-chainlink-reference-contracts) 的形式出现。

价格提要是包含价格数据的链上资源，这些数据是从多个独立的来源汇总的。它们快速、可靠、在单个事务中执行，并且比迄今为止讨论的任何其他 Oracle 解决方案都更容易实现。

## 如何使用价格源

这些提要是专门为 DeFi 空间策划的，当前可用的所有提要的完整列表可以在 [Chainlink 开发者文档](https://docs.chain.link/docs/reference-contracts)中找到。

对于本例，我们将使用 Kovan testnet 上的 ETH/USD 价格源:

PriceConsumer.sol

让我们浏览一下这段代码:

*   第 3 行:Chainlink 在其契约库中提供了许多有用的契约，我们正在导入`AggregatorInterface`。这个契约定义了一些函数，使我们的契约能够向它请求信息。**(注:截止 2020 年 8 月，** `**AggregatorInterface**` **已弃用。请使用** `[**AggregatorV3Interface**](https://docs.chain.link/docs/get-the-latest-price)` **。)**
*   第 14-16 行:在构造函数中，我们用一个新的`AggregatorInterface`和我们想要使用的提要地址初始化我们的`priceFeed`状态变量。在这种情况下，它是 Kovan testnet 上的 ETH/USD 提要。
*   第 21-23 行:这里，我们通过调用`priceFeed.latestAnswer()`获得最新价格。它返回从多个来源汇总的最新价格。
*   第 28-30 行:我们还可以获得聚合器中最后一次更新价格的时间戳。

# 结论

Chainlink 价格馈送非常容易使用，在我看来，目前在以太坊空间中没有得到充分利用。它们提供了您的智能合约可以访问的最快、最简单、最可靠的价格数据。

从官方 Chainlink 博客文章中了解更多信息:

[](https://blog.chain.link/fetch-current-crypto-price-data-solidity/) [## 获取以太坊、比特币和加密的价格

### 将价格数据提取到 Solidity 智能合约中是 DeFi 应用程序的常见要求。要获得当前…

博客.连锁.链接](https://blog.chain.link/fetch-current-crypto-price-data-solidity/) 

# 进一步阅读

如果你对区块链开发感兴趣，我会写一些教程、演练、提示以及如何开始和建立投资组合的技巧。查看以下资源:

[](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248) [## Medium 上最好的区块链开发资源

### 学习区块链、以太坊和 DApp 开发的资源列表

medium.com](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248)