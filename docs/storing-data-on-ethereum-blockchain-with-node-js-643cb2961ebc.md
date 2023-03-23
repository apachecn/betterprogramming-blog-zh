# 用 Node.js 在以太坊区块链上存储数据

> 原文：<https://betterprogramming.pub/storing-data-on-ethereum-blockchain-with-node-js-643cb2961ebc>

## 以有组织和面向模型的方式使用以太坊区块链作为持久存储，而无需编写自定义智能合约

![](img/e4afbe7d6f9e70ce88907a671ea9cd57.png)

由 [Kanchanara](https://unsplash.com/@kanchanara?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们正处于 2022 年初，每个人都在谈论互联网上的“下一件大事”:Web 3。但是大部分谈论 NFT、加密资产、替代币和区块链技术的人并不真正知道他们如何在实践中使用它们，他们也不知道它们的真正能力。

本文是一个演练，将帮助您理解区块链和智能合约的一些基本概念。它还将提供一个实际操作指南，实际使用以太坊区块链来存储结构化数据，就像您使用经典的 SQL 数据库或不太经典的非 SQL 数据库一样。

这样，您就可以利用区块链，并在下一个 web 应用程序中积极地使用它们。

首先，让我们记住区块链是什么:

> 一个区块链通俗地说就是一种 ***分布式数据库*** *，比其他数据库提供更多的透明性和更多的安全性*。

这意味着它们的主要作用是存储数据。

# 为什么专门用以太坊区块链？

以太坊区块链是令人惊奇的，因为它是第一个提供通过智能契约和它们背后的语言在其上运行代码的能力的区块链。

但是，即使对于有经验的开发人员来说，Solidity smart contracts 也可能是一个真正的地狱，因为它们有一些主要的技术缺陷(至少目前是这样)。

其中最重要的如下:

1.  Solidity 中智能合约的开发非常耗时，
2.  智能合同不容易测试
3.  Solidity 不支持许多不同的数据类型。
4.  Solidity 只支持开发人员可以传递给函数的有限数量的参数
5.  坚固性缺乏面向对象的概念。它更像是一种非常原始的编程语言，而不是一种允许更复杂的数据结构和功能的现代语言。

然而，以太坊智能合约对于各种应用仍然非常有用，从复杂的拍卖场景和分散的 web 应用到简单的数据存储。

# 热气球:拼图中缺失的部分

![](img/36d01b7c2e5c6041494b68ad5f77a4d0.png)

复杂、难以开发、测试和维护的智能合约与以太坊区块链在基于 JS 的网络应用中的实际使用之间的差距被 [EthAir Balloons](https://github.com/petrosDemetrakopoulos/ethairballoons) 所掩盖。

EthAir Balloons 是以太坊区块链的一个严格类型的 ORM(对象关系映射器)库。它允许开发人员以一种有组织和面向模型的方式使用以太坊区块链作为持久存储，而无需编写定制的复杂智能合约。我们可以说它对于基于以太坊的区块链就像 Mongoose 对于 MongoDB 一样。

当然，EthAir 气球并不限制开发者只能使用以太坊区块链*。该库可以用于任何基于以太坊的区块链(可以是私有的，也可以是公共的)。*

实际上，如果你想在以太坊区块链上试验`EthAirBalloons`和数据存储，我强烈建议在以太坊区块链的私有实例中进行任何测试和试验(你可以使用`[ganache-cli](https://www.npmjs.com/package/ganache-cli)`工具在本地运行一个)。这是因为，公共以太坊区块链将对每次写、更新和删除操作收取大量的交易费(称为“gas”)。

但是理论部分已经说得够多了:让我们来看看实际情况。

# 在区块链上存储数据

本文的这一部分假设您熟悉 JavaScript 和 Node.js 的基本概念。

首先，让我们通过在终端上键入以下命令来创建一个新的节点项目:

```
mkdir ethereum_data_storage
cd ethereum_data_storage
npm init
```

完成向导，它将询问关于项目的更多信息(名称、版本、许可证、作者等)。

然后通过在项目的根目录下执行下面几行来安装`EthairBalloons`、Express 库和`body-parser`。为了将数据的 CRUD 操作公开为 REST API 调用，我们将使用 express。

```
npm i --save ethairballoons
npm i --save express
npm i --save body-parser
```

安装完库之后，我们需要创建一个名为`index.js` 的文件和一个名为contracts **的目录。**这是存储自动生成的可靠性合同的地方。

```
touch index.js
mkdir contracts
```

在`index.js`文件中，我们应该添加以下几行，以便导入项目的各种依赖项，并声明我们希望存储在区块链上的数据模型。

依赖关系和模型声明

在前 8 行中，我们导入并声明 API 的依赖项。

在第 10 行，我们仅使用两个参数启动了一个`ethairballoons`(或者我喜欢称之为`ethAirBalloonsProvider`)的实例:

1.我们想要使用的以太坊区块链提供商的 URL(在这个例子中它被设置为一个本地的`ganache-cli`提供商)，

2.我们希望保存自动生成的模型智能合约的路径。

在提供者初始化之后，我们可以使用`createSchema()`函数创建新的数据模式，并以 JS 对象格式传递模式细节(第 12–29 行)。当然，开发人员可以(按照建议)将模式定义保存在单独的 JSON 文件中，然后使用文件顶部的`require()`语句导入它们。

既然我们的数据模式已经设置好了，是时候在区块链中部署它了，在这一点上，我想记住我们是在本地`ganache-cli`实例(这是一个以太坊区块链模拟器)中完成的，而不是在实际的以太坊网络中。

由于交易费用可能很高，强烈建议只在区块链私人以太坊部署`EthAirBalloons`模型或在本地使用`ganache-cli`。

为了实现这一点，我们在 API 上声明了一个新的端点，名为`GET /deploy`

因此，当这个端点被调用时，一个新的智能契约将基于我们之前声明的模式自动生成和部署，而不需要编写任何代码。

此函数(`Car.deploy`)返回一个布尔值，表明部署是否成功；如果部署成功，则返回一个错误对象，该对象将不被定义。部署完成后，我们可以调用模型的其他功能。

## CRUD 操作

如前所述，`EthairBalloons`完全支持所有 CRUD(创建、更新、删除)操作，甚至更多。

除了创建、更新和删除我们声明的模型的一个实例之外，`EthairBalloons`还提供了通过 Id 查找一个实例和查找模型的所有当前存储的实例的能力。

公开 CRUD 操作的端点如下面的代码所示。

# 运行本地以太网模拟器

在本地运行服务器之前，使用众所周知的`node .`命令，我们应该启动`ganache-cli`(假设它已经安装，如果没有，您可以很容易地找到如何安装它)。

当运行`ganache-cli`时，我们必须确保它是用足够的资金初始化的，以支持我们想要通过 CRUD API 和`EthairBalloons`完成的所有事务。因此，我们将参数`-l` ( `gasLimit`)设置为 80000000000000，这对于一些创建和更新操作来说足够了。最终的命令应该如下所示:

```
ganache-cli -l 800000000000000
```

现在，您已经准备好调用 API 的端点来生成智能合约并将其部署到以太坊区块链，然后创建、更新、删除和查找您的模型的任何实例。

# 就这样

我希望你会觉得它很有趣，并希望它在未来的项目中有用！你可以在这个 [GitHub 库](https://github.com/petrosDemetrakopoulos/ethairballoons-CRUD-API)上找到完整的项目。不要犹豫问与[热气球](https://github.com/petrosDemetrakopoulos/ethairballoons)相关的问题。