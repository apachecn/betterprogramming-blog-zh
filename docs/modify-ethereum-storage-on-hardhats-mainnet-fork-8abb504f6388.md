# 修改 Hardhat 的“mainnet”分支上的以太坊存储

> 原文：<https://betterprogramming.pub/modify-ethereum-storage-on-hardhats-mainnet-fork-8abb504f6388>

## 分析了如何在以太坊状态中为不同类型的变量找到插槽以及如何读取和修改它们的值的例子

![](img/acae319cf7c7e0a104503e82e97dff93.png)

Hardhat 有一个很酷的功能，可以用`hardhat_setStorageAt`手动设置任何存储槽的值。这个特性对于怀特·哈茨在以太坊`mainnet`上演示一个有效的利用是有用的，不会造成真正的破坏。分叉`mainnet`的能力对于集成测试的开发人员也很有用:模拟可能不会考虑到`mainnet`中真实契约的所有特性。

在本教程中，我们将建立一个 Hardhat `mainnet` fork，并通过几个例子来了解如何在 fork 上找到和修改真实契约中的存储变量。我们将讨论不同类型的变量，包括简单整数、打包值、映射和数组。

# 设置 mainnet 分支

首先你需要安装一个安全帽。查看本教程，了解如何安装 Hardhat 并创建您的第一个项目:

