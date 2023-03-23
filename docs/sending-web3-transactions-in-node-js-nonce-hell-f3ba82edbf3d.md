# 在 Node.js 中发送 Web3 事务— Nonce Hell。

> 原文：<https://betterprogramming.pub/sending-web3-transactions-in-node-js-nonce-hell-f3ba82edbf3d>

## 随机数是 EVM 账户的重要组成部分。知道它是如何管理的

![](img/03098b74faa02ecb09b6d48c3cc8c476.png)

可怕的数字。贷项:Unsplah

在 Node 中发送 web3 事务时，您是否遇到过以下错误？

```
replacement fee too low [ See: [https://links.ethers.org/v5-errors-REPLACEMENT_UNDERPRICED](https://links.ethers.org/v5-errors-REPLACEMENT_UNDERPRICED) ]
```

或者

```
the tx doesn’t have the correct nonce. account has nonce of: 95 tx has nonce of: 94
```

这意味着您的事务包含一个已经在您以前的事务中使用过的 nonce。

# 什么是账户现时？

随机数是你在以太坊或任何其他使用以太坊虚拟机(EVM)的区块链(如币安智能链和雪崩)上发送的每笔交易的附加号码。

对于您的第一笔交易，它从零开始，对于后续交易，它递增 1(+1)。这允许来自一个地址的每个交易具有唯一的现时。由于它对于每笔交易都是唯一的，因此有助于防止双重花费攻击。

双重花费攻击是指一个地址试图花费其当前余额两次。像从当地杂货店的女售货员那里拿回你的零钱，然后打电话给店主要同样的钱这样简单的事情就是双重消费攻击。来自一个地址的两个事务不能有相同的随机数，其中一个将被区块链网络拒绝。所以这个错误来自区块链拒绝的交易。

**注意:**在以太坊和比特币等工作证明(POW)链中，也有用于挖掘块的块随机数。它与这里提到的 nonce 是完全不同和分离的。

# 这个错误的原因是什么？

当您使用 web3 库在 Node.js 中发送事务时，如果您没有指定 nonce，它会自动为您检测它。它通过从区块链查询您的 nonce 并将其附加到您的事务中来实现这一点。从这里开始变得疯狂，和我在一起。

假设您想要同时发送两个事务，并且您没有指定它们的 nonce，那么库会自动检测 nonce 并将其附加到第一个事务。当它到达第二个事务时，它会自动再次检测并附加它。但是有一个问题，在你继续阅读之前，你能试着猜测问题在哪里吗？

发送的第一个事务获得最新的 nonce，但是当发送第二个事务时，它获得与第一个相同的 nonce，这使得它无效。它获得与第一个相同的随机数，因为当库试图再次获得随机数时，它从区块链接收相同的随机数。发生这种情况是因为第一个事务还没有被挖掘。如果它被挖掘，它将更新 nonce，并且库将接收正确的值。

# 为什么这不会发生在前端？

前端代码利用钱包发送交易。元掩码和 Trustwallet 等钱包能够跟踪当前值，因此您永远不会看到这些错误。在发送交易之前，可以在这些钱包上更改随机数。这对于[取消交易](https://nonseodion.medium.com/7-easy-steps-to-resolve-cancel-pending-ethereum-transactions-88d7d8bce053)很有用。

# 我们如何修复它？

这真的很简单，您只需要在发送交易之前知道正确的时间。您可以决定实现一个随机数跟踪器来跟踪您发送的事务，或者只需在发送下一个事务之前等待事务被挖掘。让我们看看随机数跟踪器如何与一个小节点应用程序一起工作。

下面的代码是用 Typescript 和 Ethers.js 编写的，运行在 Rinkeby Testnet 上。它在 [Github](https://github.com/nonseodion/ArticleCode/tree/main/Sending%20Web3%20Transactions%20In%20Nodejs%20Nonce%20Hell) 上托管。这是第一段代码。

config.ts

我们使用 Rinkeby 提供程序和来自环境变量的私钥来配置 Ethers.js 库。让我们看看处理发送事务和跟踪当前值的`sendTx`函数。

发送. ts

这段代码接收配置的 web3，并使用它发送事务。每个事务都应该发送事务参数。我们不使用 Ethers.js 著名的 `transaction.wait`是因为我们没有给用户反馈，这与前端代码不同。这允许我们发送多个事务，这些事务都可以在同一个块中被挖掘。该代码最重要的两个部分是随机数和锁。让我们看看他们。

在发送交易之前，我们会附上我们认为最新的随机数。随机数首先从区块链中获取，并在每次交易后递增。我们还确保在发送交易前检查最新的随机数，以防有其他使用该地址发送但不是来自我们的交易，即钱包可能被用在不同的地方。这笔国外交易也会影响现在，所以我们必须意识到。我们在函数之外定义现时，所以即使在每次`sendTx`调用之后，它的值仍然存在。`sendTx`函数使用[JavaScript 闭包属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)访问它。

从代码中您会注意到使用了锁，锁有助于暂停每个`sendTx`调用，直到它之前的调用完成。这有助于确保所有的`sendTx`调用，无论它们来自我们的小节点应用程序的哪个地方，都是按顺序执行的。锁的工作原理是将所有的调用放在一个数组中，然后一个接一个地执行它们。`lock.acquire`将调用放入数组并暂停，而`lock.release`让数组中的下一个调用运行。如果没有锁，对`sendTx`的调用将并行运行，并使用相同的 nonce 值。

以下是我们的简单交易:

短信服务

这两个事务只是发送 ETH。

我们这样运行它们。

index.ts

`tx1`和`tx2`被有意地在没有`await`的情况下调用，以显示我们的代码将如何处理来自我们应用程序中独立位置的`sendTx`调用。代码通过调用`sendTx`几乎同时发送 10 个事务。由于我们的锁，事务都被排队并按顺序执行。它们甚至可以在同一个区块开采。

当我们运行代码时，它将所有事务排队，获取 nonce，并按顺序执行它们。这样，我们能够确保每个事务都获得正确的 nonce。队列和随机数跟踪的发生是因为我们的锁和我们跟踪随机数的方式，如果没有它们，我们会得到上面的错误。

如您所知，如果您运行上面的代码，您将有效地部署 10 个合同。这是因为契约部署事务没有指定接收者，在我们的代码中也没有。我们用零代码部署合同，并向他们发送 0.01/0.001 以太网。这是我在 Etherscan 上的一个[交易](https://rinkeby.etherscan.io/tx/0xf1deb5583b7dadec4f9b0acda13b28473023f6f9cc676816c77b06bcac58d6e1)。您可以访问 [Github](https://github.com/nonseodion/ArticleCode/tree/main/Sending%20Web3%20Transactions%20In%20Nodejs%20Nonce%20Hell) 上的代码，并在您的终端上用一个简单的命令运行它。那里有一个简短的自述文件，告诉你如何设置。

这就是我们在后端处理 nonce 错误的方式。随机数是 EVM 账户中非常重要的一部分，我们已经看到了它是如何管理的。下一次，当您赶在截止日期之前遇到这个错误时，您可以自由地复制这段代码并实现它。devs 是干什么的？我将在我的下一篇文章中看到你。我将重写 Fei 协议/Rari capital 黑客使用的合同。祝我好运。