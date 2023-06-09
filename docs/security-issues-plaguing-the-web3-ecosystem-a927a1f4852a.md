# 困扰 Web3 生态系统的安全问题

> 原文：<https://betterprogramming.pub/security-issues-plaguing-the-web3-ecosystem-a927a1f4852a>

## 深入研究这些错误及其最佳预防技术

![](img/13b9d07af03e62268bb05db3d842976d.png)

在 Web3 世界中，安全漏洞造成的损失可能是巨大的。我的意思是，由于几乎每次涉及的金额巨大，当财务损失被公开时，总是很疯狂。虽然这充分说明了区块链技术的广泛采用，但也暴露了它的脆弱性。该技术建立在不变性、分散性和透明性的原则之上，但多年来已经多次证明易受攻击。与流行的观点相反，这些攻击大多并不复杂，而是智能合约中的错误造成的。这些攻击大多是针对智能合约的，要么是通过寻找漏洞，要么是利用 solidity 代码中的错误。在撰写智能合同时，无论错误的大小或复杂程度如何，都可能是致命的，因此，安全性是初露头角的区块链工程师面临的一个巨大课题。与 Web2 行业不同，在 web 2 行业中，错误很可能导致应用程序崩溃，而 Web3 中的错误往往会带来更具破坏性的财务后果。

有很多涉及 Web3 生态系统的安全问题和实例，但我们应该回顾一下第一个获得巨大关注的问题，那就是著名的 DAO 攻击。DAO 是一个在以太坊区块链建立起来的风险投资基金。DAO 是分散自治组织的首字母缩写，顾名思义，这个组织的事务不是由一个单独的实体控制和支配的。其目标是创建一个透明和分散的平台，投资者可以为商业和非营利企业汇集资金。它试图通过将权力直接授予每个投资者来消除单一实体管理基金的问题，这是现有风险资本的一个普遍问题。投资者获得投票权，他们在决定投资项目时行使投票权。正如你所期望的那样，一个支持区块链的组织的一切都是透明的。执行其规则和条例的准则向所有人公开，这反过来导致了一个系统，在这个系统中，每个人都清楚地知道正在发生的一切。这在当时是革命性的，是一个巨大的启示，影响了无数后来的项目，但正如新项目通常的情况一样，总会有差距和漏洞，随着时间的推移，这些差距和漏洞肯定会得到纠正。我可以向你保证，今天脸书、Twitter 和其他大型 Web2 公司的情况就是如此。错误只会导致他们的平台崩溃，而他们的工程师会跑一场马拉松来让一切恢复正常运行。不幸的是，这不是 Web3 公司能负担得起的奢侈品。当这一不幸事件发生时，当时价值约 5000 万美元的 360 万乙醚被攻击者非法转移给了一个孩子 DAO。在这个时间点上，以太坊平台相对来说还处于上升期，道包含了大约 14%的流通以太坊。攻击者通过在合同中发现一个*分裂函数，使他能够将资金虹吸到一个子 DAO，从而成功完成了抢劫。他递归地调用它，直到他聚集了母道中所有醚的三分之一。值得注意的是，父 DAO 有一个约束，该约束阻止乙醚在 28 天后被移动，因此子 DAO 继承了相同的约束，这使得攻击者不能立即访问资金。这种情况为以太坊社区赢得了时间来决定采取正确的行动来反击这种攻击。由于这次攻击的规模和对平台的影响，以太坊的创始人 Vitalik Butherin 认为平台有必要参与进来。提出了两种选择，第一种是软分叉，第二种是以太坊网络的硬分叉。软分叉基本上包括添加代码片段，有效地将攻击者列入黑名单，从而禁止他移动被盗的以太网。在基金会着手这项工作之前，在代码中发现了一个错误，导致该选项不起作用。因此，硬分叉似乎是最后的手段，这个过程涉及将以太坊网络反转到黑客攻击发生之前的某个时间，以便 DAO 中的资金可以安全地转移出去，并交还给投资者。这在区块链社区引起了轩然大波，并被认为是虚伪的，因为人们期望区块链代表的是不变性和去中心化。关于实现硬分叉的讨论引发了攻击者的回应，他写了一封信威胁以太坊基金会，并声称自己没有做错任何事情。他的信附在下面。*

