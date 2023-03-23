# 使用 ERC1155 标准创建多个 NFT

> 原文：<https://betterprogramming.pub/erc1155-example-to-mint-multiple-nfts-cc716673d556>

## 实际操作的例子

![](img/c23f28009a9b9797f77c31369007704e.png)

图片来自 Unsplash

在我的[上一篇文章](https://medium.com/@sandnc.eth/smart-contracts-with-timed-transitions-modifiers-reentrancy-guard-and-withdrawals-f168a4ce386c)中，我谈到了一些为定时转换、重入保护和撤回资金添加函数修饰符的技巧和提示。

然而，这是通过拖放代码片段完成的。

在本文中，我将向您展示如何创建一个使用所有这些部分的 ERC1155 NFT 合同。

ERC1155 标准允许您铸造一个唯一的 NFT 令牌 id 的多个副本。这使得它对于分散的游戏应用非常有用，玩家可以在那里铸造剑、魔法药水、制服等。

我将在这里展示的代码可以复制粘贴到 [ETH-Remix](https://remix.ethereum.org/) 中，以使用各种功能。

我故意省略了`batch mint`函数，作为读者的练习。

Open zeppelin 拥有非常好的文档来分解他们的 [ERC1155](https://docs.openzeppelin.com/contracts/3.x/erc1155#:~:text=ERC1155%20is%20a%20novel%20token,their%20guides%20before%20moving%20on.) 标准，这与 [ERC721](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721) 标准形成了鲜明的对比，在后者中，一个 NFT 令牌只能有一个副本。

简而言之，当允许的薄荷量等于“1”时，ERC1155 NFT 与 ERC721 NFT 相同。

这里是我们将使用的所有函数修饰符。在我的[上一篇文章](https://medium.com/@sandnc.eth/smart-contracts-with-timed-transitions-modifiers-reentrancy-guard-and-withdrawals-f168a4ce386c)中有所涉及。

当使用 ETH-Remix 时，请确保使用双引号输入您的价格，以便输入作为字符串。

例如:对于“1 乙醚”，请使用“100000000000000000”。

那么你需要一些方法来跟踪你的 NFT 储备，并在耗尽储备时停止铸造。ERC1155 将管理用户的 NFT 余额。

您可以通过将 NFT 储备推入 struct `NFTInfo`类型的数组来跟踪它们。然后，您可以创建一个 getter 函数来返回一个类型(`NFTInfo`)。

以下是此合同的构造者。我在代码中嵌入了示例输入，使得部署和测试契约更加容易，而不必每次都输入构造函数参数。

如果您选择这样做，只需注释掉`ids_`、`prices_`、`maxAmounts_`、`beneficiary`和`devAddress_`的构造函数参数。

下一个关键功能是合同的核心。因为它允许用户帐户创建 NFT。只要用户账户有足够的资金，而不是无效的地址，并且用于`tokenID`的 NFT 储备还没有用完，用户账户就被允许铸造 NFT。

完整的契约代码可以在我的 Github 上找到，您可以在自己选择的 IDE 中使用。

[](https://github.com/cryptonomicon46/ERC1155.git) [## GitHub—cryptonomicon 46/ERC 1155:ERC 1155，带薄荷和定时转换

### 这是 open zeppelin 对 ERC1155 NFT 合同的简单实现。目标是能够设定一个合同…

github.com](https://github.com/cryptonomicon46/ERC1155.git) 

目前就是这样，希望这有助于您理解如何使用 ERC1155 标准制作 NFT。

作为对读者的一个练习，我省略了批量制造函数。`batchMint()`应该允许一个用户帐户创建多个`tokenIDs`的副本，并且应该可以通过对`mintSingle()`功能的微小修改来实现。

这里有一个链接，链接到 [batch mint 函数的一个实现。](https://coinsbench.com/erc1155-batchmint-function-a16513fbb937)