# 智能合同:不要重新发明轮子

> 原文：<https://betterprogramming.pub/smart-contracts-dont-reinvent-the-wheel-63cee4370d19>

## 总是使用 OpenZeppelin

![](img/ad5c74153288166c961554959178694e.png)

乔恩·卡塔赫纳在 [Unsplash](https://unsplash.com/s/photos/wheel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片。

不要误解我，从头开始写智能合同是一个很好的学习工具。从头到尾让自己创建一个符合 ERC20 的合同是理解其背后逻辑的最佳方式之一。

但是，对于生产应用程序，不要重新发明轮子。

像 [OpenZeppelin](https://openzeppelin.com/) 这样的库受到以太坊开发社区的广泛关注。它们不断被更新和审查。他们也可以公开访问，易于使用。当这样的库存在时，创建自己的库是没有意义的。

# 正在导入 OpenZeppelin

*注意:更高的版本是可用的，但我们使用的是库版本 2.5.0，因为它是运行 Solidity 版本 0.5.x 的 OpenZeppelin contracts 的最后一个版本*

要将 OpenZeppelin 安装到您的项目中，请运行以下命令:

```
npm install @openzeppelin/contracts@2.5.0 --save
```

这会将所有智能合约下载到`node_modules/`中。

安装后，您可以将智能合同导入您自己的合同。这是一个在智能合约中导入 OpenZeppelin `ERC20`令牌合约的示例:

```
pragma solidity ^0.5.5;import "@openzeppelin/contracts/token/ERC20/ERC720.sol";contract MyContract is ERC20 { ...
```

这里有一些来自 OpenZeppelin 的有用的令牌、数学、实用程序和可拥有的契约，您可以轻松地在项目中实现它们。

# 代币

## **ERC20**

有六个公开可用的函数`ERC20`契约需要实现，以及两个事件。通过导入 OpenZeppelin 的`ERC20`契约，你不需要自己实现这些。

ERC20 令牌示例。

`ERC20Detailed`用于初始化令牌的名称、符号和小数，但是如果您的项目不需要命名的`ERC20`令牌，就不需要它们。

## ERC721

因为`ERC721`标记是唯一的，所以通常使用一个结构和一个结构数组来存储它们。

ERC721 令牌示例。

这个契约继承了所有九个函数和三个事件，并公开了创建新的`Person`令牌的能力，而不是实现所有九个函数和三个事件。

# 数学

## **安全数学**

在大多数现代编程语言中，算术运算的安全性是要考虑的，所以很少考虑它们的实现。然而，在坚固性方面，溢出和下溢存在安全风险。

`[SafeMath](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/math/SafeMath.sol)`是一个库，它通过在超出整数数据类型的界限时恢复事务来确保安全的算术运算。

SafeMath 示例。

`using`语句向编译器表明，契约正在使用在`SafeMath`中为`uint`操作定义的函数。不使用算术运算符(`+`、 `-`、`*`、 `/`、`%`)，而是使用函数`add()`、`sub()`、`mul()`、`div()`和`mod()`。

# 实用工具

## **SafeCast**

将`uint`变量转换为较小的整数可能会有溢出的风险。使用`SafeCast`将较大的整数数据类型转换为较小的整数数据类型(即`uint256`到`uint8`)。

这可与仅在`uint256`上执行算术运算的`SafeMath`一起使用。

SafeCast 示例。

# 所有权

## **可拥有的**

有时需要限制对智能协定中某些函数的访问，以便只有协定的所有者才能成功调用它们。一个常见的模式是在函数定义中使用`onlyOwner`修饰符。OpenZeppelin 的`Ownable`合同为你做了这些，而不是你自己编码这个模式。

可拥有的例子。

# 结论

对于编码标准的最基本的实现，总是使用库。在这一点上，业界最好的是 OpenZeppelin。这比你自己重新发明轮子更安全、更快捷、更清洁、更容易。