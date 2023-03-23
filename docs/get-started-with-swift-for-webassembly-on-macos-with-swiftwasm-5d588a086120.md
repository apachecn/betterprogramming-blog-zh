# 使用 SwiftWasm 在 macOS 上开始使用 Swift for WebAssembly

> 原文：<https://betterprogramming.pub/get-started-with-swift-for-webassembly-on-macos-with-swiftwasm-5d588a086120>

## 了解 SwiftWasm

![](img/71389f412ad9ef79a940c087ce88f204.png)

由 [Masaaki Komori](https://unsplash.com/@gaspanik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/spring?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

SwiftWasm 背后的一群勇敢的灵魂正在致力于将 WebAssembly 添加为 Swift 编程语言的编译目标。这是一项正在进行的工作，所以还没有完全准备好。但是我们已经能够在 Wasm 运行时(例如浏览器)执行 Swift 代码了！

让我们在最新的 macOS 上设置 SwiftWasm。然后，让我们尝试创建第一个基于 Swift 的应用程序！

# 安装支持 Wasm 的 Swift 语言

Wasm 支持尚未在 Swift 官方资料库中提供。因此，我们必须安装一个有实验支持的语言快照。

从[项目 GitHub](https://github.com/swiftwasm/swift/releases/latest) 下载并安装最新的 macOS 版本 SwiftWasm(撰写本文时为 5.3)。

为了能够从命令行使用 SwiftWasm 工具链:

```
export PATH=/Library/Developer/Toolchains/swift-latest.xctoolchain/usr/bin:"${PATH}"
```

并测试它是否已正确设置:

```
swift --version
```

# 测试 SwiftWasm

创建 HelloWorld Swift 文件:

```
echo 'print("Hello, world!")' > hello.swift
```

将 Swift 源代码编译成 Wasm 字节码:

```
swiftc -target wasm32-unknown-wasi hello.swift -o hello.wasm
```

# 运行输出

要从命令行运行代码，可以使用`wasmer`(Wasm 运行时)。

安装它:

```
curl https://get.wasmer.io -sSfL | sh
```

执行 Wasm 代码:

```
wasmer hello.wasm
```

# 使用 SwiftPM 制作 Swift 套餐

我们可以利用 SwiftPM 创建一个包，然后编译成 Wasm。

很简单。让我们创建一个名为`testpack`的包，然后编译并执行它:

```
mkdir testpack && cd testpack
swift package init --type executable
```

让我们来建造它:

```
swift build --triple wasm32-unknown-wasi
```

执行它:

```
wasmer .build/debug/testpack
```

# 接下来，用 SwiftWasm 创建一个 Web 应用程序

我们只是勉强设置了整个事情，并执行了一个简单的 Hello World。现在[让我们用 Swift 制作](https://medium.com/better-programming/create-a-react-and-swiftui-inspired-web-app-with-swift-for-webassembly-on-macos-with-swiftwasm-7741491b2886)我们第一个受 ReactJS 和 SwiftUI 启发的 web 应用！