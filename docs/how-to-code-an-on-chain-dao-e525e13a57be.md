# 如何编写链上刀的代码

> 原文：<https://betterprogramming.pub/how-to-code-an-on-chain-dao-e525e13a57be>

## 使用 hardhat、TypeScript、solidity 和 Openzeppelin 从头开始构建一个去中心化的治理模型。这个 DAO(分散自治组织)使用一个具有投票权的 ERC20 令牌来做出决策

![](img/509516c70cab28e241e5ecfb5e4ee70d.png)

如何构建/编写 DAO

## 介绍

我们将学习如何使用 hardhat、solidity、typescript 和 Openzeppelin 构建一个链上 DAO。对于那些不知道的人，DAO 是一个分散的自治组织，通常由区块链提供支持。你可以看我之前的[视频概述](https://www.youtube.com/watch?v=X_QKZzd68ro)关于刀工装景观或者看我之前的文章[如何造刀(高级)](/what-is-a-dao-what-is-the-architecture-of-a-dao-how-to-build-a-dao-high-level-d096a97162cc)。

对于这一个，我们直接进入代码。我们将使用 100%链上治理模型，使用 [ERC20](https://www.investopedia.com/tech/why-crypto-users-need-know-about-erc20-token-standard/) 令牌投票支持提议的变更。一旦离线投票的工具得到改进(使用 [Chainlink OCR](https://chain.link/) 模型和像 [IPFS](https://ipfs.io/) 这样的分散数据库)，我们可能会有一个教程来做这件事以节省汽油。

但是要重申，在阅读这里之前，一定要 100%地阅读我之前的关于空间景观的文章。另外，如果你想看 pythonic 版本的 brownie，你可以[在这里](https://www.youtube.com/watch?v=rD8AxZ_wBA4)看视频，[在这里](https://github.com/brownie-mix/dao-mix)看代码。

安全帽视频:

如何编写 DAO 代码

# 快速入门

获得一切的最快方法就是做以下事情:

```
git clone [https://github.com/PatrickAlphaC/dao-template](https://github.com/PatrickAlphaC/dao-template)
cd dao-template
yarn
yarn hardhat test
```

然后嘣！您将运行模拟提议投票、投票表决、投票排队、然后执行的测试！

下面是测试套件的概要:

1.  我们部署了一个 ERC20 令牌，我们将使用它来管理我们的 DAO。
2.  我们部署了一个时间锁契约，我们将使用它在执行提议之间提供一个缓冲。

> 注意:**时间锁是处理所有资金、所有权等的合同**

3.我们部署我们的总督合同

> 注意:**治理契约负责提议等，但是时间锁执行！**

4.我们部署了一个范例盒子契约，它将由我们的治理过程所拥有！(也就是我们的 timelock 合同)。

5.我们提议在我们的装箱合同中增加一项新的价值。

6.然后我们就该提案进行表决。

7.然后，我们将提案排队等待执行。

8.然后，我们执行它！

但是，让我们为您分解一下…

# 入门指南

建议您在继续此处的操作之前，先通读[硬件入门文档](https://hardhat.org/getting-started/)。

# 要求

*   [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) :如果你能运行`git --version`并看到类似`git version x.x.x`的响应，你就知道你做对了
*   [Nodejs](https://nodejs.org/en/) :如果你能运行`node --version`并得到类似`vx.x.x`的输出，你就知道你已经正确安装了 Nodejs
*   [Yarn](https://classic.yarnpkg.com/lang/en/docs/install/) 而不是`npm`:如果你能运行:`yarn --version`并得到类似`x.x.x`的输出，你就知道你安装了 Yarn。您可能需要用 npm 安装它

# 我们正在建造的东西

我们将构建一个 DAO，它使用 ERC20 令牌对我们的基本`Box.sol`契约进行投票，如下所示:

这一切工作方式的美妙之处在于，治理是模块化的，可以“粘在”任何契约上。这里的关键是我们的契约是“ownable”，也就是说只有所有者才能调用`store`函数。而这份合同的主人将是我们的刀！

# 建造它

首先，建立一个 TypeScript hardhat 项目:

```
mkdir dao-template
cd dao-template
yarn add hardhat
yarn hardhat
```

并选择 TypeScript 选项。这将在您的目录中创建一些文件夹和文件来使用。

在您的`contracts`文件夹中，创建一个名为`Box.sol`的文件。并添加您从上面看到的`Box`代码，这将是我们“执行”治理的契约。

在我们的`hardhat.config.ts`中，我们将希望将实体版本更新为`0.8.12`或任何高于`0.8.4`的版本。

我们需要添加 openzeppelin 契约，然后尝试使用以下代码进行编译:

```
yarn add @openzeppelin/contracts
yarn hardhat compile
```

而且应该编译成功！我们有一个盒子…现在怎么办？

# 治理令牌

我们的治理令牌有点特殊，在您的`contracts`文件夹中创建一个名为`GovernanceToken.sol`的新文件。它应该是这样的:

您会注意到这不是一个“普通的”ERC20 令牌，这是因为我们需要跟踪“快照”每当提议投票时，我们希望确保使用 X 个街区前的人们的余额，而不是提议提出时的余额。这将减少人们在他们认为自己想参与的投票即将到来时买卖投票代币，并确保投票数量保持一致。

一旦一个“检查点”或“快照”的人的代币余额计算投票期间，就是这样！你不能在提议投票后购买更多的代币来获得更多的选票！你必须已经持有了令牌。

我们可以确保编译时使用了:

```
yarn hardhat compile
```

# 总督合同

![](img/ebf5805890a39708d00f033f6b91426a.png)

不要和州长混淆

现在，让我们在我们的`contracts`文件夹中创建一个名为`governance_standard`的文件夹。在未来，我想添加一个`governance_offchain`文件夹，但直到链接位，这是我们得到的！

我们将创建一个名为`GovernorContract.sol`的契约，看起来像:

这是帮助我们州长投票的合同。下面是我们要看的主要函数:

`propose`:提出交易。propose 函数是模块化的，因为它允许您调用任何合同的任何交易。这些参数是:

*   `targets`:你想调用某个函数的地址列表。
*   `values`:一个 ETH(或第 1 层加密)列表，您希望将它与您的交易一起发送到相应的每个地址。
*   `calldatas`:编码函数列表和每个地址上要调用的每个函数的参数。
*   `description`:对你正在使用的方案的描述。

这个功能的美妙之处在于，它允许您在一次交易中跨多个地址做几乎任何事情。

`castVote`:我们如何投票。

`queue`:一旦投票通过，我们就将它排队等待执行。

`execute`:时间锁定结束后，我们执行方案。

你会注意到，一旦投票通过，它不会立即生效，这是故意的。如果人们不喜欢所做的改变，我们希望给他们时间“退出”协议。这种“时间锁定”或“超时”是由我们的“时间锁定”合同强制执行的…这是我们接下来要做的！

# 时间锁

在与我们的治理契约相同的文件夹中创建一个名为`TimeLock.sol`的新文件。这是将“拥有”盒子的合同。

> 注意:是的，你没看错。时间锁拥有一切。这是因为每当我们的治理传递一些东西时，我们希望确保在执行该功能之前等待最小的延迟。时间锁加强了这一点。调速器合同将是唯一可以要求时间锁合同“做”事情的合同。它只能询问投票是否通过！

我们的时间锁有几个参数:

1.  `minDelay`:投票通过和执行之间应该等待多长时间。
2.  `proposers`:谁可以向 TimeLock 契约提议事务(我们将设置它，因此只有治理契约可以)。
3.  `executors`:我们设置了这个功能，这样任何人都可以执行已经超时的功能。然而，这将是添加[链节保持器](https://docs.chain.link/docs/chainlink-keepers/introduction/)的完美时机，以确保执行是分散的！

这就是你所需要的可靠性！运行`yarn hardhat compile`编译一切！

# 脚本和测试

现在，我不想在这篇文章中多写一些代码，但这就是它的要点。你可以在我的 [dao-template github repo](https://github.com/PatrickAlphaC/dao-template) 中看到脚本和测试，其中有名为`vote`、`propose`和`queueAndExecute`的脚本，正如它们的名字所暗示的那样。

您可以看到代码，并确切了解如何实现一些高级的坚固性/安全帽概念，如:

1.  在 etherscan 上自动验证您的智能合同
2.  本地网络上的快进时间
3.  本地网络上的快进块
4.  如何将函数及其参数编码到字节
5.  旋转一个本地的 hardhat 节点，在一个命令行中包含你喜欢的所有合同
6.  高级气体报告
7.  类型链使用

还有更多！

# 了解更多信息

如果你想持续获得最新的智能合同/区块链/web3 开发者内容，请务必[关注 Medium](https://medium.com/@patrick.collins_58673/membership) 、 [Twitter](https://twitter.com/PatrickAlphaC) 和 [YouTube](https://www.youtube.com/channel/UCn-3f8tw_E1jZvhuHatROwA) 上的我，以了解最新信息。

# 灵感

*   [Openzeppelin 治理演练](https://docs.openzeppelin.com/contracts/4.x/governance)
*   [Openzeppelin Governance Github](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts/governance)
*   [道斯岛上的维塔利科](https://blog.ethereum.org/2014/05/06/daos-dacs-das-and-more-an-incomplete-terminology-guide/)
*   [维塔利克谈连锁经营](https://vitalik.ca/general/2021/08/16/voting3.html)
*   [Vitalik 谈一般治理](https://vitalik.ca/general/2017/12/17/voting.html)