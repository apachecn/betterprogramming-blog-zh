# 用 Hardhat 和 Ethers.js 编写和测试智能合同

> 原文：<https://betterprogramming.pub/writing-and-testing-a-smart-contract-with-hardhat-and-ethersjs-2b0dbd450997>

## 关于如何使用 Hardhat 环境和 Ethers.js 库编写和测试智能合约的基本概述

![](img/9c89971b3d07524a1c5d8fba8d0efa97.png)

照片由[钟登祥](https://unsplash.com/@dengxiangs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/ethereum?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[Hardhat](https://hardhat.org/) 是一个以太坊开发环境，就像 Truffle 一样，我们将使用它来开发我们的契约并将其部署在我们的本地网络上，而 [EthersJs](https://docs.ethers.io/v5/#:~:text=What%20is%20Ethers%3F,a%20more%20general%2Dpurpose%20library.) 是一个用于与以太坊区块链交互的库，我们将在我们的测试套件中使用它来与部署的契约进行交互。

# 安装和设置

Hardhat 环境的完整安装指南可以在[官方网站](https://hardhat.org/getting-started/)上找到，但是让我们来回顾一下我们这个小项目的必要步骤。

因此，让我们运行以下命令

```
npm install --save-dev hardhat && npx hardhat
```

这将安装软件包并启动交互式界面来创建项目。我们将选择`> Create an advanced sample project that uses TypeScript`并继续。

Hardhat 将生成几个文件和文件夹，但我们只对其中的几个感兴趣。

`contracts`文件夹将托管我们的智能契约，`test`文件夹托管我们的测试，`hardhat.config.ts`托管从 hardhat 生成的配置——为此，我们需要确保这里定义的 solidity 版本与我们契约的指定版本相匹配。

# 撰写智能合同

想象一下，你一直没能存下你的硬币，因为你忍不住要花钱，最终你打破了你的储蓄目标。好吧，如果你能把你的存款锁在一个金库里，只有在特定的日期过后，它才会把你的硬币还给你。这是我们将要写的智能合同背后的想法。

让我们在合同文件夹中创建一个名为`LockSave.sol`的文件。

对于这个简单的契约，我们需要三个基本函数:

1.  为最小取款日期保存 ETH 的功能
2.  一个在指定日期过后撤销的函数
3.  从所有者的地址中检索所有储蓄的功能

我们不会过多关注合同的细节，但它看起来是这样的。

首先，我们需要定义我们储蓄的形式

接下来，我们需要声明几个变量，这些变量有助于存储和将节省映射到它们的创建时间戳，并将所有者映射到保存时间戳。这种映射有助于操作和访问我们的数据。

我们需要几个自定义错误来处理不需要的行为，比如当储蓄的金额等于零时，或者当一个地址想要在日期过去之前撤销时。

## 储蓄功能

saving 函数接收取款时间戳，向地址保存添加新的保存，并返回数据。我们使用`isValidSaving`修饰符来检查金额和取款时间。

## 撤回功能

我们使用储蓄时间戳来查找储蓄，将其从储蓄中删除，然后最终将储蓄金额转移回所有者。

但是在提取之前，我们首先检查地址是否有储蓄以及提取时间，并将这些修饰符传递给上面的`withdraw`函数。

最后，我们将添加一个函数来检索属于请求地址的所有储蓄。

# 测试智能合约

如上所述，我们将在`test`文件夹中创建一个名为`index.ts`的文件，然后从`chai`、`ethers`和`hardhat`导入。

接下来，我们将在`beforeEach`中声明我们的测试套件并部署我们的契约。这将在我们每次运行一个新的测试用例时部署一个新的契约，这样我们总是有一个空白的契约来处理。

## 保存和检索保存的内容

为了测试保存功能，我们将在定义取款时间戳后进行保存，然后使用`getSavings`函数获取保存。之后，我们将使用返回的保存来断言值和时间戳。

我们使用`ethers.getSigners`来获取签名者地址，并提取第一个地址`[sender]`，这是 Hardhat 使用的默认地址。

`ethers.utils.parseEther`是一个将值从 ETH 转换为 WEI 的实用函数，这是我们在调用 payable 方法`save`时要发送给合同的值。

来自契约的响应由 EthersJs 以迭代器的形式返回，通过执行`savings.value().next().value`，我们能够以数组的形式访问值。

最后，使用`chai`中的`expect`进行断言。

## 提取存款

对于下面的测试，我们首先进行保存，然后进行提取，最后调用`getSavings`方法来断言金额被成功提取。

这就是我们测试用例的全部内容，但是对于不愉快的路径可以做更多的断言，比如试图在日期过去之前撤回或者为已经过去的日期保存。

作为参考，我在 GithubGist 上托管了两个文件，分别是[智能合约](https://gist.github.com/gracelungu/d9c7e6ed4c1749dc975ee0451241675f)和[测试](https://gist.github.com/gracelungu/4a0e1a2fea90c3677fe41fcb974795c0)。