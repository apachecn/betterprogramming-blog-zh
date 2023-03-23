# 多边形如何帮助以太坊缩放？

> 原文：<https://betterprogramming.pub/how-does-polygon-help-ethereum-scale-47b2400d44c>

## 深入了解多边形的优势

![](img/1ad8ed1260dd275e629b65b042ec1e30.png)

作者图片

# 介绍

自 2014 年成立以来，以太坊已经成为能够支持图灵完全智能合约的区块链平台事实上的领导者。

以太坊在带来一场被称为 web3 的革命中发挥了重要作用。最大的分散式交易所，NFT 市场和农业协议位于以太坊。该链负责处理价值数十亿美元的价值，它为创作者和投资者开辟了以前深不可测的途径。

尽管以太坊 dapps 在用户中广受欢迎，但它们还远未被广泛采用。使用 dapps 的总人数仍然只有几百万。其中一个最大的原因是链条固有的伸缩障碍。除了少数高净值用户，它很快就会变得不可用。

幸运的是，社区已经做出了认真的努力来帮助以太坊扩大规模。当今市场上最受欢迎的扩展解决方案是 Polygon POS(Proof of stage)侧链。本文将探讨什么是多边形，以及它如何克服以太坊的缺点。最后，我们将通过创建一个 NFT 铸币智能合同来并排比较两家连锁店的表现。

# 以太坊的问题是

尽管以太坊处于市场领先地位，但它仍面临一系列问题:

1.  低事务吞吐量:以太坊每秒只能处理 15 个事务。与 Visa 或 Mastercard 等每秒处理超过 25，000 笔交易的解决方案相比，这一数字微不足道。
2.  高汽油费:当一家连锁店可以处理几笔交易时，当交易量大时，它必须增加处理成本。以太坊因高昂的煤气费而臭名昭著。例如，不久前，在一个受欢迎的 NFT 造币厂(称为 Otherside)，它达到了近 7000 Gwei。在此期间，即使是最基本的交易，你也要花费超过 500 美元。
3.  缓慢的事务终结:以太坊大约每 15 秒向其链中添加一个块。由于多个采矿者同时竞争开采区块，一个区块不能被认为是最终的，直到至少 4-5 个区块被添加到它的顶部。从本质上讲，一个交易需要一分钟以上的时间才能被认为是最终的。对于游戏和金融工具这样的应用程序来说，如此缓慢的事务终结是完全不可接受的。

# 解决方案

为了实现大规模的采用和接受，必须在以太坊的基础上开发一个解决方案来解决它最突出的问题。本质上，我们需要一个具有以下特征的解决方案:

1.  低成本:即使是计算量最大的交易，成本也不应超过几美分。
2.  快速:交易几乎立即得到确认。
3.  EVM 兼容:开发人员不需要学习新的工具或语言来构建解决方案。
4.  与以太坊互通:轻松存取以太坊资金及解决方案。
5.  参与式社区:致力于随着时间的推移改进解决方案并在此基础上构建用户友好的 dapps 的社区。
6.  可持续发展:任何被大众接受的解决方案都必须具有环保意识。

