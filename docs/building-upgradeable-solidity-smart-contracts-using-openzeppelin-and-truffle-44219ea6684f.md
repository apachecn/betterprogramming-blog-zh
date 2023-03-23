# 利用 Openzeppelin 和 Truffle 构建可升级的可靠性智能合约

> 原文：<https://betterprogramming.pub/building-upgradeable-solidity-smart-contracts-using-openzeppelin-and-truffle-44219ea6684f>

## 端到端可靠性教程

![](img/e3203d089cbba63576a960b000aa99af.png)

Openzeppelin 和块菌图像

默认情况下，智能合约是不可变的，也就是说，在部署智能合约时，您不能更改智能合约的代码。如果您重新部署智能合约，它会生成一个新地址，您将会丢失以前的智能合约数据。

如果您不打算在任何时候更改智能合约代码，这完全没问题。

但是，如果您将来某个时候要更改您的智能合约代码，您必须想出一种有效的方法来迁移您的智能合约，而不会丢失合约数据和地址。

有一些手动方法可以迁移智能合同数据，并通过禁用旧地址来强制用户使用新生成的地址，但这是一项繁琐的任务。

在本文中，我们将学习如何使用 OpenZeppelin 可升级库和 Truffle 编写可升级智能合约。

让我们用一个 Kickstarter 合同作为用例。 [Kickstarter](https://www.kickstarter.com/) 是一个平台，允许用户为特定目的创建活动，其他用户也可以为活动做出贡献。

# **先决条件**

*   对坚实度和块菌的合理理解
*   Nodejs 和 npm
*   `ganache-cli`或 [Ganache](https://truffleframework.com/ganache) 桌面应用
*   松露

如果你没有安装 Node.js 或 npm，你将不得不安装[它们](https://nodejs.org/en/download/)，还要安装`ganache-cli`或桌面。

我更喜欢在全球范围内用`npm install -g truffle`安装松露，但是如果你决定在本地安装，也没关系。

安装后，使用您选择的名称创建一个项目目录，在终端中打开该目录并运行以下命令:

```
truffle init
```

`truffle init`将为你创建合同和迁移目录，以及一个`truffle-config.js`文件。接下来，更新你的`truffle-config`:

如果您想使用`ropsten`网络，创建一个`.env`文件并添加您的`ropsten`网络变量。

更新 truffle 配置文件后，安装所需的 npm 依赖项:

```
npm install @openzeppelin/contracts-upgradeable @openzeppelin/truffle-upgrades @truffle/hdwallet-provider dotenv
```

然后在 contracts 文件夹中创建两个文件`Kickstart.sol`和`KickstartFactory.sol`。

`Migration.sol`是你跑`truffle init` **的时候创建的。你**用下面这个更新`Migration`合同。借用了[松露官方文档](https://trufflesuite.com/docs/truffle/getting-started/running-migrations.html)中提到的例子。

将以下代码粘贴到`Kickstart.sol`文件中:

如果你已经注意到，我使用 getters 和 setter**来访问和改变契约的变量。**

**这是`OpenZepplin`对可升级智能合约变量变异的规范。也没有构造函数，而是使用了初始化函数。**

**你可以在这里阅读更多关于契约初始化器的内容。**

**将以下代码粘贴到`KickstartFactory.sol`文件中:**

**在`migrations`文件夹中:**

```
// 1_initial_migration.jsconst Migrations = artifacts.require("Migrations");module.exports = function (deployer) {
  deployer.deploy(Migrations);
};
```

**当您运行`truffle init`命令时，Truffle 将为您生成第一个初始迁移(`1_initial_migration.js`)。**

**在迁移目录`2_deploy_contracts.js`中创建新的迁移文件:**

```
// 2_deploy_contracts.jsconst { deployProxy } = require('@openzeppelin/truffle-upgrades');const Kickstart = artifacts.require("Kickstart");const KickstartFactory = artifacts.require("KickstartFactory");/** Deploy contract using openzeppelin deployProxy, which will create a proxy address for you */module.exports = async (deployer) => {
  const factory = await deployProxy(KickstartFactory, { deployer });
  const kickstart = await deployProxy(Kickstart, 
    [100,  '0x0000000000000000000000000000000000000000'], 
  { deployer   });
};
```

**粘贴上述代码后，运行以下命令:**

```
**truffle compile
truffle migrate
// Or
truffle deploy** // truffle deploy is an alias for truffle mograte
```

**迁移成功后用`truffle migrate` 运行:**

```
truffle console
```

**在合同控制台中，运行以下命令:**

```
// This will create KicstartFactory contract instance
**let factory = await KickstartFactory.deployed()**// Create a new kickstart campaign **factory.createCampaign(100)**// Get the created campaign
**factory.getDeployedCampaigns()**// Copy the address in the campaign list// Create a kickstart instance by running this command
**let kickstart = await Kickstart.deployed('0x11924C6967680124814fe7bFE0EB4a352CEB9Ef6')** // Replace the address with the address copied from the campaign list
```

**要升级`KicstartFactory`合同并添加新的`function get DeployedCampaignsLength()`，在`contracts`目录下创建一个新文件并命名为`KickstartFactoryV2.sol`。**

**此函数将获取已部署活动的总数:**

**在`migration`目录下创建一个新的迁移文件`3_upgrade_contracts.js` ，将以下代码粘贴到该文件中。**

```
// 3_upgrade_contracts.jsconst { upgradeProxy } = require('@openzeppelin/truffle-upgrades');const KickstartFactory = artifacts.require("KickstartFactory");
const KickstartFactoryV2 = artifacts.require("KickstartFactoryV2");module.exports = async (deployer) => {
  const existing = await KickstartFactory.deployed();
  console.log('Existing Address ===', existing.address); 

  const instance = await upgradeProxy(
    existing.address,  KickstartFactoryV2, { deployer });
  console.log("Upgraded Address ===", instance.address);
};
```

**如果你注意到了，我们这次没有部署`KickstartFactory` 合同。**

**相反，我们得到一个已经部署的版本的实例，并使用`existing.address` 作为`updateProxy`函数的第一个参数，将地址传递给新版本**

**粘贴前面的代码后，运行以下命令:**

```
truffle compile
truffle migrate 
// Or
truffle deploy
```

# **笔记**

**当使用 truffle 建立可升级的合同时，不要试图使用`reset flag (truffle migrate — reset)` **，**迁移您的合同，使用 reset 部署将删除您现有的所有合同状态和数据，重新开始迁移，这也将改变您的合同地址。**

**如果你想在一个开发环境中重置你的契约，你是自由的，但是在生产环境中使用`reset`是不明智的。**

**我希望你已经学会了如何用松露和`openzeppelin`可升级库建立一个可升级的智能契约。你也可以用`openzeppelin`和`hardhat`建立可升级的智能合约。**

**在 [GitHub](https://github.com/elraphty/upgreadeablesmartcontract.git) 上克隆完整的代码，也检查一下我的 [Solidity kickstart GitHub 项目](https://github.com/elraphty/soliditykickstart.git)，这是这个项目的不可升级版本，带有测试用例，也有一个前端与智能合约 ABIS 交互。**

**谢了。**