# 撰写更好的智能合同的 15 个技巧

> 原文：<https://betterprogramming.pub/15-tips-to-write-better-smart-contracts-abba3e94ddf2>

## 从定义目标开始，到确保目标可行结束

![](img/56209e3ac80d02b41a2683a0f0b68fdd.png)

来自 [Pexels](https://www.pexels.com/photo/round-gold-colored-ethereum-coin-1036637/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[驼鹿照片](https://www.pexels.com/@moose-photos-170195?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

# 1.定义目的

在开始之前，确保你清楚地了解你的智能合同应该做什么。它增加了什么价值？应该怎么用？考虑一下它是否需要成为一个区块链应用程序的问题。在更成熟的技术平台上实现会更好吗？

对智能合同的目的有一个清晰的认识。

# 2.检查之前是否做过

永远不要花时间重写已经存在的东西。如果你试图创造的是一个现有概念的不同风格，考虑扩展别人已经完成的工作。

[不要多此一举。](https://medium.com/better-programming/smart-contracts-dont-reinvent-the-wheel-63cee4370d19)

Openzeppelin 提供了一个实现 EIP 的合同库，比如 [ERC-20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol) 和 [ERC-721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol) 。通过扩展它们的功能来利用它们，而不是从头开始编写自己的。

# 3.将其命名

有一个明确的目的应该使命名你的智能合同变得容易。如果你有用面向对象语言开发的经验，就像命名一个类一样命名它。

名称应该清晰、描述性强、简洁。它应该尽量少用词，最好是一两个。它应该描述合同是什么，它做什么，并指出它负责什么。

# 4.选择您的开发环境

如果你已经有开发软件的经验，你可能会有一个首选的 IDE 来编码。多个 ide 支持 Solidity 开发。我最喜欢的是 [VS Code](https://code.visualstudio.com/) ，这是微软开发的自由文本编辑器，带有 [Solidity 扩展](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)。

如果你经验不足，不想担心设置环境的问题，[以太坊](http://ethereum.org)提供了一个很棒的在线 IDE，叫做 [Remix](http://remix.ethereum.org/) 。它允许您立即开始编码、编译和部署。

# 5.它是独立的智能合同吗？

有时聪明的合同会变得很长很复杂。如果你的合同有太多的责任，以至于名字不能描述它所做的一切，考虑分离关注点，使用[多重智能合同](https://medium.com/blockcentric/how-to-deploy-multiple-smart-contracts-using-truffle-5a7188a24366)。

如果你希望你的项目跨越多个智能合同，考虑使用 [Truffle Suite](https://www.trufflesuite.com/) 作为你项目的框架。松露让你很容易在合同上写 DApps。

# 6.选择坚实度版本

找出 [Solidity](https://solidity.readthedocs.io/en/v0.6.4/) 的最新稳定版本，并熟悉其语法。如果您正在扩展库，请确保您选择的版本不是这些库支持的版本。

比如 [Openzeppelin 契约库](https://github.com/OpenZeppelin/openzeppelin-contracts)0 . 2 . 5 版支持 Solidity 0 . 5 . 5 版，不支持 0.6.x。

# 7.它做数学吗？

如果您希望智能合约执行数学计算，请避免使用算术运算符，如加号(+)、减号(-)、乘号(*)、除号(/)和模数(%)。如果没有适当的检查，它们有[引入下溢和上溢漏洞的可能性](https://medium.com/better-programming/how-to-secure-your-smart-contracts-part-2-d3ec21f8685a)。

使用 Openzeppelin 的[安全数学库](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/math/SafeMath.sol) [进行无符号整数运算](https://medium.com/better-programming/smart-contracts-dont-reinvent-the-wheel-63cee4370d19)。

使用 SafeMath 从一个整数中减去另一个整数的示例

# 8.限制对某些功能的访问

大多数智能协定都需要对函数进行访问限制，只有协定的所有者才能调用这些函数。一个常用的模式是`onlyOwner`模式。这就是自定义修饰符要求函数的调用方等于所有者地址的地方。

为了避免为此编写自己的逻辑，Openzeppelin 提供了一个名为 [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) 的契约，您可以在代码中扩展这个契约[。](https://medium.com/better-programming/smart-contracts-dont-reinvent-the-wheel-63cee4370d19)

使用 onlyOwner 限制函数访问的示例

# 9.安全发送值

如果您希望您的智能合约将以太或 ERC-20 令牌发送到其他地址，您必须[考虑可能引入](https://medium.com/coinmonks/how-to-secure-your-smart-contracts-dc500f2c8fca)的漏洞。由于过去几年中[高调的黑客攻击](https://www.coindesk.com/understanding-dao-hack-journalists)，可重入攻击在整个行业广为人知。

发送以太网时，始终使用`transfer()`而不是`call()`，并且永远不要假设你要发送的地址是一个好的行为者。

# 10.降低复杂性

函数应该简洁明了。任何看起来太大或执行嵌套循环或递归的函数都应该重新访问。更多的计算意味着更多的汽油，意味着执行这些功能的成本更高。

考虑使用离链计算来减轻区块链应用程序的负担。例如，分散式交易所使用链外订单填充算法来填充订单，因为该算法相当复杂。一旦订单完成，结算将在区块链上进行。

如果降低复杂性太困难，考虑一下区块链可能不是你问题的理想解决方案。

# 11.节省空间

有没有永远不会填充其数据类型大小的变量？如果是这样，您应该能够通过更改数据类型来减少智能合约使用的空间量。

如果所有的整数值都是 uint 数据类型，但有些值只达到最大值 100，那么考虑 uint8。它的最大值为 255。

Openzeppelin 提供了一个名为 [SafeCast](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/SafeCast.sol) 的库，将整数值转换成更小的数据类型。

使用 SafeCast 将`uint`变量转换为 uint8 的示例

# 12.彻底测试

确保你[写尽可能多的测试](https://medium.com/better-programming/how-to-test-ethereum-smart-contracts-35abc8fa199d)。考虑每一种可能发生的情况，并测试每一条代码路径，以确保预期的结果每次都会发生。

除了在本地区块链上测试之外，还要在尽可能多的测试网上部署和测试。这些更接近地模拟了 Mainnet 环境，所以更好地表现了你的应用一旦上线后的表现。

# 13.向他人展示

让他人可以看到代码是有价值的。

拥有一双新鲜的眼睛，不受狭隘视野的阻碍，常常能捕捉到从开发人员的网中溜走的错误。第二意见和专家建议是非常有价值的工具，所以尽可能使用它们。他们的好处是在你允许自己展示给别人看之前，让你对自己的作品更加挑剔。

你也可以写一些关于你的智能合约的文档或文章，向他人解释它们。这将有助于他们理解你工作的基本概念，并帮助他们找出你可能引入的任何错误。

# 14.考虑如何利用它

像一个糟糕的演员一样思考。

想一想，如果你想窃取智能合约的资金或让它变得无用，你会如何尝试利用智能合约。问问你自己，你的智能合同在哪里容易受到攻击，你怎样才能降低风险。

如有必要，使用审计服务和[静态代码分析](https://consensys.github.io/smart-contract-best-practices/security_tools/)工具。

# 15.确保它能工作

最后，它做了你期望它做的事情吗？问问你自己，它是否服务于你一开始就设想的目的。你能写一个应用前端，把你的智能合同变成一个完整的 DApp 吗？