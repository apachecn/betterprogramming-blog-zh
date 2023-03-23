# Flutter 优于 React Native 的 5 个原因

> 原文：<https://betterprogramming.pub/5-reasons-why-flutter-is-better-than-react-native-cf2e9b077f66>

## 选择 Flutter 作为下一个跨平台应用的理由，以及对两种框架的深入研究结果

![](img/512739104bf449cd27749f74c7738915.png)

桑迪·米勒在 [Unsplash](https://unsplash.com/s/photos/flutter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如今程序员有两种竞争的跨平台应用开发选择: [Flutter](https://flutter.dev/) 和 [React Native](https://reactnative.dev/) 。我们可以使用这两个框架来构建跨平台的移动应用和桌面应用。这两个框架在外观和可用特性上看起来确实很相似。希望你已经看过很多关于 Flutter 和 React Native 的比较和评论。很多开发者认为 Flutter 不会被广泛使用，因为它使用了一种不熟悉的编程语言， [Dart](https://dart.dev/) 。编程语言只是开发者与框架交互的接口。

一个特定的框架如何解决跨平台开发问题，比特定框架的编程语言的流行程度更重要。我对 Flutter 和 React Native 的内部架构做了一些快速的研究。此外，我使用这两种框架在各种平台上创建了几个应用程序。最后，我发现如果你用 Flutter 开发下一个很棒的应用程序，会有以下好处。

# 颤振具有接近自然的性能

如今，由于强大的设备，性能被低估了。然而，用户拥有各种规格的设备。一些用户可能试图在运行许多其他应用程序的同时运行您的应用程序。您的应用程序应该在所有这些条件下都能正常工作。因此，在现代跨平台应用程序中，性能仍然是一个至关重要的因素。毫无疑问，一个没有任何框架编写的应用比 Flutter 和 React 原生应用表现更好。但是我们经常不得不选择跨平台的应用程序框架来快速交付特性。

典型的 React 原生应用有两个独立的模块:原生 UI 和 JavaScript 引擎。React Native 基于 React 状态更改呈现本机平台特定的 UI 元素。另一方面，它使用一个 JavaScript 引擎(在大多数场景中是 [Hermes](https://github.com/facebook/hermes) )来运行应用程序的 JavaScript。每个 JavaScript 到本机和本机到 JavaScript 的调用都要经过一个 JavaScript 桥，类似于 Apache Cordova 的设计。React Native 以静默方式将您的应用程序与 JavaScript 引擎捆绑在一起。

Flutter 应用没有任何 JavaScript 运行时，Flutter 使用二进制消息通道在 Dart 和本机代码之间建立双向通信流。由于这种二进制消息协议和 Dart 的超前(AOT)编译过程，Flutter 为从 Dart 调用本机代码提供了接近本机的性能。当存在高于平均水平的本机调用时，React 本机应用程序的性能可能会很差。

# Flutter 应用拥有一致的用户界面

React Native 呈现特定于平台的 UI 元素。例如，如果您在 Apple 移动设备上运行应用程序，您的应用程序会呈现本机 iOS UI 元素。每个平台都为其 UI 元素定义了独特的设计概念。有些平台有其他平台没有的 UI 元素。因此，如果使用 React Native，即使是简单的 UI 更改也需要在多个平台上进行测试。此外，您无法克服特定于平台的 UI 元素的限制。

Flutter SDK 定义了自己的 UI 工具包。因此，你的 Flutter 应用在每个操作系统上看起来都是一样的。与 React Native 的特定于平台的 UI 元素不同，Flutter 团队可以向每个 UI 元素引入新功能。多亏了 flutter-theming，你可以根据用户在特定操作系统上的设置来改变应用的主题。

几乎所有的现代应用都是从应用的设计理念来展示自己的品牌。Flutter 致力于通过一致的 GUI 层在所有支持的操作系统上构建一致的用户体验。

# Flutter 提供了一个高效的布局系统

React Native 有一个基于 FlexBox 概念的布局系统，使用 [Yoga](https://yogalayout.com/) 布局引擎创建。所有 web 开发人员和 UI 设计人员都熟悉 CSS FlexBox 样式。React Native 的布局语法类似于 CSS FlexBox 语法。很多开发人员经常纠结于高级的 CSS 样式，他们经常让团队的 UI 开发人员来修复 CSS。因此，如果你使用 React Native 来制作你的下一个应用程序，你需要雇用一名 UI 开发人员或要求移动开发人员熟悉 CSS FlexBox 语法。

Flutter 有一个基于 widget 树的布局系统。换句话说，Flutter 开发人员通常通过覆盖`[build](https://api.flutter.dev/flutter/widgets/StatelessWidget/build.html)`方法，在类似渲染树的数据结构中定义小部件。他们可以想象每个小部件在屏幕上的呈现方式。如果您选择 Flutter，现有开发人员不需要额外的 UI 开发人员或 FlexBox 经验。即使是后端工程师也能很快熟悉这种小部件树的概念，而不是 FlexBox 的概念。

由于 Flutter 基于树的布局系统，您可以提高跨平台应用程序的功能开发速度。当应用程序布局变得复杂时，程序员可以通过将小部件分配给不同的 Dart 变量，将它们分组到不同的部分。

# Flutter 官方支持所有流行平台

React Native 官方仅支持 Android 和 iOS 平台。然而，React Native 有几个支持桌面平台的分支。例如，Proton Native 从 React 原生代码库生成基于 Qt 和 wxWidgets 的跨平台桌面应用程序。但是质子原生现在已经不积极维护了，还有一个它的积极分叉:价原生。

此外，微软还维护了两个 React 原生分支:`[react-native-windows](https://github.com/microsoft/react-native-windows)`和`[react-native-macos](https://github.com/microsoft/react-native-macos)`。如果您希望为现有的 React 本机应用程序构建一个桌面应用程序，有几个选择。每一个流行的 React 原生库都不支持所有这些分叉。此外，还没有针对 Linux 的全功能 React 原生 fork。

Flutter 官方支持 Android、iOS、Linux、Windows、macOS、Fuchsia 和 Web。所有支持的操作系统都使用相同的渲染后端， [Skia](https://skia.org/) 。Flutter 通过提供高性能的 Dart-to-Native 二进制通信机制和折衷的文档，激励所有插件开发者为所有平台添加实现。因此，几乎所有流行的 Flutter 插件都可以在所有支持的平台上工作。

# 你的 Flutter 应用将在 Fuchsia 上运行

大概你已经知道谷歌正在从零开始开发新的操作系统 Fuchsia。基于微内核架构的锆石内核为 Fuchsia 提供动力。据[维基百科](https://en.wikipedia.org/wiki/Google_Fuchsia)报道，谷歌的想法是让 Fuchsia 成为一个通用的操作系统，支持几乎所有设备(包括数字手表和交通灯系统等嵌入式设备)。谷歌正在从所有现有平台的许多学习中构建 Fuchsia。因此，Fuchsia 在操作系统市场上获得成功的概率更大。

Fuchsia 正在实现 Starnix 模块，以便在 Fuchsia 内部运行 Linux 二进制文件。Starnix 模块仍然是一个非常实验性的模块，根据它的设计[文档](https://github.com/vsrinivas/fuchsia/tree/master/src/proc/bin/starnix#starnix)。显然，他们试图通过在类似 Docker 的容器中运行 Linux 内核来运行 Linux 二进制文件。因此，您的 React 原生应用程序无法在 Fuchsia 上作为真正的原生应用程序运行。如果有人希望为 React Native 添加一个 Fuchsia 后端，有人需要制作另一个类似`react-native-windows`的 fork。

Flutter SDK 可能会成为 Fuchsia 上默认的 GUI 应用程序开发工具包。因此，您的 Flutter 应用程序将在 Fuchsia 上自然工作。

# 结论

React 原生项目比 Flutter 项目早两年，整个 React 社区都支持它。Flutter 的社区仍然是新的，并且在不断发展，因为 Flutter 不使用 Angular，而且 Dart 不是像 JavaScript 那样早期流行的通用编程语言。我们仍然无法[将 Flutter 的特性](/stop-comparing-flutters-current-stage-with-other-matured-frameworks-fcdbcf1e204b)与其他成熟的跨平台框架进行比较。但是 Flutter 通过最有效的方法解决了跨平台问题。

这两个框架都运行在本地主机应用程序之上。React Native 由于其基于 JavaScript 运行时的架构，无法像 Flutter 一样提高性能。尝试用 Flutter 构建 app，不要因为认为 Dart 是一门陌生的语言而感到害怕。