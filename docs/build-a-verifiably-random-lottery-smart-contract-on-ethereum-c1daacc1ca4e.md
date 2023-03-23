# 在以太坊上建立一个可验证的随机彩票智能合约

> 原文：<https://betterprogramming.pub/build-a-verifiably-random-lottery-smart-contract-on-ethereum-c1daacc1ca4e>

## 如何创建以太坊彩票

![](img/db8cd9e8b193492675ead5dd6cf1554e.png)

照片由[迪伦·诺尔特](https://unsplash.com/@dylan_nolte?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/lottery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

*编者按:本文仅供娱乐和教育之用，不构成或包含理财建议。你从这篇文章的内容中采取的任何行动完全是你自己的。*

真正的随机性在以太坊几乎是不可能的。这是因为交易需要经过网络上多个节点的验证才能确认。如果智能合约函数是真正随机的，那么使用该函数验证交易的每个节点都会得出不同的结果，这意味着交易永远不会被确认。

[以太坊生态系统中最大的玩家之一最近发布的公告](https://blog.chain.link/verifiable-random-functions-vrf-random-number-generation-rng-feature/)引起了人们对这个问题的兴奋。使用一个叫做*可验证随机函数(VRF)的系统，*以太坊智能合约现在可以生成随机数。

这意味着那些看起来与智能合约完美契合但却无法实现的概念，因为它们需要随机数，现在可以实现了。

一个这样的概念是彩票。

# 构建彩票智能合约

我们的彩票将有三个阶段。第一个是 *open，*任何人支付少量费用就可以提交新号码。第二个是*关闭，*不能提交新的数字，随机数正在生成。第三个是*完成，*其中号码已经生成，获胜者已经得到报酬。

如果没有人中奖，彩票合同可以滚动，增加头奖。

## 定义阶段

阶段应该限制操作，以便只能执行允许的操作。例如，应该允许新提交的唯一阶段是开放阶段。如果抽奖结束或结束，合同应该禁止新的提交。

使用`enum`，我们可以定义任意多的阶段。姑且称之为`LotteryState`。在我们的状态变量中，我们定义了以下内容:

```
enum LotteryState { Open, Closed, Finished }
LotteryState public state;
```

既然已经定义了枚举，我们可以在函数中设置规则(`require`语句)，确保契约的当前`state`是我们所期望的。

鉴于这些`require`语句在整个合同中可能看起来相似，让我们将其最小化。我们可以定义一个修饰符来执行`require`语句，并且我们可以把它赋给任何我们想要的函数。

```
modifier isState(LotteryState _state) {
    require(state == _state, "Wrong state for this action");
    _;
}
```

现在，当我们定义函数时，我们可以添加这个修饰符来确保彩票的当前状态是我们所期望的。

## 提交数字

任何人都应该被允许提交一个数字，只要支付最低报名费。但是，每个参赛者不能提交同一个号码超过一次。应该允许新提交的唯一状态是开放状态。

下面是我们的`submitNumber`函数:

图 1:提交数字

第 1 行定义了名称、单个`_number`参数以及它是`public`和`payable`的事实。它还添加了`isState`修饰符来确保彩票是公开的。

第 2 行确保已经支付了正确的报名费，第 3 行确保消息的发送者还没有提交该数字，并将其添加到流程中的条目中。

变量`entries`指的是定义猜测数字和输入该数字的一组地址的映射。它是这样定义的:

```
mapping(uint => EnumerableSet.AddressSet) entries;
```

`AddressSet`是指 [OpenZeppelin](https://docs.openzeppelin.com/contracts/2.x/api/utils#EnumerableSet) `[EnumerableSet](https://docs.openzeppelin.com/contracts/2.x/api/utils#EnumerableSet)` [契约，为原语类型](https://docs.openzeppelin.com/contracts/2.x/api/utils#EnumerableSet)提供了附加功能。

一旦检查完成，接下来的四行将数字添加到猜测中，支付所有者提成的一小部分，并发出一个`NewEntry`事件。

## 抽号码

如果你读过这篇关于如何使用 VRF 的文章，那么你会知道生成一个随机数并不像调用一个函数那么简单(就像 JavaScript 中的`Math.random()`)。

要生成一个随机数，您必须从 VRF 协调器请求随机性，并实现一个 VRF 可以用响应回调的函数。为此，我们需要定义一个 VRF 消费者(创建 VRF 消费者的细节可以在这里找到)，在图 2 中我们称之为`RandomNumberGenerator`。

图 2:“RandomNumberGenerator”扩展“VRFConsumer”

我们的彩票将采取这个合同的地址作为一个注入参数时，建设。当抽取数字时，它会调用`request`函数。这要求 VRF 提供随机性，反过来，它在第 18 行提供对`filfullRandomness`的响应。您可以在图 2 调用中看到，这是对我们与`numberDrawn`的`Lottery`合同的回调。让我们来定义这些函数:

图 3:图纸编号

`drawNumber`只能由我们定义的第 1 行中彩票的所有者调用，并且只能在彩票处于开奖状态时调用。

第 7 行的`numberDrawn`是一旦随机数被 VRF 接收到后`fulfillRandomness`调用的函数。它确保`request-id`是从请求中返回的 ID，发出一个事件，支付赢家，并将彩票的状态更改为`Finished`。

# 完整的代码

图 4:彩票合同

这是一个原始的实现，但它展示了区块链上可验证随机性的出现是如何降低像彩票这样的合同的复杂性的。以前的赌博契约需要使用哈希机制、基于时间的机制、基于块的机制等，所有这些机制都是易受攻击的。

# 进一步阅读

如果你对区块链开发感兴趣，我会写一些关于如何开始和建立投资组合的教程、演练、提示和技巧。