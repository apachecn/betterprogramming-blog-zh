# 如何使用 JavaScript 向 DeFi 协议提供资产

> 原文：<https://betterprogramming.pub/how-to-supply-assets-to-defi-protocols-using-javascript-df6c6d31e58d>

## 构建您的应用程序，与复合金融互动

![](img/abd48e32dec680667415abc943c94b7c.png)

[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照。

DeFi 已经成为以太坊生态系统中一个非常流行的用例。在撰写本文时，DeFi 协议锁定了超过 10 亿美元。迄今为止，用开放、分散的替代产品取代现有金融产品的前景已被证明非常受欢迎。

这些 DeFi 平台之所以伟大，是因为它们易于开发人员使用。由于它们的开源性质，它们是可以理解的，任何愿意学习的人都可以使用。

其中最大的是复合融资，锁定价值约为 1.15 亿美元。任何人都可以向他们的协议提供资产，并开始从中获利，但是这个过程是如何工作的呢？

# 复合金融是如何运作的？

化合物目前在九个市场发挥作用:英美烟草、戴、瑞士联邦理工学院、代表、SAI、USDC、USDT、WBTC、ZRX。这些是你可以从中赚取利息的数字资产。

我们以戴为例。一旦您向协议提供 DAI，该值就被锁定，直到您决定撤销它。然而，当它在那里的时候，你同意协议可以借出那个值，因此赚取整个壶的利息。把它想象成一个储蓄账户。

一旦它在那里，你可以随时查看有多少戴，目前的利率是多少，借用其他资产，等等。

## 让我们来谈谈技术

从技术角度来看，这个供应过程是什么样的？

该协议为每个受支持的市场使用符合 ERC20 的合同。他们被称为 *cTokens* 。它们都有一个外部函数:`mint`，发送所提供的值。此函数接收所提供的值，根据汇率计算 cTokens 的等效数量，并将它们交换为所提供的值。

例如，如果汇率是 1:1，那么提供一个 ETH 将导致发送方接收一个 cETH。

与`mint`相对的是一个叫做`redeem`的函数。当用户想要提取他们的锁定值时，使用所需数量的 cETH 令牌调用`redeem`，将根据当前汇率返回 ETH 的等值。

*注意:还有一个* `*redeemUnderlying*` *函数使用基础资产单位而不是 cToken 单位作为输入。*

# 编写代码

*先决条件:本演练假设您对 JavaScript、Web3 和以太坊智能合约有基本的了解。*

我们将按照以下步骤供应和赎回 ETH 以实现复合融资:

1.  加载 cETH 合同。
2.  通过调用`mint`，锁定 ETH，并接收 cETH 令牌来提供 ETH。
3.  阅读我们的 cETH 平衡。
4.  读取我们锁定的 ETH 余额。
5.  通过提供 cETH 从协议中赎回 ETH。

## 步骤 1:加载 cETH 合同

每个令牌协定在每个网络上都有不同的地址。cToken 合同的 ABI 可在[https://compound.finance/docs/abi/<网络> / < cToken >](https://compound.finance/docs/abi/%3Cnetwork%3E/%3CcToken%3E) 公开获取。

在我们的场景中，使用 cETH 和 Kovan testnet，我们的 URL 是 https://compound.finance/docs/abi/kovan/cETH。

每个网络上每个合同的地址可以在 Compound 的官方文档中找到。

使用 web3，我们像这样初始化我们的契约实例:

```
const cEthInstance = new web3.eth.Contract(cEthABI, addr);
```

## 步骤 2:供应 ETH

图 1 显示了使用 web3 的`send`功能来`mint` cTokens 是多么容易:

图 1:铸造 cTokens

在这个阶段，我们的 ETH 将根据当前利率开始赚取利息(也可以使用[数学公式](https://compound.finance/docs#protocol-math)计算)。

## 第三步:阅读我们的 cETH 余额

因为 cToken 契约符合 ERC20，所以它们提供了一个名为`balanceOf`的功能。我们可以通过调用这个并将我们的帐户地址传递给它来检查我们的 cETH 的余额:

```
let b = await cEthInstance.methods.balanceOf(account).call();
```

## 步骤 4:读取我们锁定的 ETH 余额

cToken 合约还提供了一个名为`balanceOfUnderlying`的函数，该函数使用其拥有的 CTO ken 和当前汇率返回账户的基础资产价值:

```
let u = await cEthInstance.methods.balanceOfUnderlying(account).call();
```

## 步骤 5:从协议中赎回我们的 ETH

使用步骤 4 中的值`u`作为图 2 中的`redeemAmount`，我们可以调用`redeemUnderlying`来赎回我们的以太:

图 2:赎回基础资产

或者，我们可以使用`redeem`代替`redeemUnderlying`，并将步骤 3 中的值`b`作为`redeemAmount`传入。

# 结论

随着 DeFi 变得越来越容易使用并被如此多的人采用，学习如何与这些协议交互是任何开发人员工具箱中的一个有价值的工具。

如果你对区块链开发感兴趣，我会写一些教程、演练、提示以及如何开始和建立投资组合的技巧。查看以下资源:

[](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248) [## 区块链开发资源马上跟进

### 学习区块链、以太坊和 DApp 开发的资源列表

medium.com](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248)