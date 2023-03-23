# 使用 Swift 在装有 SwiftWasm 的 MacOS 上为 WebAssembly 创建受 React 和 SwiftUI 启发的 Web 应用程序

> 原文：<https://betterprogramming.pub/create-a-react-and-swiftui-inspired-web-app-with-swift-for-webassembly-on-macos-with-swiftwasm-7741491b2886>

## SwiftWasm 是实验性的，但这并不意味着我们不能有一些乐趣！

![](img/6a3a1df21513ad304b750b1302f418a6.png)

[梅里达勒](https://unsplash.com/@meric?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/spring?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

第一步是设置你的 macOS 机器使用实验性的[swift wasm](https://swiftwasm.org/)([web assembly](https://webassembly.org/)编译目标)。这里有一个关于如何做的指南。

# 安装开发工具

我们将使用[托卡马克](https://github.com/TokamakUI/Tokamak)来创建一个使用 SwiftUI 启发的语法的 web UI。长期目标是创建 web 用户界面，就像我们为苹果生态系统所做的那样。

但是首先，让我们安装一些开发工具来简化这个过程:

```
brew install swiftwasm/tap/carton
```

# 创建一个基于托卡马克的项目

```
mkdir TokamakApp && cd TokamakApp
carton init --template tokamak
```

现在让我们用实时重载启动开发服务器:

```
carton dev
```

在您最喜欢的浏览器中打开`http://127.0.0.1:8080`。现在每次你编辑项目中的代码，`carton`都会重新加载浏览器标签。很 JS-开发风格。方便。

# 现状核实

现在的理论是，我们可以用托卡马克编写应用程序 UI，就好像它是 SwiftUI 代码一样。现实是托卡马克还远没有完成，所以不是所有的 SwiftUI 实体都已经完全实现了。

# 使用 JavaScript 包装器创建你的网站

我们能做的是向下一层，在 JavaScript 的支持下创建 DOM 元素。我们可以使用 [DOMKit](https://github.com/swiftwasm/DOMKit) 。它是浏览器 DOM 的中级 Swift 包装器。

或者我们可以使用 [JavaScriptKit](https://github.com/swiftwasm/JavaScriptKit) 再往下两层。它是一个围绕 JavaScript API 的低级 Swift 包装器，我们用它来直接与 DOM 交互。

你可以在这里找到更多的[例子](https://github.com/swiftwasm/JavaScriptKit/tree/main/Example)。

# 导入和导出函数

在 Swift web 工具真正稳定并且功能完整之前，我们可能仍然需要使用基于 JS 的框架来开发 web 应用程序的某些部分。因此，需要[将一些 JS 功能](https://book.swiftwasm.org/examples/importing-function.html)导入我们的 Swift 应用程序。但是，或许[将一些 Swift 功能](https://book.swiftwasm.org/examples/exporting-function.html)导出到 JS 应用程序会更有用，比如导出一个真正执行和计算密集型 Swift 功能，用于 JS 界面的图像处理。

# 结论

Swift for WebAssembly 和 for web general 显然是不成熟和不完整的，但这是一个非常令人鼓舞的开始。我真的希望在不久的将来，它能成为全栈 Swift web 开发人员的真正选择。