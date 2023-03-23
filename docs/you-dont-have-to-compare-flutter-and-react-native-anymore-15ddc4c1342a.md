# 你不用再比较 Flutter 和 React Native 了

> 原文：<https://betterprogramming.pub/you-dont-have-to-compare-flutter-and-react-native-anymore-15ddc4c1342a>

## React Native 修复了主要的性能问题，并与 Flutter 等同

![](img/2e5b0a2186ea099b638bd92ad9bda813.png)

由 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Alexandru Acea](https://unsplash.com/@alexacea?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄，Canva 编辑。

Flutter 和 React Native 是现代跨平台移动应用程序开发最流行的框架。毫无疑问，基于原生平台特定 API 的开发仍然是构建流畅且性能良好的移动应用程序的技术最佳方法。然而，开发团队为每个移动操作系统维护单独的原生代码库既耗时又麻烦。因此，现在每个开发团队要么选择 Flutter，要么选择 React Native 来构建移动应用。

然后，像任何其他框架或技术堆栈一样，我们问自己一个问题。哪个更好？—颤动或自然反应。一些开发人员可能会投票给 Flutter，因为它具有近乎原生的性能和丰富的 widgets 工具包。与此同时，一些开发人员喜欢 React Native，因为它对开发人员友好，并且支持特定于平台的小部件。

由于极快的 Dart 到本机二进制通信，Flutter 应用程序的性能比 React 本机应用程序有了很大的提高。React Native 通过 JavaScript 桥与本机 API 通信。JavaScript 桥概念并不能很好地满足所有的开发需求。

因此，开发人员社区开始批评 React Native，因为它在低端设备上表现不佳，并且由于高于平均水平的资源使用率而很快耗尽电池。现在，React Native 正在通过重新设计其核心模块来解决主要的性能问题。让我们讨论一下新的 React 原生架构如何与 Flutter 竞争。

# Flutter 如何比 React Native 更好地解决性能问题

由于两个主要原因，Flutter 变得越来越流行:性能和灵活性。特定于平台的 UI 控件对于现代用例以及自定义可用性增强是有限的。例如，在没有第三方库的情况下，很难在 iOS 中实现侧菜单，因为侧菜单不是苹果 UX 指南的一部分。Flutter 不使用原生平台特定的 UI 元素，但它提供了自己的包含电池的 UI 工具包，因为 Flutter 附带了嵌入式 2D 渲染库( [Skia](https://github.com/google/skia) )。

React Native 根据 React 组件的状态变化呈现本机平台特定的 UI 元素。React Native 的 JavaScript 代码运行在一个 JavaScript 引擎内部，JavaScript 上下文通过一个桥与本机代码(原生主机 app)通信。JavaScript 桥使用基于 JSON 的异步通信方法。如果不使用 [Hermes](https://github.com/facebook/hermes) ，React Native runtime 会用 [JavaScriptCore](https://github.com/WebKit/webkit/tree/main/Source/JavaScriptCore) (JSC)解释 JavaScript 捆绑包。因此，当你频繁使用原生 API 并添加更多的 JavaScript 代码时，应用程序会运行缓慢并降低可用性。

Flutter 既没有使用 JavaScript 桥，也没有对生产应用进行 JIT 编译。Flutter engine 的 Dart-to-Native 通信使用更快的二进制内存缓冲。

# React Native 比 Flutter 表现更好的区域

当你用 Flutter 做一个跨平台的应用，它就变成了一个 Flutter app，而不是一个特定平台的原生 app。Flutter 应用有不同的 UI 规范，并为最终用户提供不同的用户体验。另一方面，React Native 提供了一种通过跨平台开发人员接口(React)使用特定于平台的原生 UI 元素的方法。在大多数情况下，我们使用跨平台框架，因为本地应用程序开发非常耗时，而不是因为特定于平台的 GUI 工具包的限制。但是，Flutter 给我们的是 Flutter 应用，而不是 React Native 这样的原生应用。

有了跨平台布局系统，应用开发变得更容易。这就是为什么 Electron 成为构建跨平台桌面应用程序的首选。它使用 HTML 作为跨平台布局系统。同样，React Native 也提供了基于 flexbox 的跨平台布局系统来呈现特定平台的 UI 元素。另一方面，Flutter 没有一个前端开发人员友好的布局系统，它提供了一个新的基于树的布局，这有时会让来自 web 开发背景的现代开发人员感到困惑。

此外，Hermes 的浏览器 API-like JavaScript 支持，React-powered 开发环境使 React Native 的开发人员生产率优于 Flutter。

# React Native 的新架构如何解决性能问题

React Native 的主要缺点是在中端和低端移动设备上缺乏性能。以前，基于 React-Native 的应用程序启动速度很慢，因为 JavaScriptCore 运行时在应用程序启动时解释 JavaScript 包。后来，React 原生团队构建了 Hermes，并通过为 JavaScript 包引入 AOT(提前)编译来加快应用程序的启动时间。Hermes 的 AOP 概念有助于 React 本地应用通过减少资源使用来降低电池消耗。由于这些性能增强，React Native 将很快使用 Hermes 作为所有新项目的默认 JavaScript 引擎。

但是，JavaScript bridge 并不适用于所有开发情况，因为它通过序列化 JavaScript/原生对象来使用基于 JSON 的异步通信。React Native 升级了其当前架构，以解决这些性能问题。现在，一个名为 [JSI](https://github.com/facebook/react-native/blob/main/ReactCommon/jsi/jsi/jsi.cpp) (JavaScript 接口)的新组件取代了当前的桥模块。JSI 通过在 JavaScript 上下文中保存本机引用，允许 JavaScript 上下文直接调用本机方法，而无需桥接。此外，它让 React Native 的本机上下文直接操作 JavaScript 对象。

新架构的 UI 事件通信(也称为[结构](https://github.com/react-native-community/discussions-and-proposals/issues/4))使用 JSI。新的架构还提供了一个新概念，通过 TurboModules 来延迟加载本地模块。TurboModules 将进一步加快应用程序的启动时间。

正如你所看到的，新的架构解决了 React 本地应用目前面临的几乎所有性能问题。React 本机新架构实现尚未发布，但它是开源的。Meta 已经用他们的生产应用测试了这个架构。

# 结论:哪个更好？

没有所谓的“最佳跨平台框架”——每个框架都擅长特定的用例。然而，早些时候，许多开发人员认为，由于其性能和灵活性，Flutter 是有史以来最强大的跨平台框架。我用下面的故事解释了为什么 Flutter 比 React Native 好:

[](/5-reasons-why-flutter-is-better-than-react-native-cf2e9b077f66) [## Flutter 优于 React Native 的 5 个原因

### 选择 Flutter 作为下一个跨平台应用的理由，以及对两种框架的深入研究结果

better 编程. pub](/5-reasons-why-flutter-is-better-than-react-native-cf2e9b077f66) 

React Native 的新架构将使 React Native 和 Flutter 的比较分数相等。最后，我们可以把 React Native 和 Flutter 的比较看作是 JavaScript/React 和 Dart/MaterialUI 的比较。或许，只是开发者体验的问题。现在，我们可以忽略性能比较因素，只关注我们正在构建什么来选择一个更好的框架。

换句话说，如果你需要一个定制的 GUI，使用 Flutter。如果您需要原生 GUI，请使用 React Native。否则，如果你喜欢飞镖，使用颤振，否则使用反应原生。

感谢阅读。