*   [https://hardhat.org/tutorial/setting-up-the-environment](https://hardhat.org/tutorial/setting-up-the-environment)

简而言之，你需要跑步:

```
$ mkdir modify-storage-tutorial
$ cd modify-storage-tutorial$ npm init -y
$ npm install dotenv hardhat [@nomiclabs/hardhat-ethers](http://twitter.com/nomiclabs/hardhat-ethers) [@nomiclabs/hardhat-waffle](http://twitter.com/nomiclabs/hardhat-waffle) ethereum-waffle ethers chai
```

在简单模式下，Hardhat 在你的电脑上模拟区块链。在 fork 模式下，它会将您的请求重定向到一个具有真实区块链快照的服务器。例如，这样的 API 由 alchemy.com 的[和 quicknode.com 的](https://www.alchemyapi.io/)和[提供。](https://quicknode.com/)

你可以查看他们关于如何叉以太坊`mainnet`的教程:

*   [https://docs . alchemy . com/alchemy/guides/how-to-fork-ether eum-mainnet](https://docs.alchemy.com/alchemy/guides/how-to-fork-ethereum-mainnet)
*   [https://www . quick node . com/guides/web 3-sdks/how-to-fork-ether eum-mainnet-with-hard hat](https://www.quicknode.com/guides/web3-sdks/how-to-fork-ethereum-mainnet-with-hardhat)

在本教程中，我们将使用炼金术 API。您必须访问[https://www . alchemyapi . io](https://www.alchemyapi.io/)，注册并在其仪表盘中创建新应用。在那里，您将获得配置 Hardhat 所需的 API 密钥。将它放在`.env`文件中，不要忘记将文件名添加到`.gitignore`中，因为这个密钥是一个秘密:

```
echo 'ALCHEMY_API_KEY=XXXXXXXXXX' >> .env
echo '.env' >> .gitignore
```

现在创建一个`hardhat.config.js`:

配置文件

最后，您可以检查一切是否正常:

```
$ npx hardhat test
```

# 如何修改单个槽变量

## 更改 Tether USD 合同所有人地址

USDT 智能合同有一个公共变量`[address owner](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#readContract)`。让我们找到它的插槽，并将其更改为我们的签名者地址。一旦完成，我们将能够运行一些特权方法，如增加总供应量。

首先，我们添加一个与 USDT 通信的接口。该接口依赖于 IERC20，因此我们需要安装 Openzeppelin 合同:

```
$ npm install @openzeppelin/contracts
```

现在添加一个`contracts/IUSDT.sol`文件:

```
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";interface IUSDT is IERC20 {
    function getOwner() external view returns (address); function issue(uint256) external;
}
```

第一个猜测是`owner`变量位于零槽。这似乎是真的！

test/ChangeUSDTOwner.js

您可以运行测试来查看它是否通过:

```
$ npx hardhat test test/ChangeUSDTOwner.js
```

# 薄荷 USDT

现在我们是所有者，我们可以铸造额外的代币:

```
const { expect } = require("chai");
const { ethers } = require("hardhat");const usdtAddress = "0xdac17f958d2ee523a2206206994597c13d831ec7"// the slot must be a hex string stripped of leading zeros! no padding!
// [https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network](https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network)
const ownerSlot = "0x0"it("Mint USDT", async function () {
    const usdt = await ethers.getContractAt("IUSDT", usdtAddress);
    const [signer] = await ethers.getSigners();
    const signerAddress = await signer.getAddress(); // storage value must be a 32 bytes long padded with leading zeros hex string
    const value = ethers.utils.hexlify(ethers.utils.zeroPad(signerAddress, 32)) await ethers.provider.send("hardhat_setStorageAt", [usdtAddress, ownerSlot, value]) expect(await usdt.getOwner()).to.be.eq(signerAddress) const amount = 1000
    const before = await usdt.totalSupply()
    await usdt.issue(1000)
    const after = await usdt.totalSupply() expect(after - before).to.be.eq(amount)
})
```

运行测试以查看它是否通过:

```
$ npx hardhat test test/MintUSDT.js
```

# 如何修改映射

## 更改 USDC 用户余额

现在让我们在 [USDC 智能合约](https://etherscan.io/token/0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48)中更改一个用户余额。

用户余额存储在`mapping(address => uint) balanceOf`变量中。

我们可以通过`hardhat_setStorageAt`直接编辑平衡，但首先我们需要找到正确的插槽。这有点棘手。您可以在[https://docs . solidy lang . org/en/latest/internals/layout _ in _ storage . html # mappings-and-dynamic-arrays](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html#mappings-and-dynamic-arrays)中查看映射是如何存储在以太坊存储中的

基本上，用户余额存储在插槽中:

```
keccak256(padZeros(userAddress).mappingSlot)
```

用 javascript 来说就是:

```
function getSlot(userAddress, mappingSlot) {
    return ethers.utils.solidityKeccak256(
        ["uint256", "uint256"],
        [userAddress, mappingSlot]
    )
}
```

那么我们怎么知道`mappingSlot`？那是`balanceOf`变量的槽？我们会用暴力解决它。你可以在[https://blog . Euler . finance/brute-force-storage-layout-discovery-in-ERC 20-contracts-with-hard hat-7ff 9342143 ed](https://blog.euler.finance/brute-force-storage-layout-discovery-in-erc20-contracts-with-hardhat-7ff9342143ed)中看到一个例子

我们将通过一个简单的检查来强制它:

```
async function checkSlot(erc20, mappingSlot) {
    const contractAddress = erc20.address
    const userAddress = ethers.constants.AddressZero // the slot must be a hex string stripped of leading zeros! no padding!
    // [https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network](https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network)
    const balanceSlot = getSlot(userAddress, mappingSlot) // storage value must be a 32 bytes long padded with leading zeros hex string
    const value = 0xDEADBEEF
    const storageValue = ethers.utils.hexlify(ethers.utils.zeroPad(value, 32)) await ethers.provider.send(
        "hardhat_setStorageAt",
        [
            contractAddress,
            balanceSlot,
            storageValue
        ]
    )
    return await erc20.balanceOf(userAddress) == value
}
```

这是暴力方法:

```
async function findBalanceSlot(erc20) {
    const snapshot = await network.provider.send("evm_snapshot")
    for (let slotNumber = 0; slotNumber < 100; slotNumber++) {
        try {
            if (await checkSlot(erc20, slotNumber)) {
                await ethers.provider.send("evm_revert", [snapshot])
                return slotNumber
            }
        } catch { }
        await ethers.provider.send("evm_revert", [snapshot])
    }
}
```

需要`try..catch`和`evm_revert`是因为随机存储修改可能会破坏契约并导致异常。

现在，我们可以编写一个最终测试来检查我们是否可以在 USDC 契约中找到并修改用户余额:

```
it("Change USDC user balance", async function() {
    const usdc = await ethers.getContractAt("IERC20", usdcAddress)
    const [signer] = await ethers.getSigners()
    const signerAddress = await signer.getAddress()

    // automatically find mapping slot
    const mappingSlot = await findBalanceSlot(usdc)
    console.log("Found USDC.balanceOf slot: ", mappingSlot) // calculate balanceOf[signerAddress] slot
    const signerBalanceSlot = getSlot(signerAddress, mappingSlot)

    // set it to the value
    const value = 123456789
    await ethers.provider.send(
        "hardhat_setStorageAt",
        [
            usdc.address,
            signerBalanceSlot,
            ethers.utils.hexlify(ethers.utils.zeroPad(value, 32))
        ]
    ) // check that the user balance is equal to the expected value
    expect(await usdc.balanceOf(signerAddress)).to.be.eq(value)
})
```

运行测试以查看它是否通过:

```
npx hardhat test test/ChangeBalanceOf.js
```

# 如何修改数组

## 修改 Aave LendingPoolAddressesProviderRegistry

让我们分析一个简单的例子，如何在 Aave 的[LendingPoolAddressesProviderRegistry](https://docs.aave.com/developers/v/2.0/the-core-protocol/addresses-provider-registry)中找到、读取和修改私有动态`address`数组，该数组存储在[0x 52d 306 e 36 e 3 b 6 b 02 c 153d 0266 ff 0 f 85d 18 BCD 413](https://etherscan.io/address/0x52D306e36E3B6B02c153d0266ff0f85d18BCD413#code)。

首先我们需要知道一个`address`数组是如何在以太坊状态下存储的:

1.  诸如`private`、`public`或`internal`的可见度修改符不影响存储机制
2.  对于一个`address`动态数组，变量的槽`p`存储元素的数量。例如，如果数组中有两个元素，那么槽`p`存储`0x02`。
3.  相应的两个元素从`keccak256(p)`开始存储。
4.  即使`address`类型是 20 字节长——每个数组元素仍然存储在一个单独的 32 字节槽中。因此，数组的第一个元素将位于槽位`keccak256(p) + 0`，第二个元素将位于`keccak256(p) + 1`。

如果你对其他类型的数组的存储方式感兴趣，请阅读[https://docs . soliditylang . org/en/latest/internals/layout _ in _ storage . html # mappings-and-dynamic-arrays](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html#mappings-and-dynamic-arrays)

`LendingPoolAddressesProviderRegistry`的代码在[https://github . com/aave/protocol-v2/blob/master/contracts/protocol/configuration/lendingpooladdressesproviderregistry . sol](https://github.com/aave/protocol-v2/blob/master/contracts/protocol/configuration/LendingPoolAddressesProviderRegistry.sol)

我们对这部分感兴趣:

```
contract LendingPoolAddressesProviderRegistry is ... {
  mapping(address => uint256) private _addressesProviders;
  address[] private _addressesProvidersList;

  ... function getAddressesProvidersList() 
    external 
    view 
    returns (address[] memory) 
  { ... } function getAddressesProviderIdByAddress(
    address addressesProvider
  ) 
    external 
    view
    returns (uint256)
  { ... } ...
```

我们想找到`_addressesProvidersList`插槽。首先让我们通过调用`getAddressesProvidersList`方法来检查它的内容。为此，我们需要向我们的项目添加一个`LendingPoolAddressesProviderRegistry`接口:

```
interface ILendingPoolAddressesProviderRegistry {
    function getAddressesProvidersList() external view returns (address[] memory); function getAddressesProviderIdByAddress(address addressesProvider) external view returns (uint256);
}
```

现在我们可以在 Hardhat 的控制台中运行它。使用以下命令运行控制台

```
$ npx hardhat console
```

然后运行 javascript 代码:

```
const target = await ethers.getContractAt("ILendingPoolAddressesProviderRegistry", "0x52D306e36E3B6B02c153d0266ff0f85d18BCD413")await target.getAddressesProvidersList()
```

输出:

```
[
  '0xB53C1a33016B2DC2fF3653530bfF1848a515c8c5',
  '0xAcc030EF66f9dFEAE9CbB0cd1B25654b82cFA8d5'
]
```

所以这个数组有两个元素。现在我们知道了`_addressesProvidersList`的槽存储了值`0x02`。让我们阅读前几个槽来寻找值:

```
await ethers.provider.getStorageAt(target.address, "0x0")
await ethers.provider.getStorageAt(target.address, "0x1")
await ethers.provider.getStorageAt(target.address, "0x2")
```

输出:

```
0x000000000000000000000000b9062896ec3a615a4e4444df183f0531a77218ae
0x0000000000000000000000000000000000000000000000000000000000000000
0x0000000000000000000000000000000000000000000000000000000000000002
```

让我们分析一下存储布局:

*   `slot 0`被我们范围之外的变量使用。
*   `slot 1`似乎被映射`_addressesProviders`使用，因为映射槽不存储元素，它总是零。
*   `slot 2`存储`0x02`，似乎是`_addressesProvidersList`的插槽！

让我们将`slot 2`的值改为`0x03`，这样数组`_addressesProvidersList`将有 3 个元素:

```
await ethers.provider.send(
  "hardhat_setStorageAt", [
    target.address,     // the slot must be a hex string stripped of leading zeros! no padding!
    // [https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network](https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network)
    "0x2", // storage value must be a 32 bytes long padded with leading zeros hex string
    ethers.utils.hexlify(ethers.utils.zeroPad(3, 32))
  ]
)
```

现在让我们调用`getAddressesProvidersList`来看看这是否有效:

```
await target.getAddressesProvidersList()
```

输出:

```
[
  '0xB53C1a33016B2DC2fF3653530bfF1848a515c8c5',
  '0xAcc030EF66f9dFEAE9CbB0cd1B25654b82cFA8d5',
  '0x0000000000000000000000000000000000000000'
]
```

成功了！现在让我们将数组的第三个元素设置为`0xDEADBEEF`:

```
const arraySlot = ethers.BigNumber.from(ethers.utils.solidityKeccak256(["uint256"], [2]))
const elementSlot = arraySlot.add(2).toHexString()
const value = "0xDEADBEEF"
const value32 = ethers.utils.hexlify(ethers.utils.zeroPad(value, 32))await ethers.provider.send(
  "hardhat_setStorageAt", [
    target.address, 
    elementSlot, 
    value32,
  ])
```

现在，如果我们再次运行`getAddressesProvidersList`，我们将得到:

```
[
  '0xB53C1a33016B2DC2fF3653530bfF1848a515c8c5',
  '0xAcc030EF66f9dFEAE9CbB0cd1B25654b82cFA8d5',
  '0x0000000000000000000000000000000000000000'
]
```

但是为什么呢？我们不是改了第三要素吗？原因在于`getAddressesProvidersList`的工作方式。它只输出存储在映射`_addressesProviders`中的数组元素。请参见位于[https://github . com/aave/protocol-v2/blob/master/contracts/protocol/configuration/lendingpooladdressesprovideregistry . sol # L33](https://github.com/aave/protocol-v2/blob/master/contracts/protocol/configuration/LendingPoolAddressesProviderRegistry.sol#L33)的代码:

```
for (uint256 i = 0; i < maxLength; i++) {
  if (_addressesProviders[addressesProvidersList[i]] > 0) {
    activeProviders[i] = addressesProvidersList[i];
  }
}return activeProviders;
```

幸运的是，我们已经知道了`_addressesProviders`映射的槽:它是`slot 1`。我们可以直接将我们的`0xDEADBEEF`添加到`_addressesProviders`中:

```
const deadBeefSlot = ethers.utils.solidityKeccak256(
  ["uint256", "uint256"],
  [0xDEADBEEF, 1]
)
await ethers.provider.send(
  "hardhat_setStorageAt",
  [
    target.address,
    deadBeefSlot,
    ethers.utils.hexlify(ethers.utils.zeroPad(1, 32))
  ]
)
```

让我们再次检查我们的数组:

```
await target.getAddressesProvidersList()
```

输出:

```
[
  '0xB53C1a33016B2DC2fF3653530bfF1848a515c8c5',
  '0xAcc030EF66f9dFEAE9CbB0cd1B25654b82cFA8d5',
  '0x00000000000000000000000000000000DeaDBeef'
]
```

太好了！值`0x00000000000000000000000000000000DeaDBeef`存储为`_addressesProvidersList`数组的第三个元素。

您可以运行完整的脚本，如下所示:

```
$ npx hardhat run scripts/ChangeAaveAddressProviderList.js
```

# с结束语

在本文中，我们分析了几个例子，如何在以太坊状态中为不同类型的变量找到槽，如何读取和修改它们的值。我们研究了如何在 USDT、USDC 和 Aave 等合同中修改`public address`、`public mapping(address => uint)`和`private address[]`。

这种技巧一定会帮助你准备和展示工作的成功。如果你不是 whitehat，而是一名开发人员，那么这肯定会帮助你编写集成测试。来自 [mixbytes](https://mixbytes.io) 团队的材料。

*祝你好运！*

# 链接

*   [Euler:与 Hardhat 签订的 ERC20 合同中的强力存储布局发现](https://blog.euler.finance/brute-force-storage-layout-discovery-in-erc20-contracts-with-hardhat-7ff9342143ed)
*   [安全帽:设置环境](https://hardhat.org/tutorial/setting-up-the-environment)
*   [安全帽:分流其他网络](https://hardhat.org/hardhat-network/docs/guides/forking-other-networks)
*   [炼金术:如何叉以太坊 Mainnet](https://docs.alchemy.com/alchemy/guides/how-to-fork-ethereum-mainnet)
*   [Quicknode:如何用 Hardhat 分叉以太坊 Mainnet](https://www.quicknode.com/guides/web3-sdks/how-to-fork-ethereum-mainnet-with-hardhat)
*   [无法在安全帽网络上设置存储槽](https://ethereum.stackexchange.com/questions/129645/not-able-to-set-storage-slot-on-hardhat-network)
*   [可靠性:状态变量在存储器中的布局](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html)
*   [Aave:地址提供商注册中心](https://docs.aave.com/developers/v/2.0/the-core-protocol/addresses-provider-registry)