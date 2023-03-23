# 用 Python 和 Infura 编程创建一个以太坊钱包

> 原文：<https://betterprogramming.pub/programmatically-create-an-ethereum-wallet-with-python-and-infura-c378dda3e19c>

## 存储加密货币的指南

![](img/b3ff0a8bdf18fe81cea65e7d27a87f88.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

作为一名 Python 开发者，找到关于如何创建以太坊钱包的教程或文章并不容易。所以，我花了几个小时想出了这个程序，现在你不用想了。

如果你选择继续读下去，你会在下面找到十行代码的解决方案。

在本教程中，我将介绍如何为以太坊和其他 EVM 链创建一个唯一的公钥-私钥对。我将使用 [BIP-39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 助记符、web3.py 和 [Infura](https://infura.io/) 来实现这一点。

**注意**:你可以使用任何提供者，比如[道德](https://moralis.io/)或者[炼金术](https://www.alchemy.com/)来达到同样的效果。把它换成 Infura 就行了。

这段代码与 Python 3.8+兼容，并且已经过测试，可以与 Django 4.0 一起使用。

在我们开始之前，您需要在 [Infura.io](https://infura.io/) 上创建一个帐户。一旦完成并验证了您的信息，您将看到您的密钥和您的区块链端点。我们将在项目的稍后阶段回到这些。

让我们安装助记库和 web3.py 库:

```
pip install mnemonic web3
```

上面的库安装了你生成助记单词表所需的依赖项，该列表用于使用 [BIP-39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 标准生成你的私钥。您还需要 web3.py 库来与区块链进行交互。

1.  创建新文件并导入所需的库:

```
from mnemonic import Mnemonic
from web3 import Web3
```

2.初始化助记符类:

```
mnemo = Mnemonic("english")
words = mnemo.generate(strength=256)
seed = mnemo.to_seed(words, passphrase="")
```

让我们花点时间来理解代码。首先，我们初始化`mnemo`类。然后，我们使用`mnemo.generate(strength=256).`生成短语。最后，我们使用上一步生成的单词生成`seed`。您可以选择提供额外的`passphrase`，以获得额外的安全性。

您现在已经为以太坊链生成了一个唯一的私钥。现在让我们从上面的密钥生成我们的公共地址。

3.初始化 web3.py:

```
MAIN_NET_HTTP_ENDPOINT = "<add your mainnet endpoint URL here>"
w3 = Web3(Web3.HTTPProvider(MAIN_NET_HTTP_ENDPOINT))
```

从 [Infura](https://infura.io/) 中获取您的端点 URL，并将其设置为上面的`MAIN_NET_HTTP_ENDPOINT`。然后，用端点初始化一个名为`w3`的类。

4.初始化帐户类:

```
account = w3.eth.account.privateKeyToAccount(seed[:32])
```

我们使用上面的`seed[:32]`,因为我们只需要使用种子的 32 位字符串作为我们的私钥。

5.获取您的公钥和私钥:

```
private_key = account.privateKey
public_key = account.address
```

就这样——你完成了！

是的，就是这么简单。不相信我？您可以打印`private_key`并使用 metamask 导入它。你也可以打印`words`，用它来恢复你的账户。

下面是全部代码，这样您可以更容易地复制:

```
from mnemonic import Mnemonic
from web3 import Web3
mnemo = Mnemonic("english")
words = mnemo.generate(strength=256)
seed = mnemo.to_seed(words, passphrase="")MAIN_NET_HTTP_ENDPOINT = "<add your mainnet endpoint URL here>"
w3 = Web3(Web3.HTTPProvider(MAIN_NET_HTTP_ENDPOINT))
account = w3.eth.account.privateKeyToAccount(seed[:32])
private_key = account.privateKey
public_key = account.address
```

感谢阅读！