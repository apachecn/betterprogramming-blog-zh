# 通货紧缩佣金令牌检测

> 原文：<https://betterprogramming.pub/deflationary-commission-tokens-detection-d71a5f451556>

## 帮助您检测非标准令牌的提示

![](img/8b5841a049da2df47950340f7be59eca.png)

图片来自 [Unsplash](https://unsplash.com/photos/71KzaWjdt84)

Defi 日益流行，如今有许多不同的令牌标准，如 ERC20、ERC721、ERC1155 等。然而，通货紧缩代币(或带佣金的代币)和所有其他代币(收件人收到的代币数量与发送的代币数量不同)没有其官方实现。

这给不同的 web3 开发人员带来了很多麻烦，因为如果不使用交易的后端模拟等，很难定义令牌是否是非标准的。这就是为什么许多开发者说这是一个骗局令牌(例如， [safemoon pull request](https://github.com/safemoonprotocol/Safemoon.sol/pull/33) 建议从 GitHub 删除所有代码)。在本文中，我们将讨论开发人员可以轻松检测这些类型的令牌的方法。

首先，让我们定义一个通货紧缩/佣金令牌的通用线。主要区别几乎总是位于`*_transfer*` 函数，因为`transfer`和`transferFrom`都使用它。

通货紧缩的标志是随着时间的推移减少供给的标志。因此，检查过去和现在的代币总量将是一个好主意。如果数额减少了——很可能，这是一个通货紧缩的象征。但是这种方法还是不太理想。管理员可以烧毁来自黑客(黑名单中的地址)的令牌，或者只是烧毁一些令牌，以减少总供应量并增加令牌排放量。所以这种方法是好的，但它是不准确的，因为有佣金的代币不会减少总供应量。

佣金令牌(按照我的定义)将交易中的一些令牌发送到特定的地址。可以是主人的地址，也可以只是零地址。从我开发者的角度来看，这种令牌和上一种是一样的。在这种情况下，总供应量不会减少，如果令牌不是 ERC20 标准，就很难理解，如果不开放源代码，几乎不可能理解费用的百分比。

# 那么，如何检测这些代币呢？

从 [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4) 开始，有一种方便又省油的方式向用户解释为什么一个操作因为自定义错误而失败。早些时候，您已经可以使用字符串来给出关于失败的更多信息(例如 revert(“资金不足”)，但是很难在其中使用动态信息。因此，使用[自定义错误](https://blog.soliditylang.org/2021/04/21/custom-errors/)，我们可以轻松记录发送和接收的数量，并对它们进行比较。从前端来看，开发人员唯一要做的事情就是对契约进行静态调用，并解码错误数据。

首先，我们需要定义错误选择器。例如，如果我们有以下错误:

```
error AmntReceived_AmntExpected_Transfer(uint256 amountReceived, uint256 amountExpected);
```

通过使用 Solidity，我们可以很容易地找到这个选择器。但是不要忘记删除空格和变量名称:

```
bytes4 public constant FUNC_SELECTOR = bytes4(keccak256(‘AmntReceived_AmntExpected_Transfer(uint256,uint256)’));
```

是`0x5afe9b48`。

或者可以使用 TypeScript/JavaScript 中的 [ethers.utils.keccak256 函数](https://docs.ethers.io/v5/api/utils/hashing/)来解码这个选择器。

最简单的方法是将这个错误作为一个函数添加到 abi 中。

```
const abi = [“function error AmntReceived_AmntExpected_Transfer(uint256 amountReceived, uint256 amountExpected);”];
```

然后用乙醚解码数据。下面是如何做到这一点:

```
const interface = new ethers.utils.Interface(abi);const decoded = interface.decodeFunctionData(interface.functions[“AmntReceived_AmntExpected_Transfer(uint256,uint256)”], error_data);
```

因此，当我们了解如何处理自定义错误时，我们需要创建一个智能契约，它将发出正确的日志，并为我们提供以下信息:

*   简单传递函数中的费用。一些代币不同的金额有不同的费用。
*   dex 中的购买费用功能，因为许多 safemoon forks 对路由器有不同的费用百分比。
*   以指数表示的卖出函数费用。

我们面临的主要问题是开发一个模拟器智能合约——失败的外部调用。我们希望只恢复已知的自定义错误。

1.  简单的代币转让合同。由于我们正在使用`transferFrom`功能- >合同必须经过批准。由于容差问题和`msg.sender`地址上必须有令牌的问题，这个函数不可用。但是在开发静态调用逻辑时，这对于测试来说还是可以的。或者如果你是铁杆，想创建一个通货紧缩令牌的大数据库。只需购买少量的每种代币，对每种代币进行审批，然后使用它。

静态调用后的错误日志将是:

```
error AmntReceived_AmntExpected_Transfer(uint256 amountReceived, uint256 amountExpected);
```

2.将代币换成有费用的代币，然后转回`msg.sender`。您可以将任何令牌交换为您想要在 DEX 上检查的令牌，方法是在交换数据—模拟器合同地址中创建接收方地址。之后，合同会将代币转让回`msg.sender`并记录佣金。

因为你不会真的交换令牌，你的静态调用将恢复，建议设置 100%的滑动和最大 uint 的最后期限。建议你从地址零开始建立这个静态调用的交换数据，`tokenIn`应该是原生币。由于零地址是一个经典的烧毁地址，它总是有足够的硬币余额，几乎任何 EVM 区块链呼叫，和本地硬币不需要津贴转移。例如，您可以通过一个 1 英寸的 API 请求接收交换数据。

静态调用后的错误日志将是:

```
error AmntReceived_AmntExpected_TransferSwap(uint256 amountReceived, uint256 amountExpected);
```

这两种方法检测收费的非标准代币绰绰有余。使用此代码，您可以轻松获得一定比例的费用:

```
feePercent = amountReceived/amountExpected.
```

但是由于 safemoon 使用自定义费用进行买卖，上面指出的函数的日志是不够的。我不会详细描述如何做到这一点，因为它变得更加复杂，而且这些信息对大多数项目都没有用。但是如果你有兴趣，你可以在我的 GitHub 上找到源代码[。](https://github.com/grGred/Commission-Simulator/blob/master/contracts/Simulator.sol)

使用错误数据时请小心，因为错误数据的来源无法追踪。默认情况下，错误数据在外部调用链中冒泡，这意味着协定可能会转发一个它直接调用的任何协定中都没有定义的错误。

此外，任何契约都可以通过返回与错误签名匹配的数据来伪造任何错误，即使错误没有在任何地方定义。