*尽管遭到强烈反对，以太坊基金会仍继续努力，并在此过程中追回了被盗资金。这种硬分叉产生了一个新的以太坊网络，它经历了某种形式的改变，但旧的网络仍然活跃，因此被称为以太坊经典。一些区块链技术核心原则的坚定信仰者坚持使用原始网络，并决定不参与被认为受到污染的网络。几年后，尽管区块链核心社区一片哗然，但可以肯定地说，Vitalik 和他的团队做出了正确的决定。*

*在 DAO 攻击上花那么多时间绝对不是我的本意，因为我想尽可能多地强调 Web3 的安全缺陷，但考虑到攻击的影响和恶名，这是我们不得不面对的情况。说它的成功引发了未来会发生的类似攻击不会错。我们已经看到了对生态系统的未来有着最大冲击和影响的 Web3 攻击，现在是我们谈论有史以来损失超过 DAO 的最大抢劫的时候了。保利网络黑客事件发生在 2021 年 8 月，导致价值约 6.1 亿美元的加密货币损失。然而，在 Poly Network 发出呼吁，承诺提供 50 万美元作为寻找其系统漏洞的姿态，并提供首席安全顾问一职后，黑客归还了他的攻击所得。现在被称为“白帽子先生”的黑客声称，这次攻击只是为了揭示保利网络的漏洞，并帮助保护它。Poly 网络是一种允许不同区块链网络交互的协议。它促进了代币从一个分类帐到另一个分类帐的交换。从成立到遭到攻击，它已经成功地在几个区块链网络之间转移了价值 100 亿美元的数字资产。为了全面理解这一技巧，理解函数可见性的概念是至关重要的。solidity 中的函数可以将它们的可见性设置为私有、公共、内部或外部。当未指定任何内容时，它会自动声明为 public。那么，让我们更深入地了解一下可见性的各种形式。*

> *私有函数:私有函数只能在声明它们的契约中被调用。即使是继承的契约也不能调用这个函数。这种类型的函数可见性总是被推荐，因为它极大地降低了攻击和安全问题的风险。这种可见性禁止外部访问和修改存储在智能合同上的信息。*
> 
> *公共函数:顾名思义，这些函数是公共的，可以从任何地方调用。它们要么在内部调用，要么使用消息。*
> 
> *内部函数:由于它与私有函数的相似性，这在过去一直是一个混淆的主题。但两者的主要区别在于，与不能被派生契约继承的私有函数不同，内部函数可以。*
> 
> *外部函数:外部函数可以被其他契约和事务访问，但是不使用“this”就不能在内部调用，这就把它和它的公共对应物分开了。在部署之后，它们是契约接口的一部分，这种信息的生命力就是我为什么必须解释函数类型以使其被理解的原因。涉及外部函数的错误导致了生态系统的严重损失，因此工程师充分了解这些函数及其在智能合同中的实现非常重要。*

*通俗地说，外部函数可以在它的父契约之外调用，所以攻击者倾向于查找这样的函数是完全有道理的。早些时候，我提到过一些攻击之所以成功，并不是因为它们的复杂性，但是可以公平地说，Poly Network 漏洞利用已经非常复杂了。简而言之，攻击者能够调用`EthCrossChainManager`契约中的外部函数，这使得他们能够覆盖公钥。*

*上面代码片段中突出显示的外部函数触发了攻击者利用的一系列响应。他能够将公钥作为参数传递给他的以太坊钱包。这是在“EthCrossChainData”契约上完成的，由于该调用来自其父契约，该契约没有反对。这些公钥拥有批准转账和取款的特权，因此一旦攻击者添加了他的公钥，他就开始疯狂地向他的地址取款。下面是两个智能合同的完整可靠性代码的链接。*

