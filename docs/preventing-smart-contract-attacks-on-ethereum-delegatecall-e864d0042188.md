# 防止以太坊上的智能合约攻击——“委托呼叫”

> 原文：<https://betterprogramming.pub/preventing-smart-contract-attacks-on-ethereum-delegatecall-e864d0042188>

## 让我们编写一个易受攻击的智能合约代码，看看攻击是如何工作的，并了解修复它的预防技术

![](img/43c3afc29ab0ec2b757f5d7db38781fb.png)

图片来源:[阿诺·弗朗西斯卡](https://unsplash.com/@clark_fransa)

由于`DELEGATECALL`的上下文保持特性，构建无漏洞的定制库并不像最初想象的那样简单。库本身的代码可以是安全的，没有漏洞；然而，当在另一个应用程序的上下文中执行时，新的漏洞可能会出现。为了更好地理解这一点，让我们用斐波那契数来检查一个相当复杂的例子。

我们以`FibonacciLib.sol`中的库为例，它可以生成斐波那契数列和类似性质的数列。

FibonacciLib.sol

这个库提供了一个计算序列中第 n 个斐波那契数的函数。它还允许用户改变序列的起始数(`start`)并计算这个新序列中的第 n 个类似斐波那契数列的数字。

现在让我们来看一个使用这个库的契约，`FibonacciBalance.sol`。

FbonacciBalance.sol

## 剥削

该合约允许参与者从合约中提取乙醚，乙醚的数量等于参与者提取订单对应的斐波那契数；也就是说，第一个参与者得到 1 以太，第二个参与者也得到 1 以太，第三个参与者得到 2 以太，第四个参与者得到 3 以太，第五个参与者得到 5 以太，以此类推，直到合约的余额低于与所请求的提款相关联的斐波纳契数。

您可能已经注意到,`start`变量已经在库和主契约中使用。在库契约中，`start`用于指定斐波那契数列的开始，并被设置为`0`，而在主契约中，它被设置为`3`。您可能还注意到，FibonacciBalance 契约中的 fallback 函数允许将所有调用传递给库契约，这允许调用库契约的`setStart`函数。所以，这个契约的要点是我们保留了变量`start`的状态，对吗？但是如果我们使用`setStart`函数，那将让我们改变局部`FibonnacciBalance`契约中开始变量的状态。如果是这样的话，有人可以提取更多的乙醚，因为`calculatedFibNumber`依赖于起始变量。但是实际上，`setStart`函数并不改变`FibonacciBalance`契约中的开始变量。这份合同的真正问题远比玩弄`start`变量更糟糕。

现在请注意，在第 21 行的`withdraw`中，我们执行了`fibonacciLibrary.delegatecall(fibSig,withdrawalCounter)`。这调用了`setFibonacci`函数，它改变了存储`slot[1]`，在我们当前的上下文中是`calculatedFibNumber`。这当然是可以预料到的(即执行后，`calculatedFibNumber`被修改)。但是，记住`FibonacciLib`契约中的`start`变量位于存储器`slot[0]`中，即当前契约中的`fibonacciLibrary`地址。这意味着函数`fibonacci`将给出错误的答案，因为它引用了`start` ( `slot[0]`)，这在当前调用上下文中是 fibonacciLibrary 地址(当解释为`uint`时，它通常会很大)。所以很可能`withdraw`函数会失败，因为它没有`uint(fibonacciLibrary)`数量的乙醚，这是`calculatedFibNumber`将返回的。

更糟糕的是，`FibonacciBalance`契约允许用户通过第 26 行的 fallback 函数调用所有的`fibonacciLibrary`函数。这包括`setStart`功能，正如我们所讨论的，它允许任何人改变或设置存储`slot[0]`。正如我们所讨论的，那个存储`slot[0]`就是`fibonacciLibrary`地址。因此，攻击者可以创建一个恶意契约，将地址转换成一个`uint`(在 Python 中使用`int('<address>',16)`可以很容易地做到这一点)，然后调用`setStart(<attack_contract_address_as_uint>)`。它会把`fibonacciLibrary`改成攻击契约的地址。然后，每当用户调用`withdraw`或 fallback 函数时，恶意契约就会运行，窃取契约的全部余额。

这种攻击契约的一个例子是:

Attack.sol

## 预防措施

Solidity 中提供的`library`关键字确保了库契约的无状态性和不可自毁性，使其更加可靠。

## 真实世界黑客示例:平价 Multisig 钱包(第二次黑客攻击)

图书馆合同:

WalletLibrary.sol

钱包合同:

钱包. sol

注意，`Wallet`契约通过委托调用将所有调用传递给`WalletLibrary`契约。这段代码中的常量`_walletLibrary`地址充当实际部署的`WalletLibrary`合同(位于`0x863DF6BFa4469f3ead0bE8f9F2AAE51c91A907b4`)的占位符。

如果还没有，请查看另一个漏洞“可重入性”

[](/preventing-smart-contract-attacks-on-ethereum-a-code-analysis-bf95519b403a) [## 防止对以太坊的智能合约攻击——代码分析

### 让我们编写一个易受攻击的智能合约代码，看看攻击是如何工作的，并了解修复它的预防技术

better 编程. pub](/preventing-smart-contract-attacks-on-ethereum-a-code-analysis-bf95519b403a)