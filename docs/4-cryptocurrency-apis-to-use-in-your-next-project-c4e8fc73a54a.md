# 在您的下一个项目中使用的 4 个加密货币 API

> 原文：<https://betterprogramming.pub/4-cryptocurrency-apis-to-use-in-your-next-project-c4e8fc73a54a>

## 使用这些超酷的 API 增强您的应用

![](img/85897df026bd344e7f9a2d27a443ae4b.png)

由[道格拉斯·洛佩斯](https://unsplash.com/@douglasamarelo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

由于比特币和替代币的迅速崛起，加密货币已经成为今年的热门话题。

但加密货币的使用案例远远不止移动可编程货币。智能合约和 Solidity 编程语言已经见证了寻求在以太坊网络上构建去中心化应用的开发人员的采用增加。

现在，Solidity 仍然是一门相对较新的语言，你可能没有机会去探索它。无论如何，有如此多的加密货币 API 可以激发你构建酷的东西。

在接下来的几节中，我们将探索一些 API 来帮助您创建令人惊叹的 web 和移动应用程序，以及您自己的数据集和交易机器人。

# 密码比较

加密货币是成对交易的。你可能习惯于 BTC/美元，但是也有许多其他的交易对。[交易对](https://cryptocurrencyfacts.com/what-are-trading-pairs-in-cryptocurrency/)是指可以在交易所交易的两种资产。例如，你可以把你 ETH 换成 BTC，反之亦然。

CryptoCompare 是从交易所获取任何加密货币当前价格的领先 API 之一。此外，你可以检索历史数据，赌注利息，并按其数量和市值排序硬币。

这里有一个获取不同法定货币比特币价格的端点:

```
[https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD,INR,EUR](https://min-api.cryptocompare.com/data/price?fsym=BTC&tsyms=USD,INR,EUR)Output:
{"USD":38944.45,"INR":3136015.25,"EUR":32960.32}
```

# 共同市场资本

CoinMarketCap 是一个追踪加密价格的领先网站。更好的是，它让您可以访问不同加密项目的最新信息。从开发人员的角度来看，CoinMarketCap API 文档是一座金矿。

首先，`[/v1/cryptocurrency/info](https://coinmarketcap.com/api/documentation/v1/#operation/getV1CryptocurrencyInfo)`返回元数据，如官方网站、技术文档、社交媒体账户和加密货币的开源代码。

这里是另一个 API: `[/v1/blockchain/statistics/latest](https://coinmarketcap.com/api/documentation/v1/#tag/blockchain)`,它让我们对区块链数据进行投票

# 现在付款

加密货币拥有非常低廉的交易费用(尽管 BTC 和 ETH 等硬币的网络费用很高，所以 ADA 等替代硬币目前可能是更好的选择)，这使得自由职业者获得支付成为一项有利可图的交易。想知道如何在您的网站或应用程序上接受比特币或加密货币支付吗？

[NowPayments.io](https://nowpayments.io/) 为设置加密支付网关提供现成的集成。你可以从在沙盒环境中测试他们的[支付 API](https://documenter.getpostman.com/view/7907941/S1a32n38?version=latest#9998079f-dcc8-4e07-9ac7-3d52f0fd733a) 开始。例如，下面是一个 API 响应，获取法定货币特定值的 BTC 的估计价格:

```
{
  "currency_from": "usd",
  "amount_from": 3999.5,
  "currency_to": "btc",
  "estimated_amount": 0.17061637
}
```

这些 API 还提供了获取以前付款、交易状态列表和创建发票的功能。

想要更多吗？NOWPayments 还可以让你[创建一个快速捐赠链接](https://nowpayments.io/donation-tools/link)，在个人博客或社交网络上分享。

# 币安 API

币安是世界上最大的加密货币交易所。他们的应用程序非常容易进行交易。但是人们总是希望有一些个性化的功能来自动化琐碎的任务或构建交易机器人。

很高兴，币安提供了一个安全的 REST API 来从他们的服务器获取数据，并以你自己的方式定制它。

首先，有一个[测试 API](https://academy.binance.com/en/articles/binance-api-series-pt-1-spot-trading-with-postman) 可以用来模拟购买。我特别发现[新订单交易](https://binance-docs.github.io/apidocs/spot/en/#new-order-trade)对于程序化投资非常有用。例如，当比特币达到一定价格时，你可以设置一个卖出指令来记录利润。

如果你想在你的加密钱包上设置一个终止开关按钮，币安 API 也很方便。例如，您可能想要立即出售您的加密资产，或者取消所有未结订单，或者下达百分比购买订单。

要了解更多 API 细节，请查看他们的 [GitHub 库](https://github.com/binance/binance-spot-api-docs)。

# 结论

虽然这是这篇快速文章的结尾，但还有很多加密货币 API 值得您去看看。比如， [CryptoQuant](https://cryptoquant.com/overview/btc-exchange-flows) 可以让你跟踪交易所硬币的流入和流出——这是观察加密货币领域趋势和情绪的一个很好的指标。同时， [ETH 加油站 API](https://docs.ethgasstation.info/) 让我们访问以太坊网络上的当前油价数据。

这一次到此为止。感谢阅读。