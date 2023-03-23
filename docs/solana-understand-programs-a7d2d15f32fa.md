# 索拉纳——理解程序

> 原文：<https://betterprogramming.pub/solana-understand-programs-a7d2d15f32fa>

## 深入了解窗帘后面的东西

![](img/569eba98f6e8bba13b73f5cc74e81254.png)

照片由[https://guerrillabuzz.com](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral)[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)——[GuerrillaBuzz Crypto PR](https://guerrillabuzz.com/)拍摄

这篇文章是我想在开始着手 [Solana](https://solana.com) 程序开发时找到的文章。事实上，有很多资源可以教你如何构建 Solana 程序，但是我还没有找到一个足够完整的资源可以让你真正理解幕后发生的事情。

在本文中，我将解释以下内容:

1.  索拉纳计划和账户的基本概念
2.  基本索拉纳程序结构
3.  从这里去哪里

到这篇文章结束时，你应该能理解窗帘后面的铁锈是干什么的了。

我希望这能促进你的旅程:)让我们开始吧。

# 1 — Solana 计划和客户

如果你来自以太坊世界，索拉纳计划基本上是一个聪明的合同。一开始我最大的困扰是在 Solana 中程序是“无状态”的。换句话说，在以太坊世界中，你可以创建一个*计数器*智能合约，并将*计数器*的值存储在智能合约本身中，这在 Solana 上是不可能的。为了在 Solana 上存储数据，您需要使用帐户。

让我澄清这一点。总的来说，你可以把索拉纳世界想象成一个由客户主导的世界。事实上，索拉纳程序只是一个被标记为可执行的账户。从[索拉纳官方文件](https://docs.solana.com/terminology#account)中我们读到一个账号是:

> Solana 分类帐中的一种记录，它或者保存数据，或者是一个可执行程序。

在 Solana 中，除了数据帐户和程序帐户，还有表示 Solana 上的本地程序的本地帐户。其中最重要的是系统程序。这个程序很重要，因为 Solana 上的每个新帐户都默认为系统程序所有。

为了完善我们的帐户知识，我们可以说帐户具有:

1.  所有者→这必须是一个程序，默认情况下是系统程序
2.  持有人→拥有账户私钥的人
3.  总是可以通过公钥来寻址
4.  可以保存数据(如果不可执行)和 SOL(sola 本地令牌)

这提出了一个重要的问题。如果我不是我帐户的所有者，我如何从/向我的帐户转移 SOL？在 Solana 上，虽然任何人都可以对账户进行贷记，但只有账户所有者可以对账户进行借记和更改数据。

索拉纳通过以下方式实现这一目标:

1.  账户持有人必须用其私钥签署指令
2.  指令必须发送给系统程序(即账户所有者，也是唯一允许借记账户和更改数据的人)
3.  系统程序对照账户公钥检查私钥，如果检查通过，则授权转账

我希望这澄清了索拉纳计划和帐户。现在我们准备好看一些代码了。

# 2 —程序结构

如果你熟悉 REST api，当你在 Solana chain 上写一个程序时，你需要定义你的程序能够处理哪些指令，因为它们是你的 web 服务器的端点。

函数签名通常采用以下参数:

*   程序 id →这是当前正在执行的程序的公钥
*   在交易过程中程序可以读写的账户。一个帐户在数组中的位置表明了它的意义，例如，当转移资金时，一个指令可以将第一个帐户定义为源，将第二个帐户定义为目的
*   特定于该功能的附加数据

在简单的 Rust 代码中，这可以解释为:

图书馆

需要注意的是，Solana 希望您将交易中涉及的所有帐户传递给函数。这使得 Solana 可以通过并行处理涉及不同账户的交易来优化交易本身的吞吐量。

换句话说，运行库可以查看一个程序(甚至跨程序)的所有传入事务，并可以检查事务的第一个参数中的内存区域是否重叠。如果没有，运行时就可以并行运行这些事务，因为它们彼此不冲突。更好的是，如果运行时发现两个事务访问重叠的内存区域，但只读不写，它还可以并行处理这些事务，因为它们彼此不冲突！

为了举例，我们重点看一下 Solana 提供的 [Hello World 例子](https://github.com/solana-labs/example-helloworld)。在本例中，我们希望存储我们向特定客户打招呼的时间。因为 Solana 中的程序是无状态的，所以我们至少需要:

1.  一个 *HelloWorld* 可执行程序(又名。程序帐户)可以处理我们的指令
2.  一个或多个*问候账号*(每个你要问候的账号一个)。那些将是不可执行的程序(又名。数据账户)，其可以存储他们被问候的次数。这些帐户必须归 HelloWorld 程序所有，以便它能够进行更改

关于要点 1，我们可以使用 [Solana CLI](https://docs.solana.com/cli/deploy-a-program) 简单地部署我们的 HelloWorld 程序。

关于要点 2，我们可以在执行 Hello 之前创建不存在的问候帐户，并将所有权转移到 HelloWorld 程序(查看完整的客户端代码[此处](https://github.com/solana-labs/example-helloworld/blob/master/src/client/hello_world.ts#:~:text=//%20Check%20if%20the%20greeting%20account%20has%20already%20been%20created))

来自 hello_world.ts 的亮点

现在,`process_instruction` Rust 代码应该清楚多了。让我们来看看:

lib.rs 中的突出显示

让我们开始吧:

1.  我们使用来自 Solana 的`next_account_info`实用程序从帐户列表中获取问候帐户值
2.  我们检查 HelloWorld 程序 id 是否是该帐户的所有者，以及进行更改的能力。如果不是，我们抛出一个错误
3.  如果一切正常:
    -我们使用`borsh`
    取消帐户的序列化-我们递增数据属性
    中的计数器-我们用新数据序列化帐户

# 从这里去哪里

您可能已经注意到，基本的 Solana 代码包含许多样板文件(例如，params 序列化和反序列化、帐户验证…)
为此， [Anchor](https://github.com/coral-xyz/anchor) 是一个框架，可以帮助您更快地创建 Solana 程序，并主要关注程序的业务逻辑。

现在，你可能想知道为什么不从 Anchor 开始，而不是从 Solana plain 开始:)总的来说，我相信从零开始总是理解抽象如何工作的好方法，并允许你在开发时做出有意识的选择。

# 参考

*   索拉纳
*   [索拉纳简介](https://book.anchor-lang.com/prerequisites/intro_to_solana.html)