# 如何测试以太坊智能合约的访问限制

> 原文：<https://betterprogramming.pub/how-to-test-ethereum-smart-contracts-for-access-restriction-9dff445400d0>

## 编写可靠性测试，以确保对功能的访问受到正确的限制

![](img/3547358e7fbdadfbeb19821798467f01.png)

照片由[凯利·麦克林托克](https://unsplash.com/@kelli_mcclintock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/restricted?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

*先决条件:这篇文章假设你对 Solidity 和以太坊智能合约有所了解。*

控制对智能合同的访问对于确保安全性至关重要。常见的模式，如 [OpenZeppelin 的](https://openzeppelin.com/) `Ownable`和`AccessControl`契约，使开发人员能够轻松实现各种级别的功能访问。

使用这些库是很棒的，因为它们是由一个大型开发人员社区审核和审查的，从而最小化了风险，最大化了它们的效用。但是你怎么知道你正在正确地使用它们呢？

我们都会犯错，即使是最优秀的人。如果您在函数中遗漏了一个`onlyOwner`声明，该怎么办？如果没有测试，您可能会部署带有巨大漏洞的代码。

我们如何确保函数被正确地限制？

# 使用可靠性测试“仅所有者”访问

假设我们有一个名为`ExampleContract`的契约，它实现了 OpenZeppelin 的`[Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol)`契约。它有许多功能，其中一些可以由任何地址调用，一些只能由契约的所有者执行。这就是使用`Ownable`中的`onlyOwner`修改器的地方:

```
function somethingSomethingTheOwnerCanDo() public onlyOwner {
    ...
}
```

如果我们的合同有很多功能，很容易漏掉一个简单的修饰符。因此，我们需要为代码中每一条成功的路径和每一条不成功的路径编写测试。

# 松露套房

Truffle 套件允许我们用 JavaScript 和 Solidity 编写测试。使用 JavaScript 测试来测试`onlyOwner`修饰符的存在是很容易的，因为可以将多个帐户注入到测试运行程序中。从错误的帐户发送邮件，您将获得回复。

在可靠性测试中，这更加困难，因为所有的测试都是从一个地址运行的:测试合同。那么我们如何在可靠性测试中模仿这一点呢？

## **答案:通过使用代理合同**

使用我们的`somethingSomethingTheOwnerCanDo()`函数作为我们测试的目标，我们需要一个测试来确保如果不是所有者的其他人调用它，它会恢复。

让我们从创建`ProxyContract`开始，它发出我们预计会失败的调用，因为它不是所有者。

```
pragma solidity ^0.6.7;import "./ExampleContract.sol"contract ProxyContract {
  function attemptNonOwnerCall(ExampleContract _contract) public {
    _contract.somethingSomethingTheOwnerCanDo();
  }
}
```

在我们的测试中，我们需要初始化这个`ProxyContract`,并使用被测试的`ExampleContract`的实例作为参数来调用`attemptNonOwnerCall`。这个测试需要断言调用将失败。我们可以用`try` - `catch`来做。这是我们的测试:

```
function testNonOwnerFails() public {
    ExampleContract testTarget = new ExampleContract();
    ProxyContract proxy = new ProxyContract();
    string expectedReason = "Owner: caller is not the owner";
    try proxy.attemptNonOwnerCall(testTarget) {
        revert("It should fail");
    } catch Error(string memory reason) {
        Assert.equal(reason, expectedReason, "It should fail");
    }
}
```

让我们一行一行地看一下:

*   `testTarget`是我们正在测试的合同，具有受限功能
*   我们创建一个新的`proxy`合同
*   `expectedReason`是当调用违反`onlyOwner`修饰符时`Ownable`返回的消息
*   我们试图通过我们的`proxy`调用受限函数，他不是所有者，所以我们期望捕捉到一个错误
*   如果没有错误，`revert`命令在`try`块中运行，导致我们的测试失败
*   如果有错误，就调用`catch`块，我们断言这就是我们预期的错误:`expectedReason`

这只起作用，因为`ExampleContract`的所有者是我们的测试契约(初始化`ExampleContract`的契约)，调用`somethingSomethingTheOwnerCanDo()`的`msg.sender`是`ProxyContract`，它有不同的地址。

# 结论

很容易假设，因为我们使用经过良好测试的库，所以我们的代码是安全的。虽然这是一个公平的假设，但知道你是否正确使用它们的唯一方法是彻底测试每个功能，并确保你对它们的期望与现实相符。