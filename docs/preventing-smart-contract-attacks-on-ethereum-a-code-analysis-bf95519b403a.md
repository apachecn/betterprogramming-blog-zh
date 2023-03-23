# 防止以太坊上的智能合约攻击——重入攻击

> 原文：<https://betterprogramming.pub/preventing-smart-contract-attacks-on-ethereum-a-code-analysis-bf95519b403a>

## 让我们编写一个易受攻击的智能合约代码，看看攻击是如何工作的，并了解修复它的预防技术

![](img/cb4d481c7659914f51d0eb6d1dae219a.png)

来源: [Undraw](https://undraw.co/)

以太坊智能合约的一个特性是它们能够调用和利用来自其他外部合约的代码。

契约通常也处理以太网，因此经常将以太网发送到各种外部用户地址。这些操作要求合同提交外部调用。这些外部调用可能会被攻击者劫持，攻击者可以强制契约执行进一步的代码(通过回退功能)，包括对自身的调用。

这种攻击在臭名昭著的[道黑](http://bit.ly/2DamSZT)中使用过。

# 了解漏洞

当合同向未知地址发送以太网时，可能会发生这种类型的攻击。攻击者可以在回退函数中包含恶意代码的外部地址处精心构造一个协定。

因此，当一个契约向这个地址发送以太网时，它将调用恶意代码。通常，恶意代码对易受攻击的协定执行功能，执行开发人员不期望的操作。

术语“可重入性”来源于这样一个事实，即外部恶意契约调用易受攻击契约上的函数，代码执行的路径“重入”它。

为了澄清这一点，考虑一下`EtherStore.sol`中的简单易受攻击合同，它充当了一个以太坊金库，允许储户每周只提取 1 个以太:

以太商店. sol

这个契约有两个公共函数，`depositFunds`和`withdrawFunds`。

`depositFunds`功能只是增加发送者的余额。

`withdrawFunds`功能允许汇款人指定提取的`wei`金额。

只有当要求提取的金额小于 1 乙醚且上周未发生提取时，此功能才会成功。

漏洞在第 17 行，合同在这里向用户发送他们请求的乙醚量。

考虑在`Attack.sol`中创建了合同的攻击者:

Attack.sol

该漏洞是如何发生的？

首先，攻击者将使用`EtherStore`的契约地址作为唯一的构造函数参数来创建恶意契约(假设在地址`0x0… 123`)。

这将初始化并将公共变量`etherStore`指向要攻击的契约。

攻击者随后会调用`attackEtherStore`函数，其中一些以太量大于或等于 1——我们暂时假设 1 个以太。

在本例中，我们还将假设许多其他用户已将乙醚存入此合同，因此其当前余额为 10 乙醚。然后会发生以下情况:

1.  `Attack.sol`的第 15 行:`EtherStore`合约的`depositFunds`函数会用 1 乙醚(和大量气体)的`msg.value`调用。发送方(`msg.sender`)将是恶意契约(`0x0… 123`)。由此，`balances[0x0..123] = 1 ether`。
2.  `Attack.sol`的第 17 行:恶意契约然后会调用`EtherStore`契约的`withdrawFunds`函数，参数为 1 ether。这将通过所有要求(`EtherStore`合同的第 12-16 行),因为之前没有提款。
3.  `EtherStore.sol`的第 17 行:契约将 1 以太送回恶意契约。
4.  `Attack.sol`的第 25 行:对恶意契约的支付随后会执行回退功能。
5.  `Attack.sol`的第 26 行:`EtherStore`合约的总余额是 10 以太，现在是 9 以太，所以这个 if 语句通过。
6.  `Attack.sol`的第 27 行:fallback 函数再次调用`EtherStore` `withdrawFunds`函数，并‘重新输入’契约`EtherStore`。
7.  `EtherStore.sol`的第 11 行:在对`withdrawFunds`的第二次调用中，攻击合同的余额仍为 1 以太，因为第 18 行尚未执行。因此，我们还有`balances[0x0..123] = 1 ether`。对于`lastWithdrawTime`变量也是如此。同样，我们通过了所有的要求。
8.  `EtherStore.sol`的第 17 行:攻击契约再撤回 1 以太。
9.  重复步骤 4-8，直到`EtherStore.balance > 1`不再出现，如`Attack.sol`中第 26 行所示。
10.  `Attack.sol`的第 26 行:一旦`EtherStore`契约还剩 1(或更少)个以太，这个 if 语句就失效了。这将允许执行`EtherStore`契约的第 18 行和第 19 行(对于每个对`withdrawFunds`函数的调用)。
11.  `EtherStore.sol`，第 18 和 19 行:余额和`lastWithdrawTime`映射将被设置，执行将结束。

最终结果是攻击者在一次交易中从`EtherStore`合同中提取了除 1 个以太网之外的所有以太网。

# 预防技术

有许多常见的技术有助于避免智能契约中潜在的可重入性漏洞。

第一个是(只要有可能)在向外部契约发送 ether 时使用内置的 [transfer](http://bit.ly/2Ogvnng) 函数。传递函数只随外部调用发送 2300 gas，不足以让目的地址/契约调用另一个契约(即重新输入发送契约)。

第二种技术是确保所有改变状态变量的逻辑在以太被送出契约(或任何外部调用)之前发生。在`EtherStore`示例中，`EtherStore.sol`的第 18 行和第 19 行应放在第 17 行之前。

对于任何执行对未知地址的外部调用的代码来说，最好将它作为本地化函数或代码执行中的最后一个操作。这就是所谓的[检查-效果-交互模式](http://bit.ly/2EVo70v)。

第三种技术是引入互斥体——即添加一个状态变量，在代码执行期间锁定契约，防止可重入调用。

对`EtherStore.sol`应用所有这些技术(使用所有这三种技术是不必要的，但是我们这样做是为了演示的目的),给出了可重入自由契约:

以太商店. sol

# 故事时间

DAO(去中心化自治组织)攻击是以太坊早期发展中发生的主要黑客攻击之一。

当时，该合同价值超过 1.5 亿美元。重入性在攻击中发挥了主要作用，最终导致了创造以太坊经典(ETC)的硬分叉。为了更好地分析 DAO 漏洞，请查看这个博客。

更多关于以太坊的分叉历史，DAO hack 时间线，以及 ETC 在硬分叉中的诞生的信息可以在(`[ethereum_standards](https://github.com/ethereumbook/ethereumbook/blob/develop/09smart-contracts-security.asciidoc#ethereum_standards)).`