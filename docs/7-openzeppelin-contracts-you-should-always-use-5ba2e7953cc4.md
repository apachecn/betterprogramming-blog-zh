# 你应该经常使用的 7 个 OpenZeppelin 合同

> 原文：<https://betterprogramming.pub/7-openzeppelin-contracts-you-should-always-use-5ba2e7953cc4>

## 使用 OpenZeppelin 改进您的智能合同开发

![](img/b2a30b78a961f949018614b783c91335.png)

OpenZeppelin 徽标

对于以太坊开发者来说，OpenZeppelin 的智能合同库是一个无价的资源。它拥有经过社区审查的 ERC 令牌标准、安全协议和其他实用程序的实现，使开发人员能够专注于功能，减少了重新发明轮子的需要。

> **“安全智能合约开发库。**建立在社区审查代码的坚实基础之上。”—[OpenZeppelin.com](https://docs.openzeppelin.com/contracts/2.x/)

以下是您应该在项目中使用的最有用的 Openzeppelin 智能合同列表。

***注意:在本文中，我们使用的是 OpenZeppelin contracts 2.5 . x 版，其中所有的合同都是使用 solidity 编译器 0.5.x 版编译的。更新的版本已经推出！***

# 控制访问

## 1.可拥有的

由`Ownable`契约提供的`onlyOwner`模式是一种简单但非常有效的模式，用于限制对某些功能的访问。这使得它成为智能合约开发人员非常受欢迎的选择。

它实现了一个前提，即部署智能合约的地址是其所有者。像转移所有权一样，某些函数应该只允许从所有者的地址调用。

图 1 显示了`Ownable`合同。

图 1:可拥有的合同

注意第 23 行的构造函数是如何设置契约所有者的。一旦任何子契约被初始化，默认情况下，初始化它的地址将成为`_owner`。

图 2 显示了扩展`Ownable`契约的简单实现:

图 2:可拥有的合同

通过给`restrictedFunction()`添加`onlyOwner`自定义修饰符，只有契约的所有者才能成功调用它。

## 2.角色

从`Ownable`向上的下一层是`Roles`库，它允许实现不止一个所有者角色。当一个契约具有多个访问级别的功能时，这是很方便的。

图 3 显示了`Roles`库。

图 3: Roles.sol

作为一个库，你不能像扩展抽象契约那样扩展它。相反，使用该库的契约使用一个`using`语句来声明该库提供的函数用于特定的数据类型。

例如，图 4 显示了一个契约，它使用`Roles`库将函数限制为两个角色:`_minters`和`_burners`。

图 4:使用角色

第 8 行声明这个契约必须使用`Roles`库来处理发生在`Roles.Role`数据类型上的任何操作。第 18 行显示了在`_minters.add()` — `add()`中使用的这个函数是由`Roles`库提供的。

# 算术

## 3.安全数学

不要使用简单的算术运算符，如加、减、除和乘，来编写数学计算。除非专门检查不足和溢出漏洞，否则不能保证计算是安全的。

这就是`SafeMath`的用武之地。它会执行所有必要的检查，确保您的计算正确运行，而不会给代码带来漏洞。

图 5 显示了`SafeMath`库。

图 5:安全数学库

像使用`Roles`库一样使用这个库，通过用`using`语句声明要由`SafeMath`执行的单元操作。这可以在图 6 的第 7 行看到。第 10 行显示了使用`SafeMath`库执行减法。

图 6:使用 SafeMath

## 4.安全广播

作为一名智能合同开发人员，您应该始终寻求节省执行时间和空间。节省空间的一种方法是对整数数据类型使用较小的大小。不幸的是，如果你对一个变量使用`uint8`并且想对它做一些`SafeMath`操作，你必须把它转换成`uint`(又名`uint256`)。如果你想让结果再次回到`uint8`中，你必须把它强制转换回来。`SafeCast`使您能够做到这一点，而不用担心溢出问题。

图 7 显示了`SafeCast`库。

图 8 显示了一个使用`SafeCast`库的基本实现，将`uint`转换为`uint8`数据类型。

图 8:使用安全广播

# 代币

## 5.ERC 20 详细信息

想都别想写一份完整的合同——open zeppelin 已经帮你完成了。你所需要做的就是扩展它并初始化它；剩下的代码已经完成了。

还有其他选项可供选择。比如 OpenZeppelin 提供的基本`ERC20`契约，只要不需要命名令牌就足够了。`ERC20Detailed`增加了命名、提供符号和小数位数的功能。

图 9 显示了使用`ERC20`和`ERC20Detailed`契约编写一个 ERC20 令牌有多简单。

图 9:自定义 ERC20 令牌

## 6.ERC 721 可数和 ERC 721 满

不可替代的代币`ERC721`也是如此。你不需要写一份已经完成的符合标准的合同。

通过调用`_tokensOfOwner(address owner)`，使用`ERC721Enumberable`而不是基本的`ERC721`来更容易地检索一个地址拥有的所有令牌。或者，如果你想要所有的铃铛和哨子，`ERC721Full`合同附带每一个额外的扩展。图 10 显示了`ERC721Full`的实现。

图 10:完整的 ERC721

# 公用事业

## 7.地址

不容易知道您的智能合约正在处理的地址是实际的钱包还是另一个智能合约。`Address`契约提供了一个名为`isContract()`的函数，它返回一个回答这个问题的布尔值。

图 11 显示了如何使用`isContract()`。

# 进一步阅读

如果你对区块链开发感兴趣，我会写一些关于入门和建立投资组合的教程、演练、提示和技巧。查看以下资源:

[](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248) [## 区块链开发资源马上跟进

### 学习区块链、以太坊和 DApp 开发的资源列表

medium.com](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248)