# 固体中的储存和记忆秘密

> 原文：<https://betterprogramming.pub/storage-and-memory-secrets-in-solidity-cd88ea0a981b>

## 在执行函数时，我们是否希望数据在调用智能协定或保存在本地之间保持不变？

![](img/d43883e14038cf9398be3bab0abc0d6e.png)

照片由 [Lia Trevarthen](https://unsplash.com/@melodi2) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄。

这一次，我们将讨论 Solidity 编程语言中的存储位置，特别是关于`storage`和`memory`位置。不知道它们代表什么以及它们如何工作会导致我们的智能合同出现问题。

# 储存；储备

智能合约中的存储保存函数调用之间的数据。我们可以想象,`storage`会被当成电脑中的硬盘。即使我们将其关闭，数据也不会被删除。在区块链上，我们在存储器中写的东西被存储起来。

# 默认存储

## 状态变量

默认情况下，Solidity 将保存存储智能合同的状态变量。

```
contract StorageContract {
  struct LuckyNumber {
    uint256 number;
    string reason;
  } mapping(address => LuckyNumber) luckyNumbers;
}
```

在这个例子中，智能合约的状态变量`luckyNumbers`保存在存储器中，并且数据将在函数调用之间保持不变。

当我们相加并得到幸运数字时，我们有一个可预测的结果。

```
function addLuckyNumber(LuckyNumber memory luckyNumber) external {
  require(luckyNumber.number != 0, "Lucky number can't be 0!");
  require(luckyNumbers[msg.sender].number == 0, "You already have set lucky number. Edit it if you have another one."); luckyNumbers[msg.sender] = luckyNumber;
}function getMyLuckyNumber() external view returns(uint256) {
  require(luckyNumbers[msg.sender].number != 0, "You don't have a lucky number set yet."); LuckyNumber memory luckyNumber = luckyNumbers[msg.sender]; return luckyNumber.number;
}
```

## 局部函数变量

默认情况下，结构、数组或映射的局部函数变量保存在存储中。这意味着，如果我们在函数中声明这些值，它们会保存在存储中，这可能会导致难以跟踪的意外问题。

如果我们在代码示例中添加一个函数`editLuckyNumber`，并将本地副本标记为`storage`，它将编辑我们期望的状态变量。

```
function editLuckyNumber(uint256 luckyNumber) external {
  require(luckyNumber != 0, "Lucky number can't be 0!");
  require(luckyNumbers[msg.sender].number != 0, "You don't have a lucky number set yet."); LuckyNumber storage _luckyNumber = luckyNumbers[msg.sender];
  _luckyNumber.number = luckyNumber;
}
```

# 记忆

在内存中，Solidity 保留所有本地定义的值类型，可以是 uint、string 等。，但不是数组、结构或映射。函数参数也保存在内存中。记住`memory`不能在智能合约级别使用，只能在函数中本地使用。

```
function multiplyByItself(uint256 number) external pure returns(uint256) {
  uint256 result = number * number; return result;
}
```

在这个例子中，我们在函数中传递的函数参数`number`存储在内存中。此外，`result`的本地定义变量存储在内存中，一旦函数执行结束，该变量将被释放。

# 使用内存和存储的缺陷

Solidity 编程语言中错误使用关键字`storage`和`memory`的一个主要陷阱是，我们在没有考虑清楚的情况下声明变量`storage`或`memory`。首先，在`storage`保存数据会消耗更多的汽油，因为我们需要为块空间付费。其次，我们应该问自己是否需要访问保存在函数调用之间的数据。通过函数调用，甚至可以有两个不同的函数。

如果我们使用关键字`memory`定义函数`editLuckyNumber`中的`_luckyNumber`，它将只在本地编辑这个函数，而不会将更改写入区块链。

```
function editLuckyNumber(uint256 luckyNumber) external {
  require(luckyNumber != 0, "Lucky number can't be 0!");
  require(luckyNumbers[msg.sender].number != 0, "You don't have a lucky number set yet."); LuckyNumber memory _luckyNumber = luckyNumbers[msg.sender];
  _luckyNumber.number = luckyNumber;
}
```

这个函数的结果将导致编辑幸运数字不起作用，因为我们只在本地更新它。

# TL；速度三角形定位法(dead reckoning)

在我们的智能合约中使用 Solidity 语言存储数据是一件至关重要的事情。使用值类型更容易，但是使用数组、结构和映射就更难了。这就是为什么当我们想要保存这些变量时，询问是必要的。在执行函数时，我们是否希望数据在调用智能协定或保存在本地之间保持不变？

# 链接

*   [样本代码](https://gist.github.com/fassko/5b19bee0c78e3e41aa5d52573b2e696e)
*   [什么是记忆关键词？它是做什么的？](https://docs.soliditylang.org/en/v0.3.3/frequently-asked-questions.html#what-is-the-memory-keyword-what-does-it-do)
*   [存储与存储的稳固性](https://www.geeksforgeeks.org/storage-vs-memory-in-solidity/)
*   [在以太坊 Solidity 中，“记忆”关键字的用途是什么？](https://stackoverflow.com/questions/33839154/in-ethereum-solidity-what-is-the-purpose-of-the-memory-keyword)