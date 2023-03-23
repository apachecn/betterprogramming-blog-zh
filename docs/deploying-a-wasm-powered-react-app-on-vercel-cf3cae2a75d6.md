# 在 Vercel 上部署基于 WASM 的 React 应用

> 原文：<https://betterprogramming.pub/deploying-a-wasm-powered-react-app-on-vercel-cf3cae2a75d6>

## 让您的 WASM 反应应用程序走向世界的一步一步的指南！

![](img/3cc393ba6607c3c3ca6d18d8f4400ef8.png)

作者图片

在过去的几周里，我一直在开发一个 React 应用，用 Rust 编写的函数为它增压。现在，首先，一点背景…

# 什么是 Web 组装？

根据 webassembly.org 的说法，它被定义如下

> WebAssembly(缩写为 *Wasm* )是一种基于堆栈的虚拟机的二进制指令格式。Wasm 被设计为编程语言的可移植编译目标，支持客户端和服务器应用程序在 web 上的部署。

简而言之，你可以用 C++、Rust 之类的静态编译语言来编译你的高性能代码，或者采用 web 汇编格式。它具有高度的可移植性，并有望以本机速度执行。

不用说，我对这项相对较新的技术感到非常兴奋。因此，我创建了一个 React 应用程序，它使用 web 汇编代码作为“伪后端”。虽然，对我来说，有人可能会说这是一个大材小用，但这仍然是一个很好的学习机会。

现在让我们从指南开始。

# 目录结构

```
src/
└── components
    ├── ComposePrompt
    ├── Controls
    ├── Edge
    ├── Node
    └── styles
wasm-parser/
└── src/
```

如您所料，项目中有两个不同的子包:

*   由 create-react-app 创建的包。它由 npm 管理，将有一个关联的`package.json`。
*   该库由 cargo-wasm-parser 创建和管理。在这个目录中，可以指定需要编译成 web 程序集的 Rust 代码。

# 在 React 上调用 WASM 函数

各种详细的[文章](https://tkat0.github.io/posts/how-to-create-a-react-app-with-rust-and-wasm)解释了如何从 javascript 调用 web 汇编编译函数，反之亦然。在任何情况下，我将总结概括的细节。

## 货物库— [wasm-parser](https://github.com/mukkund1996/composed/blob/master/wasm-parser/src/parser.rs)

函数`generate_configuration`是从 React 客户端调用的。在函数之前使用的宏——`wasm_bindgen`——支持 JS 和 WASM 编译的代码之间的高级交互。

## 编译到 WASM

Rust 库可以使用 wasm-pack 工具编译成 WASM。为了方便起见，可以将 build 命令直接添加到 npm `package.json`文件中。

此外，需要将编译后的包指定为节点包的依赖项。

## [反应客户端](https://medium.com/p/cf3cae2a75d6/edit#:~:text=https%3A//github.com/mukkund1996/composed/blob/master/src/components/ComposedFlow.js)

在客户端，通过从先前添加到`package.json`文件的依赖项中导入 WASM 编译的函数，可以很容易地调用它。

# 部署到 Vercel

现在是部署你的应用程序的有趣部分，让每个人都可以享受它。对于这个例子，我使用 Vercel 作为平台来建立一个连续的部署管道，并随后部署我的应用程序的生产版本。为了避免供应商锁定，可以遵循相同的过程将其部署在其他平台上。

## 创建一个 Vercel 帐户

*   第一步是在 Vercel 上创建一个帐户，并将其链接到 [GitHub](https://vercel.com/docs/concepts/git/vercel-for-github) 。这确保了最新的更改反映在已部署的版本中。**注意**:这不一定是生产中的最佳选择，但对于业余爱好项目来说已经足够了。
*   从 Vercel 上生成的存储库列表中添加 GitHub 项目。
*   根据您的前端客户端所基于的框架选择模板。在我的例子中，我使用了 create-react-app 模板。

## 修改构建步骤

我们需要覆盖默认的构建步骤来构建 WASM 编译的代码并将其链接到您的 React 客户端。为了简化和标准化构建步骤，我创建了一个定制的 bash 脚本，可以运行该脚本来完成产品构建。我将在下面解释每个步骤:

## 安装铁锈

Vercel 使用[亚马逊 Linux 2](https://vercel.com/docs/concepts/deployments/troubleshoot-a-build#build-image) 作为其环境的基础映像。在我们可以编译到 WASM 之前，必须在环境中安装 Rustup。

## 安装 wasm 包

## 构建 WASM 包

## 完成生产构建

# 瞧啊。你完了。

如果构建日志是干净的，并且您没有任何错误，那么您的基于 WASM 的 React 应用程序现在应该已经部署好了！

```
**Want to Connect?**Thank you for reading my article. You can also find me on [LinkedIn](https://www.linkedin.com/in/mukkundsunjii/) and my work on [GitHub](https://github.com/mukkund1996).
```