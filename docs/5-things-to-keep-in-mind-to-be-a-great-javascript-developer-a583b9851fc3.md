# 成为一名优秀的 JavaScript 开发人员需要记住的 5 件事

> 原文：<https://betterprogramming.pub/5-things-to-keep-in-mind-to-be-a-great-javascript-developer-a583b9851fc3>

## 变得时髦，使用 JavaScript 的超级能力

![](img/a1b6a0272792b392bbf5a3b0e94cf882.png)

埃斯特万·洛佩兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

自从 JavaScript 得到改进并成为最流行的语言以来，对于开发人员来说，记住至少五个基本的关键概念就变得至关重要。

今天，JavaScript 是最流行的语言，Python 即使不断发展，也只是跟随开发者的兴趣。

# 1.应用程序上下文

JavaScript 几乎可以用来开发任何东西，真的。JavaScript 可以适用于各种各样的环境，它通常定义了您使用和应用该技术的方式。

是的，语言和语法保持不变，但是一旦你知道了常见的模式和关键概念，你必须能够掌握实现你的产品所需要的上下文和框架。

如果你使用电子，你必须能够处理安装，依赖和硬件支持。如果你使用 Ionic，你需要很好地了解移动设备的硬件、层次、显示屏分辨率和便携性，以及区分 iOS 和 Android 的关键差异。因此，即使语言是相同的，框架和工具也会发生很大的变化，你首先要记住的是你的应用程序的整体环境。您可以亲眼看到 JavaScript 在应用于移动软件、桌面软件和简单的 DOM 脚本时有多么不同。

下面是使用 JavaScript 可以实现的一些功能:

**桌面应用**:使用电子

混合移动应用:使用 Ionic 或其他类似 OnsenUI 的应用

**Web 应用(前端)**:使用 jQuery、Vue、Angular、React 等等

**网络服务和守护进程、Web 应用(后端)、微服务和几乎任何东西**:使用 NodeJS

**游戏**:使用 Unity

**嵌入式应用**:使用专用工具

# 2.ECMAScript 版本和框架

Javascript 改进了很多，有很多不同的标准导致了不同的 JavaScript 版本。请记住，根据您的环境，您开发所必须基于的语言规范可能会发生变化。浏览器是怪异的软件，除非你提供 polyfills 和其他支持系统，否则你不能假装在任何东西上编码任何东西。相反，大多数使用 Typescript 的框架给你更多的自由来编码，而不用担心支持，因为它们使用 Rx 和 polyfills 来使事情工作，就像 Angular。了解纯 JavaScript(有时称为“Vanilla”)、jQuery、Angular、TypeScript 和 ECMAScript 版本之间的差异可能需要一些时间来适应。

# 3.始终使用最好的 IDE

由于 JavaScript 开发变得越来越复杂，你不能用 UltraEdit、BBEdit、Atom 或 Sublime 编码。在你开始开发更大的东西之前，这些工具是有用的、简单的和伟大的。如果你用 NodeJS、Angular、React 或 Ionic 编写代码，这些文本编辑器很快就会变得毫无用处，因为它们不符合目标。那么，我该用什么呢？我已经使用 [WebStorm](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwiI2qX86aDkAhUL26QKHcr3ANsQFjAAegQIBhAC&url=https%3A%2F%2Fwww.jetbrains.com%2Fwebstorm%2F&usg=AOvVaw09YSDMh6Pac2zN7yaABwWv) 和 JetBrains 产品很长时间了，但我最近改用了 [Visual Studio Code](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwj4kqyF6qDkAhVJC-wKHU7LB90QFjAAegQICBAC&url=https%3A%2F%2Fcode.visualstudio.com%2F&usg=AOvVaw15O90sm1ios8AUpw56hCml) ，它很棒，很轻量级，信不信由你，可用于 Windows、Mac 甚至 Linux。哇哦。

无论如何，我最好的建议是始终信任并享受对您有益的 IDE，您可以放心地选择任何带有合适的调试器、lint 和语法助手、Git 和版本支持以及可视化工具的 IDE。记住，你选择的 IDE 是你最好的朋友，它可以为你节省无数的时间。

# 4.组织您的代码并处理错误；你不是 DOM-Scripting！

在我们多年的职业生涯中，我们只使用 JavaScript 编写 DOM-script 网页。

我仍然记得我第一次使用 JavaScript 的时候，我试图在我的浏览器窗口中动画显示状态栏文本…我今天感觉自己像一只恐龙。

今天，Javascript 更加复杂和强大，是一种很好的编程语言。它有一些小的负面特性，比如对对象操作和管理的不良支持，这经常迫使您与原型和其他怪异的东西作斗争，但是它是如此的通用，并且在一段时间后编码速度非常快，以至于我们可以接受这种妥协。

但是你能想到的最糟糕的主意就是像几年前一样使用 JavaScript。用一个好的设计概念来编码，不要着急，这总是很重要的。如果你想在更高的专业水平上使用 JavaScript，这是无法避免的。您不能像编写 jQuery 脚本例程那样编写 JavaScript。您的应用值得更多，您也一样。如果你没有以一个好的设计模式开始编码，你将永远不会在 JavaScript 上变得更好；您将永远保持 DOM-scripting。

除了组织代码和正确使用设计模式，不要忘记处理错误。我已经经历过**很多**的网页，在那里错误只是被抛出而从未被处理，在大多数情况下导致你的 jQuery 逻辑冻结并停止工作。在真实而复杂的应用程序中，这种问题要严重得多。在移动应用程序中，除非你的框架自动提供一个后备，否则它会使应用程序完全停止工作。在桌面应用程序或 NodeJS 应用程序中，它会有相同的行为，并且您不能允许您的应用程序或守护程序因未处理的错误而停止工作。

处理 JavaScript 错误的最好方法非常简单:实现 try-catch 块，最终创建自定义类来表示您的自定义异常，然后根据需要抛出它们。

# 5.如果可以的话，使用 JavaScript 的超能力

如前所述，JavaScript 已经改进了很多，而且你不是 DOM 脚本。这就是为什么您应该使用最新的 ECMAScript 特性和 Rx 支持的操作符来更快地编码并提高代码的可读性和灵活性。您可以使用`Promises`和`Observables`操纵和管理异步代码，我们鼓励您这样做，因为它们不难应用，并且它们提供了很大的改进和可能性。如果您的平台支持是兼容的，您甚至可以使用 web workers、SQLite 接口、依赖注入、模块化等等。不将应用程序建立在高级技术的基础上是很遗憾的，因为它们在许多平台和环境中都有很好的支持，从而提高了原生 JavaScript 技术的能力。

# 结论

提高您的 Javascript 知识是您无法避免的，而且绝对值得。“老一套”的 Javascript 已经一去不复返了，一个优秀的前端开发人员必须喜欢并开始融入新的技术、库和东西。站起来，在 JavaScript 上加点糖，你的代码会更有趣、更强大。如果你甚至不知道什么是`Observable`，你将永远不会被认为是一个优秀的现代 JavaScript 开发者，甚至你自己都不会！