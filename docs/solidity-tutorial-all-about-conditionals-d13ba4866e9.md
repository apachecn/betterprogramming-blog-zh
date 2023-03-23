# 坚实度教程:关于条件句的所有内容

> 原文：<https://betterprogramming.pub/solidity-tutorial-all-about-conditionals-d13ba4866e9>

## 通过智能合同示例了解

![](img/75cc81715457fb4da78554d0b1676db4.png)

Alex Padurariu 在 [Unsplash](https://unsplash.com/) 上拍摄的照片

每天写代码的人都知道什么是`if / else`语句。但是，既然我们在 [*“关于可靠性的一切”系列文章*](https://medium.com/coinmonks/solidity-tutorial-all-about-constructors-46a10610336) 中，我们将涵盖关于可靠性智能合同语言的一切！

> 没有弯曲的规则！我们不能省略条件句！

在今天的文章中，我们仍然会涵盖 Solidity 中条件句的基础知识，以及它们的一些变体。

但是我们不会从头开始重新解释`if`语句，我们将编写 4 个非常受欢迎的智能合约项目的可靠性代码:CryptoKitties、BarnBridge、Sablier 和 LUKSO。

所以我们也可以学习和了解一些流行的智能合约是如何工作的，增加我们的好奇心。

# 目录

```
**Overview****Example 1 — CryptoKitties** Basic if statement (one condition)
  Either one condition or another with ||
  Use a variable as a condition**Example 2 — BarnBridge** Concatenating multiple conditions with &&
  if { … } else { … }**Example 3 — Sablier** One line if statements
  What else can be written inside if statements in Solidity?**Example 4 — LUKSO** omitting curly braces**Final Notes on Solidity conditionals****References**
```

# 概观

Solidity 中的条件遵循其他编程语言的基本结构:

*   条件包含在一个`if`语句的括号内。
*   如果条件评估为`true`，花括号内的代码运行。
*   如果条件评估为`false`，程序执行跳转到右括号`}`后的下一条语句。

这里没什么新鲜的！但是让我们看看 mainnet 上目前运行的一些真实的 Solidity 代码！

# 例 1 — [隐猫没有性别](https://medium.com/u/c8b1419b5d28#:~:text=Note%3A%20CryptoKitties%20do%20not%20have,as%20either%20matron%20or%20sire.) *、*但是它们繁殖的方式遵循和动物一样的规则。产生后代有特定的规则。

一只新的 **CryptoKitten** 可以通过繁殖产生:

*   一位*护士长* (=“女性”)
*   一头*公畜* (=“公的”)

你可以想象，不是每一个女主人和男主人都能一起繁殖！有些配对是不允许的！。

CryptoKitties 的繁殖规则可以在游戏的 Solidity 代码中找到。合同的名字说明了一切:`KittyBreeding.sol`。看看[功能](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162) `[_isValidMatingPair(...)](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162)` [](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162)。我在下面添加了要点代码。

[功能](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162) `[_isValidMatingPair(...)](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162)` [第 123 行](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L117-L162)

## **基本 if 语句(一个条件)**

```
if ( <condition-to-be-met> ) {
    // run this Solidity code...
}
```

小猫*“不能和自己繁殖”*。所以我们不能为护士长和先生提供相同的身份。因此，如果护士长是相同的先生，它不是一个有效的交配对。 [*参见 KittyBreeding.sol，第 134 行*](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L134) *。*

```
// A Kitty can't breed with itself!        
if (_matronId == _sireId) {            
    return false;        
}
```

## **一个条件或另一个条件**

正如任何人可能猜测的那样，小猫不能和父母一起繁殖。所以无论是父系还是母系。

这可以通过在条件中使用短路操作符`||`来完成。如果两个条件中至少有一个为真，那么`if`块中的代码将会运行。

在隐猫的情况下，维护对将无效。*参见*[*kitty breeding . sol，第 139 行。*](https://github.com/dapperlabs/cryptokitties-bounty/blob/79cb69e7aadfc68fc116cd82dcc21f4336cc744c/contracts/KittyBreeding.sol#L139)

```
// Kitties can't breed with their parents.        
if (_matron.matronId == _sireId || _matron.sireId == _sireId) {        
    return false;        
}if (_sire.matronId == _matronId || _sire.sireId == _matronId) {            
    return false;        
}
```

## **使用变量作为条件**

第一款受欢迎的 NFT 游戏 CryptoKitties 使用了一种拍卖机制。它包括一个名为`[**autobirth**](https://www.cryptokitties.co/blog/post/learn-how-our-last-mystery-smart-contract-works/)`的特性，允许任何人建立一个守护进程在合适的时间调用`giveBirth(...)`函数。

这基本上相当于:

> *挖掘隐猫！⛏ 😸 🙀⛓*

cryptokitties 中的`autobirth`选项可以在合同`KittyAuction.sol`中找到。如果拍卖中的出价包括`*autobirth*` *费用*，则`doAutoBirth`模式被激活并用于创建新的 cryptokitten。一个额外的`AutoBirth`事件在网络上发出，作为 cryptokitten 是由守护进程创建的信号。

```
if (doAutoBirth) {            
    // Auto birth fee provided, trigger autobirth event                
    Kitty storage matron = kitties[_matronId];            
    AutoBirth(_matronId, matron.cooldownEndTime);        
}
```

# 示例 2 -巴恩布里奇

![](img/fac85a2a2e88781b462b79b6a1903917.png)

图片来源:[docs.barnbridge.com](https://docs.barnbridge.com/)

我们将通过查看[巴恩布里奇](https://barnbridge.com/)的可靠性代码来了解智能合约中更多形式的条件。

BarnBridge 是一个旨在赌 Defi 风险的协议。用户可以购买债券(以代币的形式)，并使用这些债券来押注其他 Defi 协议可能遇到的潜在风险(*例如:*通货膨胀、Defi 利率的变化、Defi 协议的基础 ERC20 代币的价格波动等)。

他们针对不同的风险类别提供几种产品/债券。[智能收益率](https://docs.barnbridge.com/smart-yield/beginners-guide-to-smart-yield) s 就是其中之一，关注 Defi 利率的变化。

简而言之，巴恩布里奇的目标是:

> 将 Defi 环境中的风险符号化

让我们看看`SmartYield.sol`契约，以获得更多的`if`条件的例子。

## **串联多个条件**

您可以使用短路操作符`&&`连接条件。

在巴恩布里奇，这是用来检查过期债券的。未清偿的优先债券一旦到期，就应立即取消。 [*参见 SmartYield.sol*](https://github.com/BarnBridge/BarnBridge-SmartYieldBonds/blob/d55ddb1daa41f01921750fb1c87cf71d06746cb2/contracts/SmartYield.sol#L570) 中的第 576 行

## **如果…否则…**

经典的`if`陈述离不开`else`陈述。这里有一个基本的提醒:

*   如果条件评估为`true`，则`if`块内的代码将运行。
*   如果条件评估为`false`，将运行`else` blcok 中的代码。

这是来自巴恩布里奇的一份声明。它用于根据到期日处理次级债券。 [*见 SmartYield.sol，第 326 行。*](https://github.com/BarnBridge/BarnBridge-SmartYieldBonds/blob/d55ddb1daa41f01921750fb1c87cf71d06746cb2/contracts/SmartYield.sol#L326-L336)

# 示例 3 — [功能](https://medium.com/u/dc89fff4a7be#L237-L263) `[withdrawFromStream(...)](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L237-L263)`，尤其是[线 258](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L258) 。

如果用户从他/她以前打开的流式“频道”中取钱，协议检查频道中的剩余余额。如果没有剩余资金，通道将被关闭并删除。

这是通过一行`if`语句完成的，不需要用花括号打开一个块。

## Solidity 中的 if 语句里面可以写什么？

几乎任何东西！基于契约的逻辑。但是一些常见的例子是:

*   拨打外部合同电话
*   `return`功能

这是 Sablier 的两个基本例子。

契约中的最后一个[函数](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L287-L288) `[cancelStream(...)](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L287-L288)`允许取消流，并将所有令牌传送回流的预期接收者。

您可以在函数内部看到对用于支付流的令牌契约的外部调用(通过`token.safeTransfer(...)`)。*见* `*Sablier.sol*` *，第 287–288 行。*

又如，`[deltaOf(...)](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L101-L102)` [函数](https://github.com/sablierhq/sablier/blob/6ccec84f5d61c64dd981aa28b5c831fe491f90ab/packages/protocol/contracts/Sablier.sol#L101-L102)包括两个`if`语句，即`return`立即依赖于`**block.timestamp**` **的值。** *见* `*Sablier.sol*` *，第 101—102 行。*

我在这里已经给出了两个基本的例子，但是正如你可以想象的，`if`条件句可以导致几乎任何可靠的东西。其他场景可能是更新契约状态或恢复。

# 示例 4 — [LSP6KeymanagerCore.sol](https://medium.com/u/2376b006b57f#L268-L269) [的第 268–269 行找到这个条件`if`语句](https://github.com/lukso-network/lsp-smart-contracts/blob/e7a84948c6e1fb2e994540c3eb529cb89748411e/contracts/LSP6KeyManager/LSP6KeyManagerCore.sol#L268-L269)[。我为简单创造了一个简单的要点，去触及本质。](https://github.com/lukso-network/lsp-smart-contracts/blob/e7a84948c6e1fb2e994540c3eb529cb89748411e/contracts/LSP6KeyManager/LSP6KeyManagerCore.sol#L268-L269)

# **关于稳固性条件句的最后注释**

关于坚固性条件句的最后两点注释:

*   `if`条件句不能省略括号。
*   与 C #不同，没有从非布尔类型到布尔类型的转换。

这意味着以下陈述在可靠性方面无效:

```
if (1) {
    // ...
}
```

# 参考

 [## 坚固性-坚固性 0.8.13 文件

### 部署合同时，您应该使用最新发布的 Solidity 版本。除了特殊情况，只有…

docs.soliditylang.org](https://docs.soliditylang.org/en/v0.8.13/) [](https://k3no.medium.com/control-structures-in-ethereum-3f2d4149b84a) [## 以太坊中的控制结构

### 听着，我喜欢以太坊，我认为它很酷，我甚至写了一本关于它的书，我真正不喜欢的是…

k3no.medium.com](https://k3no.medium.com/control-structures-in-ethereum-3f2d4149b84a) [](https://medium.com/cryptokitties/cryptokitties-birthing-fees-increases-in-order-to-accommodate-demand-acc314fcadf5) [## 隐猫分娩费用增加，以适应需求

### 所以你可能已经注意到 CryptoKitties 在这个周末爆发了——以一种好的方式。你可能也注意到了…

medium.com](https://medium.com/cryptokitties/cryptokitties-birthing-fees-increases-in-order-to-accommodate-demand-acc314fcadf5) [](https://www.cryptokitties.co/blog/post/learn-how-our-last-mystery-smart-contract-works/) [## 了解我们最后一个神秘的智能合同是如何工作的- CryptoKitties 博客

### 当我们推出 CryptoKitties 时，我们有意围绕 CryptoKitties 基因组培养神秘感和发现。在…

www.cryptokitties.co](https://www.cryptokitties.co/blog/post/learn-how-our-last-mystery-smart-contract-works/) [](https://www.cryptokitties.co/blog/post/cryptokitties-birthing-fees-increases-in-order-to-accommodate-demand/) [## CryptoKitties 分娩费用增加，以适应需求- CryptoKitties 博客

### 所以你可能已经注意到这个周末 CryptoKitties 有点爆炸了——以一种好的方式。你可能也注意到了…

www.cryptokitties.co](https://www.cryptokitties.co/blog/post/cryptokitties-birthing-fees-increases-in-order-to-accommodate-demand/) [](https://barnbridge.com/) [## barn bridge-DeFi 风险标记协议

### BarnBridge 旨在降低与 DeFi 相关的风险，如通货膨胀风险、市场价格风险和现金流…

barnbridge.com](https://barnbridge.com/) [](https://github.com/BarnBridge/BarnBridge-SmartYieldBonds/tree/master/contracts) [## 巴恩布里奇-SmartYieldBonds/主巴恩布里奇/巴恩布里奇-SmartYieldBonds 的合同

### SMART Yield 允许用户从其他项目的债务池中分出收益，如 Aave、Compound 或…

github.com](https://github.com/BarnBridge/BarnBridge-SmartYieldBonds/tree/master/contracts) [](https://github.com/sablierhq/sablier) [## GitHub - sablierhq/sablier:资金流协议

### 资金流动的协议。如果您想了解 Sablier 在高层是如何工作的，请从我们的常见问题开始，并查看…

github.com](https://github.com/sablierhq/sablier) [](https://faq.sablier.finance/) [## 常见问题

### 对于 Sablier，以太坊区块链上的实时金融协议。

faq.sablier.finance](https://faq.sablier.finance/)  [## 卢克斯特技术文件

### LUKSO 和相关标准的网络、标准、工具和开发指南。

docs.lukso.tech](https://docs.lukso.tech/) [](https://github.com/lukso-network/lsp-smart-contracts) [## GitHub-lukso-network/LSP-smart-contracts:通用概要的参考实现…

### 通用配置文件智能合约的参考实现。有关更多信息，请参见文档…

github.com](https://github.com/lukso-network/lsp-smart-contracts) [](https://github.com/lukso-network/lsp-smart-contracts/blob/e7a84948c6e1fb2e994540c3eb529cb89748411e/contracts/LSP6KeyManager/LSP6KeyManagerCore.sol#L268-L269) [## 位于 e7a 84948 c 6 E1 FB 2e 994540 c 3 EB 529 CB 89748411 e 的 LSP-smart-contracts/LSP 6 keymanagercore . sol…

### 此文件包含双向 Unicode 文本，其解释或编译可能与下面显示的不同…

github.com](https://github.com/lukso-network/lsp-smart-contracts/blob/e7a84948c6e1fb2e994540c3eb529cb89748411e/contracts/LSP6KeyManager/LSP6KeyManagerCore.sol#L268-L269)