以太坊核心开发人员正在致力于第 1 层扩展修复，如[分片](https://ethereum.org/en/upgrades/sharding/)，但这些解决方案还远未完成。我们需要任何人今天就能轻松开始的解决方案。

# 引入多边形

Polygon 的创建者设计了一个包含上述所有特性的链。自 2017 年问世以来，多边形链已经成为最受欢迎的以太坊缩放解决方案之一。

从技术上讲，Polygon 是一个以太坊侧链。侧链是一个独立的区块链，通过桥连接到以太坊。与 L2 不同，侧链不继承以太坊主链的安全特性。相反，它们实现自己的一致协议并阻止参数。因此，侧链不需要向以太坊发送状态变化和事务数据(而 L2 则需要)。

侧链与以太坊的更大独立性使得 Polygon 更加集中，并使其能够实现非常高的吞吐量。

以下是 Polygon 在以太坊上实现的改进:

1.  高事务处理速度:Polygon 每秒可以处理高达 65，000 个事务，与其集中式竞争对手的规模相当。
2.  低交易成本:即使是 Polygon 上最繁重的交易也只需要几分钱。在大多数情况下，与以太坊相比，成本可以降低 10，000 倍。
3.  即时交易终结:Polygon 的共识协议保证交易几乎即时(不到两秒钟)得到确认。
4.  EVM 兼容:多边形是建立在相同的软件以太坊。因此，以太坊开发者可以使用相同的工具在 Polygon 上构建。
5.  强大的社区:Polygon 团队已经花费了数百万美元来建立一个由建筑商、投资者和用户组成的强大社区。它已经是以太坊上一些最受欢迎的 dapps 的所在地，有更好的 UX 和更便宜的成本。
6.  低能耗:Polygon 使用利益验证协议，比它的 PoW 对手少消耗 99.9%的能量。事实上，Polygon 已经承诺到 2022 年底成为[碳负](https://polygon.technology/sustainability/)，使其成为当今市场上最大的环保区块链之一。

# 关于多边形 PoS 安全性的一个注记

与其他侧链不同，Polygon PoS 确实有一种方法可以继承以太坊的安全性。Polygon PoS 链利用[等离子桥](https://docs.polygon.technology/docs/develop/ethereum-polygon/plasma/getting-started/)来维持和保证高水平的安全性。Plasma 拥有独特的退出机制，一套智能合同来帮助移动资产和检查点进行验证，使 Polygon PoS 能够利用以太坊的安全性。

这里唯一的缺点是通过等离子桥从 Polygon 到 Ethereum 的撤退必须经过 7 天的撤退期。使用 [PoS 桥](https://docs.polygon.technology/docs/develop/ethereum-polygon/pos/getting-started/)，可以更快地取款(30 分钟到 3 小时内)。然而，这种方法不如使用等离子体桥安全。

这两种桥使用起来仍然安全可靠，在大多数情况下，应该可以很好地满足用户的需求。但是，如果您的项目需要最先进的安全性，谨慎的做法是考虑使用多边形 L2 解决方案，如 [Zero](https://polygon.technology/solutions/polygon-zero/) 、[夜幕](https://polygon.technology/solutions/polygon-nightfall/)和 [Hermez](https://polygon.technology/solutions/polygon-hermez/) 。

# 行动比较

既然我们已经看到了 Polygon PoS 在某些项目上相对于 Ethereum 的优势，让我们分析一下这两个链的部署。

在本文的以下部分中，我们将创建一个简单的 NFT 契约，并将其部署到 Polygon Mumbai 和 Ethereum Goerli 测试网。我们将观察两个链的开发过程是如何几乎相同的。

接下来，我们将分析之前(由其他团队)在以太坊和 Polygon 主网上部署的 NFT 合同，然后比较成本和确认时间。

# NFT 项目概述

我们将要创建的项目非常简单。我们想写和部署 ERC-721 合同，铸造一个单一的 NFT 到我们选择的区块链。

此类项目的第一步是创建 NFT 艺术及其相关元数据，并将其上传到 IPFS。对于本教程，我们已经为您做到了这一点。

你可以点击这里查看上传到 IPFS [的图片，点击这里](https://gateway.pinata.cloud/ipfs/QmeDadHM1U3jVw1pMpZ5ZENBJhFGafHQeUNTNoapw7j3mq)查看相关的 NFT 元数据[。](https://gateway.pinata.cloud/ipfs/QmUyZoK21qb8YknXGfDB34RTY8vMqPb6Bsj9U9iLEnyrZR)

![](img/c10f749a1272cf59ce61b172a30a2613.png)

作者图片

如果你有兴趣了解更多关于 NFT 元数据、IPFS 和 Pinata 的信息，请点击这里查看这篇文章[。](https://dev.to/rounakbanik/working-with-nft-metadata-ipfs-and-pinata-3ieh)

# 创建 NFT 合同

## 步骤 1:安装元掩码扩展

如果你还没有，为你的浏览器安装[元掩码扩展](https://metamask.io/download/)。安装后，MetaMask 将引导您创建第一个钱包。

## 第二步:将 Goerli 和 Mumbai 连锁店加入 MetaMask

默认情况下，MetaMask 带有以太坊 mainnet 和 Goerli testnet 配置。要添加多边形 mainnet 和 Mumbai testnet 链，请遵循本文档中[的说明。](https://docs.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/)

## 第三步:从水龙头中取出 GoerliETH 和 Mumbai MATIC

前往炼金术士的 [Goerli 水龙头页面](https://goerlifaucet.com/)请求一些免费 goer lies。你可能需要为此创建一个免费的炼金术账户。

如果一切顺利，当你转到歌利测试网时，你的钱包里应该有 0.05 歌利。

同样，从 Polygon 的官方水龙头获得一些免费的 Mumbai MATIC。如果成功，切换到孟买连锁店应该会告诉你，你有 0.2 MATIC 的余额。

## 步骤 4:安装 NPM 和节点

我们将使用 JavaScript 和 node 构建我们的项目。如果您的本地机器上没有安装 node 和 npm，您可以在这里[安装。](https://nodejs.org/en/download/)

您可以通过使用以下命令检查终端上节点的版本号来确保一切正常运行:

```
$ node -v
```

## 步骤 5:创建一个节点项目并安装依赖项

让我们通过运行以下命令来设置一个空的项目存储库:

```
$ mkdir nft-contract && cd nft-contract
$ npm init -y
```

我们将使用行业标准以太坊开发环境 Hardhat 来构建和部署我们的智能合约。通过运行以下命令安装 Hardhat:

```
$ npm install --save-dev hardhat
```

我们现在可以通过运行以下命令并选择“创建一个基本的示例项目”来创建一个示例 Hardhat 项目

```
$ npx hardhat
```

同意所有默认设置(项目根，添加一个`.gitignore`，并安装示例项目依赖项)。

要检查一切是否正常，请运行以下命令:

```
$ npx hardhat test
```

我们现在已经成功地配置了我们的 Hardhat 开发环境。接下来让我们安装 OpenZeppelin 合同包。这将使我们能够访问 ERC-721 实现(NFTs 的标准)，在此基础上我们将构建我们的合同。

```
$ npm install @openzeppelin/contracts
```

## 步骤 6:编写智能合同

在您喜欢的代码编辑器(例如 VS Code)中打开存储库。在`contracts`文件夹中，创建名为`NFTContract.sol`的新文件。

我们将创建一个非常简单的 ERC-721 契约，它在部署时(即在构造函数中)铸造一个 NFT。将以下代码添加到文件中:

## 步骤 7:在本地部署契约

现在，让我们编写一个脚本，在本地部署和测试契约。在脚本文件夹中，创建一个名为`run.js`的新文件，并添加以下代码:

现在，在您的终端上运行以下命令:

```
$ npx hardhat run scripts/run.js
```

## 步骤 8:将合同部署到 Goerli

要将合同部署到 Goerli，我们必须将网络添加到`hardhat.config.js`文件中。用以下内容替换文件末尾的`module.exports`对象:

```
module.exports = {
  solidity: "0.8.4",
  networks: {
    goerli: {
      url: "https://ethereum-goerli-rpc.allthatnode.com/",
      accounts: ["<-- WALLET PRIVATE KEY -->"]
    }
  }
};
```

用元掩码钱包的私钥替换占位符。(这可以在账户详情中找到。)注意不要公开共享此密钥或文件。

要部署到 Goerli testnet，运行以下命令:

```
npx hardhat run scripts/run.js --network goerli
```

如果一切顺利，您的终端将在 Goerli 网络上显示已部署合同的地址。前往[https://goer Li . ethers can . io](https://goerli.etherscan.io/)并搜索您的合同地址以查看您的合同。

你可以在这里找到我们部署[的合同。](https://goerli.etherscan.io/address/0x64ccE52898F5d61380D2Ec8C02F2EF16F28436de)

## 步骤 9:将合同部署到孟买

现在让我们将合同部署到 Polygon Mumbai 网络。这一步最好的部分是，我们必须完全不改变我们的合同或运行脚本。我们唯一需要添加的是`mumbai`网络到`hardhat.config.js`。

通过运行以下命令部署到孟买:

```
npx hardhat run scripts/run.js --network mumbai
```

您可以在[https://mumbai.polygonscan.com](https://mumbai.polygonscan.com/)搜索您的合同地址。你可以在这里找到我们的合同。

你也可以在 [OpenSea Testnets](https://testnets.opensea.io/) 上搜索你的 Goerli 和 Mumbai NFTs。例如，[这里的](https://testnets.opensea.io/assets/mumbai/0x785e970c281ecfcbbc83b1ca1b32fb30ba0e08b3/0)是我们在孟买网络上创造的 NFT。

# 比较成本和时间

到目前为止，应该非常清楚以太坊和多边形侧链的开发几乎是相同的。

但是成本和时间的影响是什么？

我们无法用我们部署的合同来回答这些问题，因为我们是在使用假币的测试网上操作的。

但不用担心。我们已经代表你将这些合同部署到以太坊和多边形主网。让我们来看看表演。

## 以太坊

你可以在以太坊[这里](https://etherscan.io/address/0x5acea83cd77703773429959e66cc2c2f648262a7)查看已部署的 NFT 合同，在这里查看合同创建交易[。](https://etherscan.io/tx/0x26ac19375af708c7d7b9266d9aae9198f49b41124f49af68dc1d28963f6363af)

尽管最近以太币价格(以美元计算)和平均天然气价格(以 gwei 计算)下降，但合同部署成本约为 0.02 以太币或 40 美元。

确认这个以太坊交易的时间大约是 40 秒。

## 多边形

您可以在多边形[的](https://polygonscan.com/address/0xceb61fdf7130d170c50c02e3a9bed4eb83b42a46)处查看已部署的 NFT 合同，在处查看合同创建事务[。](https://polygonscan.com/tx/0x95c2bbbb79ab07db7ea656b0506c88bd3f3e83e485e1d075f770581f312ffd06)

请注意，当 MATIC 交易价格为 0.94 美元时，我们支付了 0.06 MATIC。因此，我们支付了 0.06 美元来部署合同。

确认该交易的时间是 6 秒。

实际上，该合同的确认速度几乎提高了 7 倍，成本降低了 1000 倍！

# 结论

以太坊无疑是现存的最重要的智能合约区块链。然而，它面临着规模和交易成本方面的巨大障碍。

希望这篇文章有力地说明了为什么 Polygon 是解决以太坊可伸缩性问题的灵丹妙药。作为 PoS 侧链，它显著提高了处理速度并降低了成本。

对于以太坊开发者来说，在多边形上构建几乎不需要额外的技能提升。以太坊工具兼容这两种环境。要开始在多边形上构建，请在这里查看文档。