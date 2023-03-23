# 编写 WASM 兼容代码的重要性

> 原文：<https://betterprogramming.pub/the-importance-of-writing-wasm-compatible-code-7916519fa30e>

## 这不需要太多的努力，而且它可能是分发它的关键

![](img/db878914c055a2dcd4d2cce7f8c9886c.png)

照片由 [Artem Labunsky](https://unsplash.com/@labunsky?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我已经写了几篇关于 WebAssembly 和 Rust 的文章，但是这篇文章应该对那些用可以编译成 WASM 的语言编写代码的人有用，尤其是如果你使用的是一流的 WebAssembly 公民，比如 Rust、C、C++或 Zig。

如果你没有读过 WASM 的介绍性资源，比如本帖中的[，WebAssembly 是一种二进制语言，由它的 VM 转换成机器代码。你没听错，是 VM，也就是说你只需要一个支持 WASM 的 VM 就可以运行任何以`.wasm`结尾的可执行文件。此外，在大多数情况下，将您的代码编译成 WASM 只需很少的努力。](/get-started-with-wasm-in-rust-2347056bab4)

# WASM 应用程序的工作流

在解释为什么您应该拥有 wasm 兼容版本的代码之前，让我们来看看您开发和部署 WASM 应用程序的流程是什么样的:

1.  将代码/现有代码移植到 wasm 兼容的分支中
2.  将其编译为 WebAssembly
3.  发布`.wasm`二进制文件
4.  有人管理它，例如:`wasmer run your_code.wasm`

就是这样:一个不需要安装和跨平台(是的，还有移动)的解决方案，只需要你编译`your_code`到 WebAssembly。

这几乎让我想起了 docker，它们有着相似的特征，正如这里的[所解释的那样](https://www.docker.com/blog/why-containers-and-webassembly-work-well-together/)，Docker 可以运行 wasm 容器，它们甚至可能成为最流行的容器。

# 编译到 WASM 意味着什么

构建面向 WebAssembly 的代码意味着任何人都可以运行它，而无需安装、依赖冲突和强制安全性，尤其是在使用 Rust 等语言编写代码时，Rust 以在编译时强制执行安全实践而闻名。

作为用户，您可能更愿意运行 WASM 二进制程序，因为您知道只要虚拟机是安全的，它就可以运行在沙盒中，例如，如果它无法检查变量的边界，就不会容易发生转义，在本例中，这是解释器的问题。

即使 WASM 是一项相对较新的技术，像 Cloudflare 这样的服务已经提供了一个[环境来将 Rust 无服务器](/rust-for-the-fastest-serverless-experience-80530acdd4d4)功能部署到 Cloudflare 网络，就像它们是 JavaScript 功能一样。

这种多功能性是每个开发人员在编写代码时都应该考虑的，特别是当它不需要花费太多精力来实施本段中描述的特征时。

# +1 如果不依赖 Rust 的标准库

对于大多数用例来说,`std`工作得很好，但是目标 WASM 的构建更有可能在`#[no_std]`环境中运行，因为它们可能是嵌入式系统，或者在一般环境中,`.wasm`二进制文件中的每个字节在成本和性能方面都有所不同。

Rust 标准库包含了很多东西，所以如果你能依靠`core`库来构建你的应用，那对你来说就是+1。你可能仍然需要在`std`中使用类型，比如向量。然而，你可以[创建你自己的](/rust-should-you-work-on-your-own-types-yes-and-heres-how-5214c61300b0) `[Vec](/rust-should-you-work-on-your-own-types-yes-and-heres-how-5214c61300b0)` [类型](/rust-should-you-work-on-your-own-types-yes-and-heres-how-5214c61300b0)或者用你需要的基本类型创建你自己的`std`库，而不是使用整个 std 及其 prelude 导入。

# 编译到 WASM

我只用过 Rust 的 WebAssembly，我不认为我需要为此切换到另一种语言。如果你不知道 Rust/C/C++/Zig 或任何其他支持编译到 WASM 的语言，你可以使用 AssemblyScript (AS ),但它很可能会导致内存使用效率较低，这并不一定意味着它会运行得更慢，但在一些情况下，如智能合同开发，你需要小心内存的去向，因为你要支付费用，我总是建议使用高效的语言。

这部分只是关于编译 Rust 到 WASM，因为我没有用其他语言编译的经验。然而，其中一些提示可能对其他语言也有用，因为它们引用了更广泛的 WASM 二进制。

将库编译成 WebAssembly(在 Rust 中)非常简单:

```
cargo build —-target wasm32-unknown-unknown
```

但是您可能会看到一些错误，这些错误是您在不针对 WebAssembly 进行测试或构建时看不到的，尤其是如果库很大并且依赖于许多依赖项(这些依赖项通常依赖于许多依赖项本身)。以下是一些常见错误和相应的修复方法。

# 库不兼容性

最常见的错误很可能是库不兼容，这给你留下了四个选项:

*   有时候，你只需要启用特性；一些库可能支持 WASM，但只有特定的特性标志。在这种情况下，您可能想要检查库的`Cargo.toml`文件并查看可用的特性。
*   在库的存储库中打开一个问题，询问 WASM 支持，最好的情况是，维护者可以添加它。
*   叉库加 WASM 支持自己
*   使用另一个 WASM 兼容的库

# 避免文件系统

web 上没有文件系统。因此，WebAssembly 中没有。如果需要读写文件，可以将内容作为参数提供。一些虚拟机还允许您提供输入和输出作为 CLI 参数。

或者，你可以看看 WASI 的。

# 避免系统库

WASM 没有系统库(很明显)，绑定到系统库的板条箱不会工作。

## 对依赖 std 的库持怀疑态度

使用 Rust 标准库的库更有可能执行非法的 wasm 操作，比如访问文件系统。

## 如果您正在与 JS 通信，请使用`wasm-bindgen`

这里不多说了，`wasm-bindgen`是 Rust 和 JavaScript 之间的桥梁。如果你在浏览器上使用 WASM，你可能需要使用这个箱子。例如，[我在本文](/get-started-with-wasm-in-rust-2347056bab4)中使用了它。

# 不要做同步输入输出的事情

网络是异步的。您不能同步执行 I/O，但必须使用期货。例如，编译到 WASM 的 rust Cloudflare 工作程序就是这样设法等待它收到的请求内容的:

```
router.post_async("/wasm", |mut req, ctx| async move {     
    ...
    let json_string = req.text().await?;
    ...
}
```

您可以看到我们多么需要一个异步方法来产生结果。

# 分配器投诉

如果你使用的是分配函数，并且想在没有标准库的情况下编译成 wasm，你需要使用`wee_alloc`作为[，我在我的第二篇博客中解释了](https://heytdep.github.io/comp_posts/1)--Rust:-wasm-with-no-std-allocator/post.html)。

*如果你觉得我在这一部分遗漏了什么，请在评论部分随意添加内容。*

根据您的代码，使其与 WASM 兼容会有不同的难度。在某些情况下，这是不值得的，例如，如果代码严重依赖于文件系统，并且您不打算创建一个与 WASI 一起工作的 fork。

本文并不是要告诉开发人员总是要编写 WASM 兼容的代码，而是要考虑 web assembly 实施的一些策略，以及它们是否很适合他们的应用程序或工具。即使我强烈支持 WebAssembly 并试图用 WASM 兼容的语法来思考，我最近还是创建了自己的静态博客，使用不兼容 WASM 的 Rust 脚本将 LaTeX 文档转换成我的帖子。和所有事情一样，这都是关于取舍的。

感谢阅读！