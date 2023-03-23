# 索拉纳编程初级读本

> 原文：<https://betterprogramming.pub/solana-programming-primer-1c8aae509346>

## 茄遇锈(无锚)

![](img/fe34f8cb9872911762e43c735aab4d67.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@theshubhamdhage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我在 2022 年 2 月的最后一刻写了这篇文章，以便在 Illini Blockchain 的 Solana Onboarding 期间提供额外的指导/资源。

它伴随着我 2022 年 1 月参加的 Jump Crypto x Solana Labs x pyt boot camp 的 [echo 项目](https://github.com/IlliniBlockchain/solana-onboarding/tree/master/echo-skeleton)。

我想我应该把这个作为公开记录。这概述了用 Rust 编写的 Solana 程序开发的基础，没有流行的框架锚。

# 先决条件

对 [Solana 编程模型](https://docs.solana.com/developing/programming-model/overview)的基本理解。
基本[生锈](https://doc.rust-lang.org/book/) ( [以身作则](https://doc.rust-lang.org/rust-by-example/))。

# 项目文件结构

这是一个索拉纳项目将包括什么的基本例子。

```
project/
  js/
  program/
    src/
      entrypoint.rs
      error.rs
      instruction.rs
      lib.rs
      processor.rs
      state.rs
    tests/
    Cargo.toml
```

简单解释一下主要元素:

*   `js/` —用于测试的客户端脚本。
*   `entrypoint.rs`—`processor.rs`上的包装器，提供区块链上的接入点。
*   `error.rs` —自定义错误定义。
*   `instruction.rs` —表示不同指令及其参数的枚举定义。
*   `lib.rs` —导出模块，以便在其他板条箱中轻松访问。
*   `processor.rs` —实际运行代码的定义。
*   `state.rs` —账户数据序列化的结构定义。
*   `Cargo.toml` —定义板条箱元数据和依赖关系。

# 开发流程

1.  修改程序并部署。

```
cargo build-bpf // builds your program - make sure you're in your program's root directorysolana program deploy [PATH_TO_DOT_SO_FILE]// example: solana program deploy /Users/alecchen/Documents/Code/solana-onboarding/echo-skeleton/program/target/deploy/echo.so
```

2.运行客户端脚本进行测试。

```
// javascript
node [SCRIPT_FILENAME]
// example: node index.js
```

3.客户端要么成功并在块浏览器上打印到事务的链接，要么它将发出失败事务的散列，然后您将它复制并粘贴到块浏览器中。

```
// example success output:
<https://explorer.solana.com/tx/85CVVw4CrmNbzqCXEmRDF3LEUFEymc4s1pUtDK1urcV6R3jbRjdJNWGjb24Trbt8bZjN5fJkqA55GjNvoBLubfE?cluster=devnet>
Echo Buffer Text: asdfff
Success

// example fail output:
Error: Transaction 4MDQWBNkuRAf59gjqgoFBDLq76UPVS73LUktTP1TJ8op1Qtvw2UsxcRw6tMHKo5mjsWEaZWC5CLePtV44YTFsUA3 failed ({"err":{"InstructionError":[1,"InvalidInstructionData"]}})
    at sendAndConfirmTransaction (/Users/alecchen/Documents/Code/solana-onboarding/echo-skeleton/js/node_modules/@solana/web3.js/lib/index.cjs.js:2981:11)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async main (/Users/alecchen/Documents/Code/solana-onboarding/echo-skeleton/js/index.js:75:14)
// copy 4MDQWBNkuRAf59gjqgoFBDLq76UPVS73LUktTP1TJ8op1Qtvw2UsxcRw6t into a block explorer (make sure you're on devnet!)
```

Solana 的块资源管理器对调试非常有帮助！花些时间探索输出的一切。它将显示许多有用的信息，包括帐户输入、它们的可写/签名者标志、SOL 和`spl-token`余额变化、指令和调用的子指令，以及程序日志，您可以在其中找到打印语句和错误消息。

4.重复步骤 1–3。

# 程序代码

当您实现 Solana 程序指令时，您的代码通常由 3 个主要部分组成:

1.  获取帐户和反序列化
2.  帐户验证
3.  逻辑和功能

# 获取帐户和反序列化

帐户以数组`AccountInfo`的形式传入。要使用这些，您将定义一个迭代器并检索对帐户的引用。

但是你怎么知道哪个账户是哪个账户呢？您需要在某处指定您期望的帐户，然后 1)由客户端传递正确的帐户数量和顺序，2)由您作为程序开发人员确保您的程序仅在您收到正确的帐户时运行(这部分是帐户验证部分)。您应该注意到在 echo 框架中，在客户端上定义指令时传递的帐户与`instruction.rs`中预期/概述的帐户相匹配。

总之，关于反序列化。默认情况下，帐户数据只是一个字节数组。如果您希望某个帐户的数据以某种特定的结构到达，您可以反序列化它以有组织的方式访问该数据。您需要预先定义该结构，并在其中包含某种反序列化字节的方法(这些方法通常被定义为 Rust 中的特征)。SPL 程序通常有使用`Pack`特征的反序列化方法。如果您正在定义自己的结构， [Borsh](https://github.com/near/borsh) 是一个为常见数据类型预定义方法的库。

一个`spl-token`计划薄荷账户的例子:

我们自己定义的结构示例:

# 帐户验证

如前所述，您依赖于客户端传递符合您期望的帐户。这为客户端传递可能会破坏代码功能的帐户提供了机会。

例如，假设您有一个基本的交换程序，其中该程序需要这 4 个帐户(以及其他帐户):您的令牌 a 保管库、您的令牌 b 保管库、交换程序的令牌 a 保管库以及交换程序的令牌 b 保管库。它从您的保管库中取出一定数量的令牌 a，存入其自己的保管库，然后从其自己的保管库取出一定数量的令牌 b，并存入您的保管库。现在，如果您为自己的令牌提供一个保险库来代替交换程序的保险库，会怎么样呢？如果它没有验证帐户，那么它会将令牌 a 存入您的金库，而您将免费获得令牌 b！真实的 Solana 程序中的一个例子:二月初的虫洞漏洞是由于使用了一个不赞成使用的助手函数，该函数没有正确地验证帐户。

验证帐户最常见的情况是使包含特定数据的帐户无效，确保帐户是特定的 PDA，并检查程序是否是特定的程序。

## **验证账户数据**

为了验证数据，有两个部分:

1.  确保帐户可以正确反序列化
2.  确保数据符合特定的约束条件。

例如，确保发送交易的用户实际上是他们想要从中转移令牌的令牌帐户的所有者。

从现在开始，我将省略 imports 和 getting accounts 部分，使事情不那么混乱。

## **验证 PDA**

另一种验证帐户的常见方法是使用 PDA。如果帐户是 PDA，您可以通过自己查找 PDA 并检查密钥是否匹配来确保它是正确的帐户。你可以通过两种方式做到这一点:1)如果你只有种子而没有凸起，`find_program_address`和 2)如果你有凸起和种子，`create_program_address`。

## **检查程序 id**

所有的程序都是账户，如你所知，如果你的程序与某个账户交互，它必须被传递到指令中。因此，如果你的程序调用另一个程序，客户必须传递那个程序的帐号。

这带来了一个安全问题。现在，客户端可以传入一个程序，该程序实现了您的程序试图访问的相同指令，但却附加了一些恶意行为。为此，我们必须检查传入的程序帐户是否正确。

通常你会和一些著名的程序一起工作，他们会有公开的 Rust crates，暴露出某种`id()`功能，让你很容易检查，例如索拉纳本地程序，SPL 程序。其他时候，您可能只需要硬编码程序 id。

## **assert_msg helper**

我在训练营中被介绍的一个很好的助手功能是`assert_msg`。当您抛出一个错误时，它只会打印出与该错误类型相关的消息。您可能在不同的上下文中使用相同的错误，在不同的上下文中，额外的消息将有助于调试。`assert_msg`正是如此。

# 逻辑和功能

在你确保了你的账户都是正确的之后，你实际上可以写代码了。在这里，你可以随心所欲地做任何事情，但无论如何，你可能会做以下两件事情中的一件或两件:

1.  修改帐户数据
2.  调用其他程序

## **修改账户数据**

修改帐户数据的两种方法:1)直接修改字节，2)定义一个结构并将数据序列化到帐户字节数组中。

直接修改:

序列化数据(使用 Borsh):

## **跨程序调用**

通常在程序中，你会写你必须调用区块链上的其他程序。对于这些，您将主要使用两个函数:`[invoke](<https://docs.rs/solana-program/1.6.4/solana_program/program/fn.invoke.html>)`用于常规的跨程序调用，而`[invoke_signed](<https://docs.rs/solana-program/1.6.4/solana_program/program/fn.invoke_signed.html>)`用于 PDA 需要签名的跨程序调用。

它们本质上是一样的，除了`invoke_signed`你为 PDA 传递种子(附加了凹凸)。

两者的例子在[这里](https://gist.github.com/alecchendev/c872ed76541b339ce3d71c72783c7d7b)都有。

您会注意到第一个参数采用了`Instruction`类型。像所有指令一样，它包括帐户、程序 id 和输入数据。

然而，正如你在例子中看到的，通常程序会有包装函数来创建指令，这使得我们更容易做到这一点。您可以通过检查源代码来了解如何创建原始指令。