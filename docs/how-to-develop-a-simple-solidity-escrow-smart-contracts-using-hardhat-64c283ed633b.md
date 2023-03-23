# 如何使用 Hardhat 开发一个简单可靠的托管智能合同

> 原文：<https://betterprogramming.pub/how-to-develop-a-simple-solidity-escrow-smart-contracts-using-hardhat-64c283ed633b>

## 在本教程中，我们将创建一个简单的托管智能契约，测试它，并使用 Hardhat 将其部署在 Testnet 上。

![](img/8645f8a7a21fb8bcfe9abdb0bc5a2b71.png)

[水彩壁纸矢量](https://www.freepik.com/vectors/watercolor-wallpaper)由 [Freepik](http://www.freepik.com) 创作

假设你对区块链相对陌生，不用担心。首先，在一步一步地编写我们的智能合约之前，我们将回顾一些可靠性和安全帽的基础知识。在本教程结束时，您应该能够使用 Solidity 和 Hardhat 重新创建一个托管智能合同。我们开始吧！

# 什么是扎实？

智能合约是一个简单的程序，它按照作者设置的预定义规则在区块链上执行交易。以太坊的智能合约使用一种指定的编程语言，Solidity。Solidity 是一种面向对象的编程语言，专为在以太坊虚拟机(EVM)上运行智能合约而构建，语法类似于其他编程语言 C++、Python 和 JavaScript。

Solidity 在将智能合约部署到以太坊虚拟机之前，将它编译成一系列字节码。每个智能合同都有其地址。要调用特定的函数，您需要一个应用程序二进制接口(ABI)来指定您想要执行的函数，并返回您期望的格式。

创建智能契约需要一个开发环境，用于在 Testnet 上测试和部署契约。有很多替代品可供选择，如松露和它的 Ganache 套件或 Remix，Solidity IDE。但是还有第三种选择，[安全帽](https://hardhat.org)。

# 什么是安全帽？

Hardhat 是一个使用 Node.js 构建的 Solidity 开发环境，它在 2019 年首次发布了测试版，此后一直在发展壮大。有了 Hardhat，开发者不需要离开 JavaScript 和 Node.js 环境来开发智能合约，比如 Truffle。

测试用 Hardhat 构建的智能合约也很容易，因为 Hardhat 有一个即插即用的环境，不需要你建立一个个人以太网来测试你的智能合约。为了连接到智能契约，您使用 [Ethers.js](https://docs.ethers.io/v5/) ，为了测试它们，您可以使用众所周知的 JavaScript 测试库，如 [Chai](https://www.chaijs.com) 。

# 安装安全帽

安装 Hardhat 很简单。您需要安装 npm 和 Node.js v12。假设您使用 Linux，您需要运行以下命令:

```
sudo apt update curl -sL [https://deb.nodesource.com/setup_12.x](https://deb.nodesource.com/setup_12.x) | sudo bash - sudo apt install nodejs
```

然后，要安装 npm，请运行以下代码:

```
sudo apt install npm
```

安装 npm 后，就可以安装 Hardhat 了。Hardhat 基于 Node.js，只能使用 npm 安装。创建一个新目录并启动 Node.js 项目:

```
mkdir hardhat-example cd hardhat-example npm init -y
```

然后，安装 Hardhat 作为开发依赖项:

```
npm i --save-dev hardhat
```

要启动一个安全帽项目，你需要一个`hardhat.config.js`文件。您可以使用下面的命令自动生成它:

```
npx hardhat
```

创建一个空的`hardhat.config.js`。您的安全帽环境几乎准备好了。您只需要安装其他依赖项:

```
npm i --save-dev @nomiclabs/hardhat-ethers ethers chai
```

Hardhat 使用 Ethers.js 连接到智能契约，并使用 Chai 作为断言库。打开你的`hardhat.config.js`，添加下面的代码:

瞧啊。您已经创建了您的 Solidity 开发环境。本教程中的智能合约将使用 Solidity 0 . 8 . 4 版。

# 编写您的第一份 Solidity 智能合同

在本例中，您将创建一个简单的托管智能合同，类似于 [Tornado Cash](https://tornado.cash) 。执行智能合约的每个用户将向智能合约存放几个令牌，智能合约将返回一个哈希。您可以使用哈希将令牌提取到不同的帐户中。

## 引导您的智能契约进行开发

本教程将使用开放式 Zeppelin 智能合约。Open Zeppelin 提供了一个由社区审查的安全智能合同库。要使用 Open Zeppelin smart contracts，请使用 npm 在您的项目中安装它们的库:

```
npm i -S @openzeppelin/contracts
```

Open Zeppelin 对 ERC20 和 ERC721 令牌都有实现标准。在本例中，您将使用 ERC20 标准。

您的智能合约将使用 DAI 加密货币，但是您必须创建一个模拟的 DAI 令牌来测试您的本地节点。我们将为令牌和托管智能合同创建智能合同模板。

首先，创建一个新的 contracts 目录并创建一个名为`MockDaiToken.sol`的文件:

然后，创建另一个名为`Escrow.sol`的文件:

您只能在本地环境和测试环境中使用 MockDaiToken。当在 Testnet 上测试时，使用实际的 DAI 令牌。pragma Solidity 版本将用于 Solidity 版本`0.8.0`和更高版本。

## 托管存款功能

将代币存入托管智能合约非常简单。您将把资金从您的钱包转移到智能合约的钱包。

每份智能合约都有一个钱包，你可以在里面存放你的资金。但是，您还需要用唯一的散列来映射每笔存款。您可以使用`mapping`类型存储地图，并添加一个`deposit_count`来计算您使用`uint`类型输入了多少存款:

存款函数将有两个参数，交易散列和交易金额。交易散列将从函数外部生成，并与存款金额一起插入到映射中。因为您将收到两个参数，所以您必须验证它们以确保用户不会插入恶意的输入。

您可以使用`require`方法来验证这三个条件:

1.  验证事务哈希不为空
2.  验证托管金额是否不等于零
3.  验证事务哈希是否不冲突并且尚未使用

输入成功验证后，将其插入`mapping`并增加存款计数。接下来，创建一个`view`函数，它根据发送者的地址、存款金额和现有存款数量生成一个惟一的散列:

创建一个`view`函数并在外部调用它，而不是在存款函数内部调用它，这将减少您的函数需要消耗的汽油费。与存款函数不同，`view`函数本质上只是读取区块链的当前状态而不改变它。

## 退出托管

要从托管中提取资金，您需要创建一个接受交易散列参数的独立函数。该功能假定您将提取全部已存入的保管暨代付款服务，不能用于部分提取。您需要验证两个条件:

1.  验证事务哈希是否不为空
2.  验证事务哈希的映射是否存在

之后，您可以将资金转移到汇款人的地址，并将映射的余额设置为零:

## 最终托管智能合同文件

如果您正确地遵循了教程，您的智能合约将如下所示:

接下来，您需要使用 Chai 测试您的智能合约。

# 使用 Hardhat 测试您的智能合约

使用 Hardhat 的最大优势之一是测试套件非常简单。如果您已经熟悉 JavaScript 测试，您可以很快适应 Hardhat 的测试，尤其是如果您经常使用 Chai。

## 配置您的测试

在开始测试和部署托管智能契约之前，您需要启动 MockDaiToken 智能契约。托管智能合约依赖于 ERC20 令牌地址:

## 计划快乐和不快乐的测试

软件测试有一条快乐的路和一条不快乐的路。快乐的路径是当你测试软件的成功场景时，而不快乐的路径是当你测试软件可能出现的每个异常时。

将有两个功能需要测试，提取托管和存款托管。当事务成功时，每个函数都有一条快乐路径。然而，确定不愉快路径数量的一个好的经验法则是计算参数必须通过的验证次数。

在提现托管功能的情况下，将有两个验证。因此，它有两条不愉快的道路:

1.  正在验证事务哈希是否为空。
2.  正在验证映射中是否存在事务哈希。

对于存款托管功能，将有三个验证。但是，存款要求您使用大量的令牌，并且有可能在 amount 参数中输入比您拥有的更多的令牌。因此，您必须再添加一个验证测试，因此该函数有四条不愉快的路径:

1.  正在验证事务哈希是否为空。
2.  验证提交的存款金额是否不为零。
3.  正在验证映射中是否不存在事务哈希。
4.  验证发送者是否有足够的存款。

## 测试存款功能

在定义了快乐和不快乐的路径之后，您可以编写您的单元测试。首先，写快乐的道路，这将是最容易的。

## 快乐之路

在配置阶段，您已经为快乐路径和不快乐路径测试定义了一个帐户，您可以相应地使用它们:

测试将使用 Ethers.js 与智能合约进行交互，并将 Chai 用作断言库。

## 不幸的道路

接下来，通过实现不愉快的路径来增加测试的覆盖率。您需要测试四条不愉快的路径:

1.  正在验证事务哈希是否为空
2.  验证提交的存款金额是否不为零
3.  正在验证映射中是否不存在事务哈希
4.  验证发送者是否有足够的存款

要模拟一个空散列，可以使用`ethers.constants.HashZero`:

模拟零金额相当于`ethers.utils.parseUnits("0")`:

在下一个测试中，您可以使用它们`happyPathAccount`,因为您将模拟一个事务散列是否已经存在于映射中:

最后，即使一切都过去了，你仍然需要有足够的零用钱。

## 测试取款功能

现在，我们将使用取款功能重复一遍。

## 快乐之路

在测试取款函数的快乐路径之前，您还需要调用存款函数:

## 不幸的道路

您需要测试两个不愉快的路径来实现取款功能:

1.  正在验证事务哈希是否为空
2.  正在验证映射中是否存在事务哈希

# 将您的智能合约部署到 Testnet

Hardhat 为您提供了一个简单的界面，您可以使用它来部署您的智能合约。

## 配置您的安全帽部署

您可以将您的智能合约部署到任何以太坊测试网，包括 Ropsten、Kovan、Goerli 和 Rinkeby 测试网。每个 Testnet 都有不同的 RPC 连接，您不会想要一个接一个地硬编码它们。

您可以在`hardhat.config.js`中添加连接详情:

理想情况下，您希望在环境变量中包含 RPC URL 和部署者私有密钥。您可以[用私钥创建一个新的以太坊钱包](https://vanity-eth.tk)。

在本例中，您将在本地 Testnet 和 Rinkeby Testnet 中部署您的智能合约。
要访问 JavaScript 中的环境变量，您可以使用`dotenv` npm 包来使用`.env`文件，而不是硬编码它们。用下面的命令安装`dotenv`:

```
npm i -S dotenv
```

`dotenv`是作为依赖项安装的，而不是作为开发依赖项安装的，因为您将在开发环境之外使用它。

## 创建您的部署脚本

首先，您需要定义您的部署阶段。创建一个新的`.maintain`目录并生成一个新的`deployment.js`文件:

您需要在部署之前获取 Hardhat 参数:

检索目标网络名称和 RPC URL 后，继续部署您的智能合约。

只有当您部署到一个本地测试网时,`MockDaiToken`才会被部署。否则，您希望使用实际的 DAI 令牌:

接下来，部署您的托管智能合同。托管智能合约在其构造函数中接受 ERC20 令牌地址。您需要为目标网络提供 DAITokenAddress:

您的部署脚本已经完成！您可以部署您的托管智能合同。

# 使用 Hardhat 运行本地以太网

您可以通过运行以下代码轻松启动本地以太网:

```
npx hardhat node
```

上面的命令将在`port 8545`上本地启动一个新的以太坊 RPC 服务器。您可以创建一个前端应用程序，并使用 Metamask 连接到您的本地 RPC 服务器。

# 部署您的智能合同

在本地或像 Rinkeby 这样的测试网上部署您的智能合约也是类似的。您可以使用`--network`标志定义要部署智能合约的网络。如果要部署到本地网络，命令如下:

```
npx hardhat run .maintain/deployment.js --network localhost
```

否则，如果您想在 Rinkeby Testnet 上部署:

```
npx hardhat run .maintain/deployment.js --network rinkeby
```

如果一切成功，它将返回这样的内容:

```
> hardhat-article@1.0.0 deploy:rinkeby /home/user/hardhat
> npx hardhat run --network rinkeby .maintain/deployment.js
Deploying to network rinkeby [https://rinkeby.infura.io/v3/3656fc820asc4e72bf3jdk1948480640](https://rinkeby.infura.io/v3/3656fc820asc4e72bf3jdk1948480640)
Contracts deployed!
Deployed Escrow Contract address 0xF6C2123ba061eE544A6363836155FD6af86D7C08
```

恭喜您，您已经部署了您的托管智能合同！

# 结论

在本文中，您了解了如何使用 Hardhat 来开发、测试和部署以太坊智能合约。接下来，您可以通过学习将您的前端应用程序从浏览器连接到智能合约来进行更深入的学习。

我希望你喜欢这篇文章！如果你有任何问题，一定要留下评论。编码快乐！

*最初发表于*【https://blog.logrocket.com】