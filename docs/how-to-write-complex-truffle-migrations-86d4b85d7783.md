# 如何编写复杂的块菌迁移

> 原文：<https://betterprogramming.pub/how-to-write-complex-truffle-migrations-86d4b85d7783>

## 有了多个契约和依赖注入

![](img/ebd7a0311896480d298748c97ef20f68.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3750157) 的 [xresch](https://pixabay.com/users/xresch-7410129/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3750157) 。

本文是为学习如何使用 Truffle 套件进行区块链开发的开发人员准备的。如果你以前从未使用过 Truffle，请在继续下一步之前阅读[如何通过六个步骤使用 Truffle 部署智能合约](https://medium.com/swlh/develop-test-and-deploy-your-first-ethereum-smart-contract-with-truffle-14e8956d69fc)。

部署合同并不总是像运行`deployer.deploy(MyContract)`一样简单。如果您需要部署多个，该怎么办？如果您需要部署一个在其构造函数中接受参数的智能协定，该怎么办？以下是如何用 Truffle Suite 处理复杂迁移的方法。

# 多重合同

Truffle 使用一个`migrations/`文件夹来存储迁移脚本。如果你浏览了几本[松露教程](https://medium.com/swlh/develop-test-and-deploy-your-first-ethereum-smart-contract-with-truffle-14e8956d69fc)或者[官方文档](https://www.trufflesuite.com/docs/truffle/overview)，你会注意到这个文件夹里已经预装了一个名为`1_initial_migration.js`的文件。这是因为，为了让 Truffle 迁移工作，该文件需要在执行任何其他迁移之前将`Migration`合同部署到区块链。

Truffle 以升序运行每个迁移文件，因此文件应该这样命名。既然`1_initial_migrations.js`已经被占用，我们的迁移应该从`2_`开始。比如`2_deploy_contracts.js`。这是我们编写迁移代码的地方。

图 1 显示了部署单个契约的迁移:

图 1:单一部署。

为了从同一个迁移文件部署另一个契约，我们需要新的契约，并在函数中添加一个 deployer 语句，如图 2 所示。

图 2:多个合同。

图 2 中的迁移部署了`MyERC20`和`SecondERC20`。

# 依赖注入

并非每个智能协定都有空白构造函数。通常，他们希望在初始化时将参数传递给他们。例如，我们的`SecondERC20`智能契约中的构造函数定义可能是这样的:

```
constructor(address _owner) public { ...
```

这就是所谓的依赖注入，因为我们注入的是契约正确运行所依赖的东西。

如果是这样，我们的迁移脚本需要在部署时传递一个`address`给它。仅仅打电话给`deployer.deploy(SecondERC20);`是不行的。

幸运的是，`deploy`函数接受多个参数。它尝试将收到的任何额外参数注入到它正在部署的协定的构造函数中。图 3 向我们展示了它的样子:

图 3:依赖注入。

好吧，但是如果一个智能契约作为参数接受的依赖项是另一个智能契约呢？在这种情况下，我们部署合同的顺序很重要。

假设我们有一个用于分散式 exchange 平台的智能合约和一个用于 ERC20 令牌的智能合约。`Exchange`合同的构造者接受`ERC20`合同作为参数。为了迁移成功，我们必须首先部署`ERC20`契约，从部署中检索值，然后将其作为参数传递给我们的`Exchange`部署。图 4 向我们展示了它的样子:

图 4:注入智能契约。

注意这个函数现在是怎样的`async`，我们`await`第一个部署函数的返回值。这是因为`deploy()`是异步的，返回一个承诺。然后我们获取结果并将其传递给`Exchange`部署调用。瞧啊。

# 进一步阅读

如果你对区块链开发感兴趣，我会写一些教程、演练、提示以及如何开始和建立投资组合的技巧。查看以下资源:

[](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248) [## 区块链开发资源马上跟进

### 学习区块链、以太坊和 DApp 开发的资源列表

medium.com](https://medium.com/blockcentric/blockchain-development-resources-b44b752f3248)