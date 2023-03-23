# 如何烧 NFT

> 原文：<https://betterprogramming.pub/how-to-burn-an-nft-c306c9cb6df>

## [NFT 系列](https://medium.com/@donpablooooo/list/nft-series-34dea60327e7)

## ERC721 可靠性合同

![](img/a031d98971ea0f7c1f480a100c38501e.png)

[姚期](https://unsplash.com/@yaoqiqiqilai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# **什么是 NFT**

不可替换令牌(NFT)是存储在区块链中的唯一可识别的数字资产。与可替代的加密货币不同，NFT 是独一无二的。

NFT 的所有权在区块链注册，所有者可以转让，从而可以出售和交换非森林产品。

NFT 通常指的是数字文件(图像、视频或其他)，NFT 的市场价值与其所指的数字文件相关联。

那么，如果 NFT 是有价值的数字资产，我为什么要烧掉它们呢？

# **为什么要烧 NFT**

一个原因可能是希望通过让某个特定收藏的 NFT 变得更稀有来提高其价值。例如，拥有一个足球运动员雕像的所有十个 NFT 的人可能决定销毁除一个之外的所有 NFT，以增加剩余一个的价值。

但是，更常见的是，公司:

*   向所有者提供烧掉他们的 NFT 的机会，以换取实物产品或 NFT 的“升级”版本
*   通过宣布他们将在特定日期后销毁所有未售出的非食品药品，敦促人们迅速购买一滴非食品药品
*   消除未售出或有问题的 NFT

# **如何刻录**

奇怪的是， [EIP-721](https://eips.ethereum.org/EIPS/eip-721) 声明:

> “NFTs 的销毁(“焚烧”)不包括在本规范中。您的合同可以通过其他方式实现这些。”

好了，我们来讨论一下燃烧特性！

我们需要从 ERC-721 的实施开始。OpenZeppelin [ERC721](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol) 是使用最广泛的实现之一。

这是合同代码的一个片段:

正如我们所看到的，这个实现已经提供了一个内部的`_burn`函数。该函数接受令牌 id 作为输入，并执行以下操作:

*   获取 NFT 的所有者
*   调用`_beforeTokenTransfer`钩子函数(钩子是什么稍后会更清楚)
*   清除令牌的所有批准
*   减少所有者余额
*   发出传输事件
*   调用`_afterTokenTransfer`钩子函数

那么，任务完成了？

等等，有东西不见了！

该函数不检查发送者是否被授权对令牌进行操作。我们自己做吧！

为此，我们可以使用`_beforeTokenTransfer`钩子函数。

如 [OpenZeppelin 文档](https://docs.openzeppelin.com/contracts/3.x/extending-contracts#using-hooks)所述:

> “钩子只是在某个动作发生之前或之后被调用的函数。它们提供了一个集中点来挂钩和扩展原始行为。”

`_beforeTokenTransfer`钩子是一个内部虚函数，在任何令牌传输(包括铸造和烧录)之前调用，条件如下:

*   当“from”和“to”都不为零时，我们就处于令牌传输的情况下
*   当“from”是零地址时，我们处于铸造情况
*   当“到”为零时，`tokenId`即将被烧毁
*   “从”和“到”永远不会都是零

遵循挂钩的 OpenZeppelin [规则，遵循`BurnableNFT`实现。](https://docs.openzeppelin.com/contracts/3.x/extending-contracts#rules_of_hooks)

在第 33 行，我们检查我们是否处于烧录或传输场景(“from”不是零地址)。

那么，允许转移或烧录 NFT 的唯一地址必须是它的所有者，因此，在第 34 行，我们得到了`tokenId`的所有者，并且“要求”它必须等于事务的发送者(`msg.sender`)。

然后，唯一要做的就是添加一个调用父契约`_burn`函数的公共`burn`函数。

所有的固体代码和测试可以在:[https://github.com/donpabblo/burnable-nft](https://github.com/donpabblo/burnable-nft)找到

如果你在这里，谢谢你的阅读。我真诚地希望这个解释对你有用。

更多阅读来自 [**NFT 系列**](https://medium.com/@donpablooooo/list/nft-series-34dea60327e7) 。