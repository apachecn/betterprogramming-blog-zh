# 如何为标记令牌编写智能合同

> 原文：<https://betterprogramming.pub/how-to-write-a-smart-contract-for-stake-the-token-a46fdb9221b6>

## 了解标记的工作原理以及如何为令牌标记创建智能合同。

![](img/75cb646dd634ec529c64c85692e90f9e.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

今天，我们在区块链领域听到了许多新词，staking 就是其中之一。人们通常将赌注概念用于订婚。今天，我们将了解以下关于打桩的要点。

1.  什么是赌注？
2.  如何编写令牌标桩的智能合约？
3.  打桩过程是如何工作的？
4.  Rinkeby Testnet 上的部署。

# 什么是赌注？

*   赌注是一种通过定期或动态存放加密货币的利息赚取额外被动收入的方式。
*   如今，一些公司用“库存”这个词来表示订婚。
*   在这个游戏中，你把你的令牌或 NFT 押在赌注上，作为回报，你会得到一份奖励(这取决于你押的时间)。

# 如何编写令牌标桩的智能合约？

请注意以下步骤:

*   我们创建了样本 ERC-20 苹果(APL)令牌。
*   我们将提供 3 个月的令牌(你可以按你喜欢的方式保存它)。
*   3 个月后，你将获得 32%的奖励。例如:如果你在赌注上放 100 个代币，那么 3 个月后你将得到 132 个代币。

**打桩合同流程:**

*   首先，我们将一些 APL 令牌转移到标桩合同地址。
*   我们用两个参数调用 ERC20 契约中的`approve`方法。第一个标桩合同地址，第二个是金额(这里我们给出总供应量的 50%)。
*   我们用 amount 参数(您想要下注的金额)调用 Staking 契约中的`stakeToken`方法。
*   赌注期结束后，您可以调用赌注合同中的`claimReward`方法，您将获得您的金额和利息。

以下是在区块链世界实现这一目标的步骤。

*   创建 ERC-20 苹果(APL)令牌

*   我们将用这两个重要的方法创建一个 StakeAPL 契约

1.  赌注法
2.  索赔奖励方法

*   `StakeToken` 方法:用户将金额传入一个参数中(为魏格式)。该方法检查所有有效条件，然后将赌注金额转移到合同中，并将该条目存储到结构中，并发出 Staked 事件。

```
require(stakeAmount >0, “Stake amount should be correct”); require(block.timestamp < planExpired , “Plan Expired”); require(addressStaked[_msgSender()] == false, “You already participated”); require(aplToken.balanceOf(_msgSender()) >= stakeAmount, “Insufficient Balance”);aplToken.transferFrom(_msgSender(), address(*this*), stakeAmount);stakeInfos[_msgSender()] = StakeInfo({
            startTS: block.timestamp, 
            endTS: block.timestamp + planDuration,
            amount: stakeAmount,
            claimed: 0 });emit Staked(_msgSender(), stakeAmount);
```

*   `**claimReward**` 方法:用户在下注时长完成后获得奖励。首先，我们获得用户下注的金额，然后计算利息百分比并相加。将该数量传输到用户地址并发出`Claimed`事件。

```
uint256 stakeAmount = stakeInfos[_msgSender()].amount; uint256 totalTokens = stakeAmount + (stakeAmount * interestRate / 100); stakeInfos[_msgSender()].claimed = totalTokens; aplToken.transfer(_msgSender(), totalTokens); emit Claimed(_msgSender(), totalTokens);
```

# **打桩流程是如何工作的？**

*   我们已经有了一个 ERC-20 合同(APL 令牌),这个合同地址会传递到 StakeAPL 构造器部署时间中。
*   转让一些 APL 代币给`StakeAPL`合同。
*   第二个用户(拥有 APL 令牌)调用 APL 令牌`approve`方法，并批准使用标桩金额来标桩合同地址。
*   调用`stakeToken`方法，用金额作为参数来标记契约，就这样。在锁定时间到期后，您可以调用`claimReward`方法。你会连本带利得到这笔钱。

# **rinke by Testnet 上的部署**

*   你可以复制下面的代码并使用 remix 进行部署。你可以跟随[这篇文章来重新理解](https://guptaarnish-it.medium.com/how-to-verify-smart-contract-in-etherscan-polygon-and-binance-4ef4e2698fb0)。

[](https://guptaarnish-it.medium.com/how-does-the-bitcoin-seed-phrase-work-b4de10bc275b) [## 比特币种子短语是如何工作的？

### 种子短语是比特币钱包的关键。

guptaarnish-it.medium.com](https://guptaarnish-it.medium.com/how-does-the-bitcoin-seed-phrase-work-b4de10bc275b) 

感谢您的阅读。