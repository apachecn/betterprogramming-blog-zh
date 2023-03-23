# 如何在 Solana 和 Anchor 中创建智能合同

> 原文：<https://betterprogramming.pub/how-to-create-smart-contracts-in-solana-and-anchor-e67ff0747c63>

## 锚定部署您的合同

![](img/5d32b496a987c12223fba30ad2964f50.png)

# 索拉纳是什么？

Solana 是 Anatoly Yakavenko 创建的一个去中心化的区块链平台。索拉纳的盖帽时间是 400 毫秒，快得惊人。

这是因为他们的历史共识机制的证明，这实际上是一个利害关系的证明，但增加了额外的可变时间。PoH 用于将不可信的时间流逝编码到只附加数据结构的分类帐中。

在提交块之前，区块链必须同意时间，节点必须来回聊天以确定时间，直到他们同意时间，这可能需要很多时间。Solana 提出了一个解决方案，通过历史证明来保存时间戳，这样节点就不必等待时间一致，而且他们还有一个加密证明。

要了解更多关于历史证据的信息，我推荐你阅读 Solana 的白皮书。

# **什么是主播？**

Anchor 是一个开发 Solana smart contract 的框架，包含几个开发工具。所以基本上，主播是一个救生员，让开发智能合同变得非常容易。

# **项目概述**

在本指南中，我们将重点关注项目设置、基本操作和测试。

我们将创建一个计数器应用程序，每当我们调用 RPC 时，计数器就加 1。

# **先决条件**

Rust——Rust 是一种非常强大的通用编程语言。我们将把它用于智能合约开发。有一本很好的[书](https://doc.rust-lang.org/book/)可供学习 Rust。

[Solana 工具套装](https://docs.solana.com/cli/install-solana-cli-tools) —包括 Solana CLI。

这个项目的代码可以在[这里](https://github.com/anmoldh121/solana-counter)找到。

# **入门**

首先，创建一个新的锚点项目:

```
anchor init counterapp
```

在项目结构中，您将看到以下文件和文件夹。

这是索拉纳计划(智能合同)的目录

`test` —这是 javascript 测试代码所在的地方

`migrations` —这是部署脚本

`app` —这是前端将要建立的地方

让我们看看程序目录中的`lib.rs`文件。

```
use anchor_lang::prelude::*; declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS"); #[program]
pub mod counterapp {
    use super::*;
    pub fn initialize(ctx: Context<Initialize>) -> ProgramResult {        Ok(())
    }
}#[derive(Accounts)]
pub struct Initialize {}
```

这是 CLI 写的最基本的程序。有一个函数 initialize 在被调用时什么都不做就成功执行，initialize 结构体定义了 initialize 函数的上下文。

现在我们已经建立了我们的项目，让我们建立我们的计数器应用程序。

为此，我们需要创建一个帐户来存储我们的数据。你一定在想这个账户到底是什么？帐户只是在 Solana sealevel 中存储和访问数据的方式。更多信息，推荐阅读[本](https://solana.wiki/zh-cn/docs/account-model/#:~:text=On%20Solana%2C%20any%20account%20can,completely%20stored%20in%20other%20accounts.)。

在上面的代码中，我们定义了两个结构，`CounterAccount`结构是我们的`Account`包含一个计数变量，它将存储我们的计数。

```
#[derive(Accounts)]
pub struct Create<'info> {

    #[account(init, payer=user, space = 16+16)]
    pub counter_account: Account<'info, CounterAccount>,

    #[account(mut)]
    pub user: Signer<'info>,

    pub system_program: Program<'info, System>,
}#[account]
pub struct CounterAccount {
    pub count: u64,
}
```

`Create`结构是我们的指令结构，它定义了创建账户的上下文，也就是说，“嘿，我想创建一个 32 字节的账户 counter_account”。

`#[account(…)]`属性定义了 Anchor 为创建上下文所做的预处理中的约束和指令。现在让我们创建我们的函数。

```
pub fn create(ctx: Context<Create>) -> ProgramResult {
    let counter_account = &mut ctx.accounts.counter_account;
    counter_account.count = 0;
    Ok(())
}
```

`create`函数是 rpc 请求的处理程序，它接受用`Create` struct 创建的上下文。

酷毙了。我们已经完成了这个神奇的项目。现在让我们创建测试函数并部署我们的杰作。

```
import * as anchor from '@project-serum/anchor';
import { Program } from '@project-serum/anchor';
import { Counterapp } from '../target/types/counterapp';describe('counterapp', () => { const provider = anchor.Provider.env()
    anchor.setProvider(provider); const program = anchor.workspace.Counterapp as Program<Counterapp>; const counterAccount = anchor.web3.Keypair.generate(); it('Is initialized!', async () => {
        await program.rpc.create({
            accounts: {
                counterAccount: counterAccount.publicKey,
                user: provider.wallet.publicKey,
                systemProgram: anchor.web3.SystemProgram.programId,
            },
            signers: [counterAccount]
        } as any)
    }); it("Increment counter", async () => {
        await program.rpc.increment({
            accounts: {
                counterAccount: counterAccount.publicKey
            }
        } as any)
    }) it("Fetch account", async () => {
        const account: any = await
        program.account.counterAccount.fetch(counterAccount.publicKey)
        console.log(account.count)
    })
});
```

现在，进行测试。

```
anchor test
```

测试通过后，我们现在可以部署程序了。确保`solana-test-validator`正在运行。

```
anchor deploy
```

感谢阅读。