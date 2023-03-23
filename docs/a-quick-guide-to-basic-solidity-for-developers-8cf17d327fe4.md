# 开发人员基本可靠性快速指南

> 原文：<https://betterprogramming.pub/a-quick-guide-to-basic-solidity-for-developers-8cf17d327fe4>

## *让我们开始了解以太坊智能合约*

![](img/3bcd0530198706621fdf8e44a9bfe847.png)

[行政长官](https://unsplash.com/@executium?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/ethereum?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

这个快速指南是那些已经离开智能合约业务一段时间的人，或者想学习 Solidity 智能合约基础知识的新手的一个小小的复习。我自己还不是一个老手，但是我会尽我最大的努力来帮助新老学习者。

# 什么是稳固

大多数智能合同都是用这种语言编写的。这意味着从 NFT 到 Defi dApps 的任何东西都可以用 Solidity 编写。它运行在区块链上，代码也存储在那里。这意味着您不能有任何错误，因为一旦部署了契约，您就不能更新它。

幸运的是，以太坊、Polygon 和其他区块链有几个测试网，我们可以在将智能合约部署到现实世界的主网之前，在这些测试网上测试我们的智能合约代码。

# 新语法

对于下一部分，我假设您有一些编程知识。从函数和变量到类和接口。稳健并不难学，但你需要记住它的一些怪癖。

## 映射

映射是一种在 Solidity 中存储数据的方式。当给定一个键时，它们将为您检索值。例如，index 给出的令牌的所有者。

它们通常用于跟踪代币的所有者。但在更复杂的智能合约中，它们通常有很多用途。

## 功能

函数的语法在不同的语言中是非常标准的。由于修饰语的原因，Solidity 的语法略有不同。

`scope`表示谁可以调用该函数。这将是`private`、`public`、`internal`或`external`。一个`view`函数不需要任何油费，但只能从链中读取。

## 修饰语

修饰符确保只有特定的用户可以执行某个功能。最常用的默认修饰符是`onlyOwner`，它确保只有合同的所有者才能执行该功能。但是你可以自己做。

`_;`语法让代码返回到被称为修饰符的函数。如果您调用`_;`，代码将假设条件已经通过。

你可以用修饰符做很多检查，否则你必须在函数本身中做。

# 结论

如果你是其他领域的开发人员，这并不难学。如果你是一个新的开发人员，我可能要为你写另一篇文章。Solidity 是一门相当新的语言，会随着时间的推移而改变。如果你想在没有媒介的情况下从头开始学习，我强烈建议 [Crypto Zombies](https://cryptozombies.io/) 学习语言的基础知识。

你也可以阅读文档和更多的例子[在这里](https://www.tutorialspoint.com/solidity/index.htm)。

非常感谢您的阅读，祝您度过美好的一天。

## 想联系作者？

*查看* [*推特*](https://twitter.com/MVissers4) *。*