> *[*ethcrossschainmanager . sol*](https://github.com/polynetwork/eth-contracts/blob/d16252b2b857eecf8e558bd3e1f3bb14cff30e9b/contracts/core/cross_chain_manager/logic/EthCrossChainManager.sol)T5[*ethcrossschaindata . sol*](https://github.com/polynetwork/eth-contracts/blob/master/contracts/core/cross_chain_manager/data/EthCrossChainData.sol)*

*为了确保这篇文章不会篇幅过长，我将不再详细讨论其他攻击。从好的方面来看，这些攻击的原因都是相似的，都源于相似的漏洞。这些漏洞源于常见的错误，使他们能够茁壮成长。一些同样流行的攻击的例子是虫洞攻击、Mt. Gox、Coincheck、KuCoin、Bitmart 等等。因此，我将继续深入研究这些错误及其最佳预防技术。*

# ****1。重返****

*这是智能合约中最具破坏性的攻击之一。这种攻击导致了 Web3 生态系统中的一些大规模盗窃，最著名的是 DAO 攻击。当对恶意合同进行外部调用时，就会发生这种攻击，这使得恶意合同能够对原始合同进行递归调用，其唯一目的是耗尽合同中存储的资金。尽管这一攻击具有基本性质，但其多年来的后果已被证明具有严重影响。在进行外部调用之后的状态改变已经被证明是在已经成为这种攻击的受害者的契约中再次出现。这使得攻击者能够重复调用取款功能，直到资金完全耗尽或达到一个堆栈。状态改变旨在停止资金提取并更新余额，因此，如果在提取功能后错误地设置了状态改变，则可以递归地调用提取功能，以确保这种状态改变不会受到影响。下面是一段代码，突出显示了外部调用后的状态变化。*

*如上所述的状态改变需要清空呼叫者的余额，并且在该过程中确保没有剩余资金可以提取。这样做是正确的，但是状态改变的位置使智能契约容易受到可重入攻击。因此，解决方案是确保在进行外部调用之前更新状态。*

*因此，我们做了必要的修改，以确保任何类型的重入攻击失败。*

# ***2*。整数下溢和上溢****

*这是智能合约中可能导致安全风险的另一个问题。与其他缺陷不同，这一缺陷经常被忽视，但同样具有毁灭性。solidity 中的数字使用 UINT(无符号整数)声明。UINT 有不同的大小，例如 uint8、uint16、uint64 和 uint256。使用像 uint8 这样的较小的 uint 大小可以节省存储和处理成本，但也会使我们面临整数溢出的风险。一个无符号整数可以拥有的位数是由它的大小定义的，这基本上意味着我们可以在一个 uint8 中存储的最大数是 255。我们通过这样做得到这一点(^后书 8 - 1)。现在，如果我们在 uint8 中将 1 加到 255，将得到 0。简而言之，这是一个整数溢出。在下溢的情况下，从 0 减去 1 将返回 255，我认为现在可以清楚地看到为什么这可能是一个巨大的安全问题。这个问题我们来看一个智能合约。*

*从上面的代码中需要指出的一点是，我使用的 solidity 版本不同于我包含的其他代码片段。我不得不使用 v0.7.0 来说明这个安全问题，因为 v0.8.0 以上的版本已经避免了这个安全风险。因此，对这个问题最明显的预防技巧是使用最新版本的 Solidity，因为你没有什么可担心的。但是如果你喜欢旧版本的 Solidity，那么使用更大尺寸的 uint 将是正确的选择。同样重要的是要知道，当我们不指定无符号整数的大小时，uint256 是自动声明的。*

# ****3。*自毁***

*这是 Solidity 中的一个函数，它将一个地址作为输入。当调用这个函数时，它删除契约的字节码，并将契约中的整个以太存放到指定的地址。我们将会看到一个为这个漏洞提供空间的坚固性代码。这是从 solidity-by-example 网站上得到的，我将试着把它分解成最简单的部分。这份合同规定了区块链上一场比赛的规则和条例。在这个游戏中，有一个目标金额，玩家被要求只发送 1 乙醚到合同中。当一个玩家送出 1 个以太，而合约余额等于目标金额时，合约中的全部以太被撤回到他的地址。*

*在上面的代码片段中明确指出，玩家一次只能发送 1 个以太，希望让余额等于`targetAmount`。*

*但是使用`selfdestruct`功能，可以强制发送以太网到约定地址，导致余额超过`targetAmount`。这将导致合同中的奖励被永久锁定，因为没有对余额超过`targetAmount`的情况进行定义的处理，因为这种情况预计不会发生。*

*正如我前面提到的，`selfdestruct`函数删除了一个契约的字节码，并将契约中剩余的总以太提取到通过该函数传递的地址。*

*通过获取游戏的合同地址，攻击者只需通过`selfdestruct`函数来强制发送以太。*

*在 Solidity 中，只有可支付的地址被给予接收以太的特权，因此攻击者强制将游戏契约地址转换为可支付的。因此，在所有这些都到位后，执行攻击功能会向游戏契约发送以太，导致余额超过目标，然后使奖励永久无法获得。*

*就像其他攻击一样，我们可以采取措施确保不给攻击留下任何空间。但是，在探索其预防措施之前，了解使这种攻击成为可能的智能合约的漏洞是很重要的。*

*其脆弱性是误用`this.balance`的结果。因此，合同依赖于余额中包含的总价值。这导致了与合约平衡的直接互动，从而有利于操纵。*

*当调用`selfdestruct`函数时，以太被发送到约定地址，因此游戏中的平衡被实例化远离`this.balance`的情况，使得`selfdestruct`的动作无效。因此，基本上，发送到合同地址的醚不会增加余额，也不会影响游戏的结果。因此，攻击企图基本上意味着我们从世界上某个地方的仰慕者那里得到了免费的乙醚。*

# ****4。默认可见性****

*我们之前浏览过这个，但是没有完全深入。如我所说，与此相关的错误在过去已经成为灾难的原因。函数可见性可以设置为 public、private、external 或 internal，如果我们没有指定。这决定了一个函数是可以由用户从外部调用，由其他契约派生，还是严格地在内部调用。不正确地使用可见性会使函数面临外部未授权访问的威胁，这可能会给 Web3 项目带来厄运。当智能合约开发人员错误地忽略了应该是私有的函数的可见性说明符时，这个问题就出现了。我们将看到一份有这个缺陷的合同，以帮助我们更好地理解。*

*上面的合同旨在管理一个地址奖金猜测游戏，玩家必须生成一个以太坊地址，其中最后 8 个六边形为零才能获胜。从我们可以看到，上面的函数的可见性没有被指定，因此被设置为 public。因此，攻击者可以通过调用公共的`sendWinnings`函数来利用这个缺陷，窃取奖金。由这一错误导致的攻击的一个流行例子是第一次奇偶多重签名黑客攻击，导致价值 3100 万美元的以太网损失。养成总是指定函数可见性的习惯，即使它是一个公共函数，这是根除源于这种错误的攻击的第一步。此外，Solidity 的最新版本在编译没有指定可见性的函数时会显示警告。*

*看起来我们已经讨论了很多，但是相信我，我们仅仅触及了表面。Web3 生态系统的美妙之处在于它不时提醒你你一无所知的不可思议的能力。在我们的旅程中，有一个信息是显而易见的，那就是 Web3 中的安全问题是多么的灾难性。社区一直在探索和寻找新的方法来提高安全性，避免多年来普遍存在的重大损失。最好相信，尽管安全失败和损失，Web3 是一场革命，将改变一切变得更好。就像我常说的，现在还是早期阶段，现在是开始参与的最好时机。有各种各样的方式来参与 Web3 生态系统并为其做出贡献，除了随之而来的兴奋和激动之外，它还让我们有机会成为一些特殊事物的一部分。但是，尽管生态系统得到了广泛的采用、承诺和潜力，但不可否认的是，它受到了相当多的攻击，这些攻击留下了不可磨灭的痕迹，而且没有迹象表明我们已经看到了它们的终结。*