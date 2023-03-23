# 以太坊 DApps 的前 5 个 Web3 JavaScript 函数

> 原文：<https://betterprogramming.pub/the-top-5-web3-javascript-functions-for-ethereum-dapps-7bc108bfd37>

## getAccounts、sendTransaction、estimateGas 等等

![](img/c8dffd379ba49f05fa61808397d66fae.png)

Paul Esch-Laurent 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

Web3 在传统互联网和以太坊区块链之间架起了一座桥梁。它使用户能够通过浏览器与您的 DApp 进行交互。当在前端使用 JavaScript 时，了解 [Web3JS](https://web3js.readthedocs.io/en/v1.2.6/) 的来龙去脉是必不可少的。

下面是 Web3JS 中一些最有用和最常用的函数的列表。

# web3.eth.getAccounts()

使用此功能获取所有可用的帐户地址。

用法:

```
let accounts = await web3.eth.getAccounts();
console.log(accounts[0]);
```

或者:

```
web3.eth.getAccounts().then(console.log);
```

# web3.eth.sendTransaction()

使用此功能将以太网从一个帐户发送到另一个帐户或智能合同地址。根据交易的不同，它需要一些参数。可能的参数包括`from`、`to`、`value`、`gas`、[更多](https://web3js.readthedocs.io/en/v1.2.6/web3-eth.html#sendtransaction)。

如果您将此方法用作前端的一部分，那么您可以捕获提交到区块链期间发生的某些事件。这些事件是`transactionHash`、`receipt`、`confirmation`和`error`。

用法:

# web3.eth.estimateGas()

如果你将一个交易发送到一个合同，你可能需要估计气体。传递与`web3.eth.sendTransaction()`相同的参数，以接收气体估计值。然后，您可以将结果添加到您的`web3.eth.sendTransaction()`调用的参数中。

用法:

# new web3.eth.Contract()

使用此功能将您已部署的合同加载到您的 DApp 中，以便您可以开始与其进行交互。参数包括合同部署到的`ABI`和`address`。

用法:

```
let contractInstance = new web3.eth.Contract(
    MyContract.abi,
    deployedAddress
);
await contractInstance.doSomething();
```

# web3.utils.toWei()

使用此函数将以太值转换为智能合约使用的价值单位 Wei。

用法:

```
let weiValue = web3.utils.toWei("1", "ether");
...
```

# 进一步阅读

如果你对区块链开发感兴趣，我会写一些教程、演练、提示以及如何开始和建立投资组合的技巧。

查看以下资源:

[](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248) [## 区块链开发资源马上跟进

### 学习区块链、以太坊和 DApp 开发的资源列表

medium.com](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248)