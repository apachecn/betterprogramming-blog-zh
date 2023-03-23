# 区块链开发教程:从以太坊 Mainnet 获取最新区块号

> 原文：<https://betterprogramming.pub/blockchain-development-tutorial-get-the-latest-block-number-from-ethereum-mainnet-bcc3ac2b605b>

## 用炼金术写一个简单的 Web3 脚本

![](img/f34f9aa0f2e55f5a22ede9a2c197bf5b.png)

照片由 [Esther Jiao](https://unsplash.com/@estherrj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

不知道从哪里开始？这个简短的教程将带你编写一个简单的 Web3 脚本，使用 Alchemy 从以太坊 mainnet 获取最新的块号。我们假设你已经通过了[入门](https://docs.alchemy.com/alchemy/introduction/getting-started)步骤，并且拥有了[炼金术账户](https://alchemy.com/?a=035d6ae2d0)。

## ​1.从您的命令行，创建一个新的项目目录和 cd 到它

```
mkdir web3-example
cd web3-example
```

## 2.如果您还没有安装 Alchemy Web3 依赖项，请安装它

你可以使用任何你选择的 web3 库，然而，使用 [Alchemy Web3](https://docs.alchemy.com/alchemy/documentation/alchemy-web3) 有很多好处:

```
npm install @alch/alchemy-web3
```

## 3.创建一个名为 index.js 的文件来添加内容

您最终需要用 Alchemy HTTP API 密钥替换 demo 密钥，并添加以下代码:

```
async function main() {const { createAlchemyWeb3 } = require(“@alch/alchemy-web3”);const web3 = createAlchemyWeb3(“https://eth-mainnet.alchemyapi.io/v2/demo");const blockNumber = await web3.eth.getBlockNumber();console.log(“The latest block number is “ + blockNumber);}main();
```

不熟悉异步的东西？看看这篇[文章](https://medium.com/better-programming/understanding-async-await-in-javascript-1d81bb079b2c)。

## 4.使用节点运行它

```
node index.js
```

## 5.您现在应该会在控制台中看到最新的块号输出！

```
The latest block number (at time of writing) is 11043912
```

呜！恭喜你。您刚刚使用[炼金术](https://alchemy.com/?a=035d6ae2d0)编写了您的第一个 web3 脚本。