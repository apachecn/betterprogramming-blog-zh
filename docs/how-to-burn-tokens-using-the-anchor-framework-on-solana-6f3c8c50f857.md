# 如何使用 Solana 上的 Anchor 框架刻录令牌

> 原文：<https://betterprogramming.pub/how-to-burn-tokens-using-the-anchor-framework-on-solana-6f3c8c50f857>

## 试用该程序的简要指南

![](img/b3b0b10adce5439a51024ce9b923a3f1.png)

照片由[阿玛多·洛雷罗](https://unsplash.com/@amadorloureiro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/flame?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这篇文章是我写的上一篇文章“如何使用 Anchor 框架初始化 Mint 帐户和 Mint 令牌”的延续，可以在下面的[链接](/how-to-initialize-mint-accounts-and-mint-tokens-using-the-anchor-framework-c989d1ba4f97)中找到。我强烈推荐阅读那篇文章，以便更好地理解本教程的背景。

本文将向您展示如何在 Anchor 框架中使用“派生宏”来烧录已经生成的令牌。在我们开始之前，我想告诉你什么是 Anchor 框架，以及它将如何帮助像我们这样的开发人员在 Solana 网络之上进行构建。与以太坊生态系统不同，在 Solana 之上开发略有不同，主要是因为它们是两种类型的区块链网络。

如果您以前有过在 Solana 网络上开发的经验，您就会知道在 Solana 上开发时，序列化和反序列化是您应该始终记住的两个概念。这就是锚框架的用武之地。这个框架简化了结构的序列化/反序列化，因此开发人员不需要担心这个问题。这是通过“派生宏”完成的

根据官方文档，如这里的[和](https://docs.rs/anchor-lang/latest/anchor_lang/derive.Accounts.html)所示，派生宏可以通过使用属性和指令提供进一步的功能。

在本文中，让我们看看 Anchor 框架是如何让开发人员非常容易地从特定的关联令牌帐户(ATA)刻录令牌的。

# 第一步。先决条件

*   VS 代码—代码编辑器
*   锚点版本— 0.24.2
*   本教程的第 1 部分可以在[这里](/how-to-initialize-mint-accounts-and-mint-tokens-using-the-anchor-framework-c989d1ba4f97)找到
*   之前对锚框架的了解

# 第二步。批准将由 ATA 所有者铸造的令牌

这是 Rust 代码，用于将批准转移给另一方，以烧掉其他人拥有的令牌。

让我们深入研究这段代码，看看它是如何工作的:

*   结构`ApproveToken` 是传递给函数`approve_token.`的内容
*   第 31 行指定了我们应该批准哪个 ATA 的令牌。
*   第 34 行指定了应该向谁授予燃烧许可。值得注意的是，该事务应该由 ATA 的当前所有者签名，而令牌现在就在 ATA 中。
*   第 35 行的`signer` 应该是第 31 行提到的 ATA 的所有者。

然后，这个结构将被传递给`approve_token` 函数，以及应该给予批准的令牌数，指定为`amount`。然后，将执行跨程序调用(CPI)来批准烧毁令牌。

现在，让我们来看一下批准烧毁令牌的 JS 代码。

正如您在上面这段代码中看到的，函数由`fromWalletB`签名，它目前拥有 ATA 和其中的令牌。由于`fromWalletA` 被指定为`delegate`，在该交易被执行后，给定 ATA 中的 100 个代币的批准将被交给`fromWalletA` 进行销毁。

需要注意的一件重要事情是，即使在执行了事务之后，指定数量的令牌仍然在同一个 ATA 中。只有其被刻录的批准被转移到`fromWalletA`。

另一件要记住的重要事情是，如果你想烧掉你的代币，批准应该还给铸币账户的原所有者。即使您将批准转移到某个随机钱包，它们也不能被刻录并在以后从您的 ATA 中删除。只有代币来源的铸币账户的所有者可以烧掉它们。

# 第三步。从 ATA 刻录令牌

假设对要铸造的令牌的批准已经被转移回 mint 帐户的所有者，让我们看看如何编写代码来从特定的 ATA 中刻录令牌。

这段代码类似于上一教程中用来铸造令牌的代码。因此，我不会深入这段代码来解释它是如何工作的。现在你应该很清楚了。需要注意的是，在第 34 行和第 35 行中,`from` 账户被标记为`mut`,因为该函数会改变账户的余额。同样地，`mint`账户也被标记为`mut`。烧完之后，代币的总供应量会减少。

现在，让我们看看如何与来自 JS 的 Rust 代码进行交互:

这也非常类似于铸造令牌的 JS 代码。因此，我不会浪费你的时间来解释代码。如果您有任何问题，请随时发表评论。

给你。这就是你如何使用锚框架烧令牌。您可以看到 Anchor 框架是如何让 Solana 开发人员的生活变得非常轻松的。

我会跟进一些来自区块链世界的其他教程。

在那之前，

干杯！！

# 资源

1.  锚郎文档—[https://docs.rs/anchor-lang/latest/anchor_lang/derive.Accounts.html](https://docs.rs/anchor-lang/latest/anchor_lang/derive.Accounts.html)
2.  教程第一部分—[https://better programming . pub/how-to-initialize-mint-accounts-and-mint-tokens-using-the-anchor-framework-c 989 D1 ba 4 f 97](/how-to-initialize-mint-accounts-and-mint-tokens-using-the-anchor-framework-c989d1ba4f97)