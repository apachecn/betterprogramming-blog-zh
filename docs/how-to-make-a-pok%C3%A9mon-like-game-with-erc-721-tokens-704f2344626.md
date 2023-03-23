# 如何用 ERC 721 代币制作类似神奇宝贝的游戏

> 原文：<https://betterprogramming.pub/how-to-make-a-pok%C3%A9mon-like-game-with-erc-721-tokens-704f2344626>

## ERC-721 代币:它们是什么？它们是如何工作的？我们如何使用它们？

![](img/84c81f6a00a37e2451e3a9221c5c4e16.png)

[杰伦](https://unsplash.com/@filmape?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/pokemon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# ERC 队以 20 比 721 战胜 ERC 队

在 2017 年 ICO 泡沫最严重的时候，ERC-20 代币随处可见。它们被科技公司用作一种众筹形式，一些公司声称未来可以在他们的平台上使用这些代币。

**ERC-20 代币就像货币**。每一美元的价值都是一样的。把一张一美元的钞票换成另一张一美元的钞票，你实际上得到的是同样的东西。这就是所谓的**可替换令牌**。

[**ERC-721**](http://erc721.org/) **代币如同收藏品**。每张口袋妖怪卡都和其他口袋妖怪卡不一样。即使是代表同一个口袋妖怪的卡片，价值也不一样。有些卡状况比其他卡好，有些是限量版或特别版等。一张皮卡丘卡并不总是等同于另一张皮卡丘卡。这就是所谓的**不可替换令牌**。

# 但是怎么做呢？

[ERC-721 标准](https://eips.ethereum.org/EIPS/eip-721)描述了任何不可替代令牌必须遵守的接口，以被视为 ERC-721。

幸运的是，我们不需要每次创建 ERC-721 时都创建新的代码来满足这个标准。社区维护的图书馆的存在为我们解决了这个问题。OpenZeppelin 提供了一个[伟大的库](https://github.com/OpenZeppelin/openzeppelin-contracts)，它填补了大部分空白。

让我们看看如何使用 OpenZeppelin 创建一个非常有限的数字口袋妖怪游戏的仿制品。我们将把我们的游戏叫做以太门。

# ERC-721 以太怪游戏

首先，让我们概述一下关于每个 Ethermon 的一些假设:

*   每辆车都归某人所有，
*   他们从第一层开始，
*   他们和其他口袋妖怪战斗，
*   通过战斗获得等级。

我们还需要围绕战斗的一些逻辑。为简单起见，我们假设如果一个以太怪攻击另一个以太怪，胜者将由哪个以太怪等级更高来决定。如果等级相同，攻击者获胜。战斗的胜利者上升两级，失败者上升一级。

## 项目创建

请注意，在本演练中，我们将使用[松露套件](https://www.trufflesuite.com/)。

首先创建一个新文件夹并初始化 Truffle 项目。

```
mkdir ethermon
cd ethermon/
truffle init
```

## 使用 OpenZeppelin

要使用 OpenZeppelin，我们需要通过 npm 导入库。让我们初始化 npm，然后获取 OpenZeppelin 的正确版本。我们使用的是最新的稳定版本，在撰写本文时是版本`2.5.0`。确保你有 Solidity 编译器版本`0.5.5`。

```
npm init
npm install @openzeppelin/contracts@2.5.0 --save
```

## 扩展 ERC-721

在我们的`contracts/`文件夹中，让我们创建一个名为`Ethermon.sol`的新文件。为了在 OpenZeppelin 代码中包含所有为我们编写的功能，我们需要导入并扩展`[ERC721.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58a3368215581509d05bd3ec4d53cd381c9bb40e/contracts/token/ERC721/ERC721.sol)`。

图 1 向我们展示了此时`Ethermon.sol`应该是什么样子:

图 1: Ethermon.sol

我们通过运行`truffle compile`来确保我们的合同正确编译。接下来，让我们编写迁移脚本，以便将其部署到本地区块链。在`migrations/`中创建一个名为 `2_deploy_contracts.js`的新迁移文件。粘贴图 2 的内容。

图 2: 2_deploy_contracts.js

确保您的`truffle-config.js`针对您的本地区块链设置正确。您可以通过运行`truffle test`来测试这一点。

## 写 Ethermon 逻辑

我们需要 Ethermon 合同能够:

1.  创造新的怪物。
2.  将怪物分配给它们的主人。
3.  让主人与他们的怪物对抗其他怪物。

让我们从第一步开始。我们需要我们的`Ethermon`契约来存储所有现存怪物的数组。这个庞大的数据需要存储一些信息，比如名称和级别，所以我们将使用一个 struct。

图 3 显示了我们的合同在这个阶段应该是什么样子。

图 3:创建新的怪物

第 7 行定义了我们的`Monster`结构，第 12 行定义了我们的数组。我们还添加了一个`gameOwner`变量来存储部署了`Ethermon`契约的地址。第 19 行显示了我们的`createNewMonster()`函数的定义。

首先，它检查这个函数是由游戏的所有者调用的。然后，它使用现有的怪物数量生成一个 ID，将一个新的怪物推送到我们的数组中，最后使用`_safeMint()`将怪物 ID 分配给它的所有者。这包括步骤 1 和 2。

`_safeMint()`是 ERC-721 合同提供给我们的一个功能，我们是从这个功能延伸而来的。它通过检查 ID 是否已经存在来安全地将 ID 分配给一个地址。

现在，我们可以创造新的怪物并把它们分配给主人。进入第三步:战斗逻辑。

## 战斗逻辑

如前所述，我们的战斗逻辑决定了一个怪物升多少级。更高级别的怪物赢了就升两级，输了就升一级。如果他们是同一级别，攻击者获胜。图 4 显示了当我们为战斗添加代码时，我们的契约看起来是什么样子。

图 4:添加了战斗功能的 Ethermon.sol

第 19 行现在显示了战斗函数逻辑。目前，任何人都可以打电话给`battle()`。然而，我们需要限制这个功能，这样只有攻击怪物的主人才能发起战斗。为此，我们可以添加一个修改器，利用`ERC721.sol`中的`ownerOf()`功能。图 5 向我们展示了它的样子。

我们做到了！一个非常基本但工作正常的怪物战斗游戏，使用 ERC-721 建造。

我们可以创造新的怪物并指定主人。在那里，怪物的主人可以和其他人战斗来升级他们的怪物。

# 后续步骤

1.  这段代码中有一些漏洞。你能认出他们吗？[本文解释算术上溢和下溢](https://medium.com/better-programming/how-to-secure-your-smart-contracts-part-2-d3ec21f8685a)。
2.  我们这里还没有任何测试。为这个合同写一些 JavaScript 和 Solidity 测试。使用[这篇文章来学习更多关于测试你的智能合约](https://medium.com/better-programming/how-to-test-ethereum-smart-contracts-35abc8fa199d)。
3.  扩展这个代码并创建一个 React 前端，这样你就可以让用户加入你的 Ethermon 游戏了！跟随[这篇关于如何将 React 应用连接到区块链](https://medium.com/better-programming/how-to-connect-a-react-app-to-the-blockchain-fa9dbd0bbd69)的教程。