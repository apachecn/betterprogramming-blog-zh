# Solidity 智能合约安全性:防止重入攻击的 4 种方法

> 原文：<https://betterprogramming.pub/solidity-smart-contract-security-preventing-reentrancy-attacks-fc729339a3ff>

## 检查、效果和交互(CEI)、互斥、拉支付和 Gas 限制都是防止可重入攻击的有效技术。

![](img/dbd7db4ba05b59cf540b672956d88405.png)

照片由 [Unsplash](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

可重入是一种编程技术，在这种技术中，函数执行被外部函数调用中断。在外部函数调用的逻辑中，存在允许它在原始函数执行能够完成之前递归调用自身的条件。在某些情况下，重复重新进入一个进程来执行外部逻辑可能是可取的，但不一定是一个错误。但是，对于智能协定，不推荐使用这种技术，因为它将控制流的执行释放给了可能试图利用资金的不受信任的协定。此外，在执行对外部契约的调用时，应该使用防重入模式和防护来防止这种类型的攻击。

有三种主要的技术可以防止重入:

*   检查、效果、交互(CEI)
*   重入保护/互斥
*   拉付款

此外，最后一种方法可能有效，但不推荐使用:

*   气体极限

# 检查、效果、交互

CEI 模式是防止重入的一种简单而有效的方法。检查是指条件的真实性。效应是指由相互作用引起的状态改变。最后，交互指的是功能或契约之间的事务。

下面是一个不要做的事情的例子(交互*先于*效果):

```
// contract_A: holds user's fundsfunction withdraw() external {
​ ​ ​​uint userBalance = userBalances[msg.sender];​ ​ ​​require(userBalance > 0);​ ​ ​​(bool success,) = msg.sender.call{ ​​value: ​userBalance ​​}("");
​ ​ ​​require(success,);​ ​ ​​userBalances[msg.sender] = 0;
}
```

以下是攻击者的接收函数:

```
// contract_B: reentrancy attackreceive() external payable {
​ ​ ​​if (address(contract_A).balance >= msg.value) {
​ ​ ​​​ ​ ​​contract_A.withdraw();
​ ​ ​​}
}
```

攻击者的 receive 函数接收提取的资金，应该只返回“`success`”，但是检查`contract_A`是否包含更多的资金。如果为真，`contract_B`再次递归调用取款函数，直到所有资金用完。

下面是一个使用 CEI 模式的取款功能示例:

```
function withdraw() external {
​ ​ ​​uint userBalance = userBalances[msg.sender];​ ​ ​​require(userBalance > 0);​​​ ​ ​​**userBalances[msg.sender] = 0;**​ ​ ​​(bool success,) = msg.sender.call{ ​​value: ​userBalance ​​}("");
​ ​ ​​require(success,);
}
```

通过在将资金转移到`contract_B`之前将用户在`contract_A`中的账户余额清零，在`contract_B`发起重入攻击时，取款功能中的条件将为假，执行将恢复。正如这个案例所强调的，一行代码的位置可能是具有重大漏洞和可重入安全性的区别。

# 重入保护/互斥

可重入保护或互斥(互斥标志)可以构造为函数或函数修饰符，但逻辑很简单:在易被重入的函数调用周围放置一个布尔锁。“locked”的初始状态为 false(未锁定)，但在易受攻击的函数执行开始之前立即被设置为 true(已锁定),然后在它终止后被设置回 false(未锁定)。

下面是一个使用上面的取款函数的例子:

```
bool internal locked = false;function withdraw() external {
​ ​ ​​require(!locked);
​ ​ ​​locked = true;​ ​ ​​uint userBalance = userBalances[msg.sender];
 ​ ​​require(userBalance > 0);
​ ​ ​​(bool success,) = msg.sender.call{ ​​value: ​userBalance ​​}("");
​ ​ ​​require(success,);
​ ​ ​​userBalances[msg.sender] = 0;​ ​ ​​locked = false;
}
```

尽管这个撤销函数不遵循 CEI 模式，因此容易受到重入攻击，但是简单的布尔“锁定”变量可以防止重入，因为第一个 require 语句将等同于 false 并恢复事务。

# 拉付款

这最后一项技术被 [Open Zeppelin](https://docs.openzeppelin.com/contracts/4.x/api/security) 推荐为最佳实践。然而，在自动化方面有一点折衷。拉式支付通过中介托管发送资金并避免与潜在的恶意合同直接接触来实现安全性。

在这里，合约资金被发送给中介托管:

```
function sendPayment(address user, address escrow) external {
​ ​ ​​require(msg.sender == authorized);​ ​ ​​uint userBalance = userBalances[user];​ ​ ​​require(userBalance > 0);​ ​ ​​userBalances[user] = 0;​ ​ ​​(bool success,) = escrow.call{ ​​value: ​userBalance ​​}("");
​ ​ ​​require(success,);
}
```

在这里，托管资金可以由接收方提取:

```
function pullPayment() external {
​ ​ ​​require(msg.sender == receiver);​ ​ ​​uint payment = account(this).balance;​ ​ ​​(bool success,) = msg.sender.call{ ​​value: payment​ ​​}("");
​ ​ ​​require(success,);
}
```

通过中介托管发送资金，合同资金可以防止重入攻击。如果托管为多个帐户持有资金，则托管可能会被重入，因此在适用的情况下，应该实现 CEI 模式和/或重入保护。

# 气体极限

最后，气体限制可以防止重入攻击，但这不应该被认为是一种安全策略，因为气体成本取决于以太坊的操作码，这些操作码可能会发生变化。另一方面，智能合约代码是不可变的。无论如何，了解函数之间的区别是有价值的:`send`、`transfer`和`call`。

函数 send 和 transfer 本质上是相同的，但是如果事务失败，transfer 会恢复，而 send 不会。

```
// transfer will revert if the transaction failsaddress(receiver).transfer(amount);// send will not revert if the transaction failsaddress(receiver).send(amount);
```

关于可重入性，send 和 transfer 都有 2300 个单位的气体限制。使用这些函数应该可以防止重入攻击的发生，因为这不足以递归回调原始函数来利用资金。

与发送和转移不同，调用没有 gas 限制，并将转发其 gas 以执行复杂的多合同事务。当然，后者也包括可重入攻击。

# 结论

成功的可重入攻击可能是毁灭性的，并可能耗尽受害者合同中的所有资金，因此了解潜在的漏洞并实施有效的保护措施非常重要。

无论是否存在漏洞，都应该默认实现 CEI 模式；这是很好的实践。额外的安全性可以通过使用重入保护和/或拉式支付来实现。最后，气体限制可以防止再入，但不应该被认为是一种安全策略。