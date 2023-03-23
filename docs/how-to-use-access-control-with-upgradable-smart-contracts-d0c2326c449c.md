# 如何通过可升级的智能合同使用访问控制

> 原文：<https://betterprogramming.pub/how-to-use-access-control-with-upgradable-smart-contracts-d0c2326c449c>

## 使用 Hardhat 开发启用访问控制

![](img/e0f30c9e04efdc94011d5b64dbb82adb.png)

图片来自 Unsplash

使用可升级代理、透明可升级代理或信标代理可能会造成很大的混乱。更令人困惑的是如何为您的可升级合同使用访问控制和其他修饰符。

在本文中，我们将通过一个简单的例子来介绍如何使用 Hardhat 开发环境对可升级契约进行访问控制。如果你不熟悉代理模式，这里有一个关于[代理升级模式](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies)的很好的介绍。

# 设置

使用以下代码用 Hardhat 创建一个新的准系统项目:

像这样设置相关性:

*   “合同-可升级”插件包含所有合同的可升级变体，包括 ERC20、ERC721、ERC777 等。
*   “hardhat-upgrades”插件提供了使用 Javascript 部署和升级智能合约的语法糖。

接下来，将“安全帽升级”插件导入到您的`hardhat.config.js`文件中。要使用可升级的访问控制，您需要 solidity 编译器 0.8.2 或更高版本。一个简单的配置文件应该是这样的:

# 可升级合同

接下来，将`Greeter`合同修改为可升级。然后，我们可以执行以下操作:

1.  从 oppenzeppelin 导入可升级的变体。
2.  代替`Ownable`，我们使用可升级的变体`OwnableUpgradeable`
3.  用初始值设定项方法替换构造函数，使用初始值设定项修饰符。请注意，您需要使用适当的参数手动调用每个父契约的`__{contract}_init();`方法。
4.  将`setGreeter`标记为`onlyOwner`，并进行其他定制。

测试我们是否可以升级此合同，并保持访问控制。我们通过复制 Greeter.sol、修改契约名称并添加一个新方法(也是`onlyOwner`)来创建一个 GreeterV2.sol 契约。

# 测试

为了测试代理和访问控制是否按预期工作，我们构建了一个简单的测试:

1.  将旧的逻辑 Greeter.sol 部署为代理。
2.  `Owner`将问候语设置为“旧数据”。
3.  用新方法`resetGreeting`将逻辑升级到 V2。
4.  检查代理地址是否保持不变(该地址存储代理的状态，而不是逻辑)。
5.  检查问候语是否仍然是“旧数据”(在升级过程中保持原始状态)。
6.  检查新方法`resetGreeting`是否可以被`owner`调用。

我设置了其他几个基本测试来测试访问控制。随意查看我的完整代码[这里](https://github.com/shawlu95/Proxy-Ownable/blob/main/test/test_proxy.js)。

进行测试。结果在意料之中！

# 外卖食品

OpenZeppelin 为所有流行的 ERC 合同提供可升级的变体。为了使用可升级契约，我们需要使用可升级访问控制和其他继承契约的可升级变体。整个库可以在我的 [GitHub](https://github.com/shawlu95/Proxy-Ownable) 获得。

# 进一步阅读

*   [撰写可升级合同](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable)
*   [与安全帽一起使用](https://docs.openzeppelin.com/upgrades-plugins/1.x/hardhat-upgrades)
*   [与升级一起使用](https://docs.openzeppelin.com/contracts/3.x/upgradeable)