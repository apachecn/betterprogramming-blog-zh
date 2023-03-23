# 使用多呼叫加速您的 DeFi 查询

> 原文：<https://betterprogramming.pub/speed-up-your-defi-queries-using-multicall-d4cf652d8ab6>

## **如何对呼叫进行分组以更快的方式从以太坊的 DeFi 协议中获取数据的实例**

![](img/9283490b8162c745f97684070630189a.png)

照片由 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*编者按:本文仅供教育娱乐之用，不构成理财建议。*

# **简介**

有时，从区块链获取数据在时间和发送给它的请求数量方面会很昂贵。如果我们希望同时获得大量数据，在仪表板中显示或进行分析，并且我们必须调用合同的不同功能或使用不同参数的相同功能，这可能会导致很长的查询时间。如果我们使用一个像 [Infura](https://infura.io/) 这样的提供者，我们可能会达到连接的极限。

## **什么是 Multicall？**

[Multicall](https://github.com/cavanmflynn/ethers-multicall#readme) 是一个 npm 包，它将几个 HTTP 调用组合成一个。这样，如果我们想要从 *n 个*不同的请求中获取数据，我们可以在发送之前将它们分组，并且只发送一个 HTTP 请求，从而改善响应时间和我们的`eth_call`调用的消耗。

## 测试它是如何工作的

为了了解这种机制是如何工作的，以及是否真的比传统方法有所改进——为每个函数调用 *n* 次——我们将做一个例子，演示我们如何在不使用和使用 Multicall 的情况下做到这一点，然后分析结果。为此，我们将通过调用函数`getAccountLiquidity`来查询复合协议。我们将使用 1000 个不同的地址来获取所有这些地址的信息。

# **创建项目**

## **安装依赖关系**

为了进行这个测试，我们将创建一个节点项目，我们将安装 [ethers.js](https://docs.ethers.io/v5/) 的依赖项来与区块链交互， [money-legos](https://money-legos.studydefi.com/#/) 以更简单的方式引用 ABIs 和契约，以及 Multicall 包。

为此，我们使用以下内容创建项目:

```
npm init -y
```

我们安装我们已经讨论过的依赖项:

```
npm install -S @studydefi/money-legos ethers ethers-multicall
```

## **导入依赖关系**

在这两种情况下，我们都必须使用公共依赖项，并实例化提供者以连接区块链。我们通过以下方式做到这一点:

我们还将创建一个函数来显示结果和执行时间，如下所示:

# **调用合同**

## **来自循环的调用**

为了用传统方法进行测试，我们将遍历地址数组，在一个`map`循环中，我们将逐个返回每个查询的结果，直到我们到达文件拥有的 1000 个地址。为此，我们将执行如下功能:

在这里，我们实例化复合审计员合同，并为每个地址调用帐户流动性函数。

## **进行多呼叫**

当使用 Multicall 调用时，这个函数需要做一点修改，留下如下形式:

我们在这里做的是利用 Multicall 包中的`Provider`和`Contract`类。首先我们初始化这个提供者，传递我们的提供者`web3`和带有地址及其 ABI 的契约。

一旦我们创建了它，我们做和以前一样的过程。通过一个`map`，我们调用账户流动性函数。但在这种情况下，我们不是将它们发送到网络，而是将它们分组到一个数组中。一旦创建了这个数组，就调用创建的提供者的`all`函数，这就调用了网络。

# **分析结果**

要看是否真的有了明显的改善，我们先来看时差。

正常方法(不使用多调用执行)

```
Time to process in milliseconds: 124653Time to process in seconds: 124.653Number of entries in the result: 1000
```

使用多呼叫

```
Time to process in milliseconds: 9591Time to process in seconds: 9.591Number of entries in the result: 1000
```

# **结论**

正如我们所看到的，时间的减少是相当可观的，从 124 秒到 9.5 秒，所以它的成本会少十分之一。

此外，如果我们比较一下`eth_call`呼叫的数量，我们还会看到一个非常显著的下降，从一千多次下降到只有一次。因此，如果我们依赖一个以太坊的访问提供者，其中对 API 的调用是有限的，这是需要考虑的事情。