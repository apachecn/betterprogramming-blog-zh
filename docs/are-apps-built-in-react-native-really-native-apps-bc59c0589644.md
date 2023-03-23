# React Native 内置的应用真的是原生应用吗？

> 原文：<https://betterprogramming.pub/are-apps-built-in-react-native-really-native-apps-bc59c0589644>

## RN app 和 Swift app 有什么区别？

![](img/bf2b41befdb13f0ce59b708fdfe6e816.png)

图多尔·巴休在 [Unsplash](https://unsplash.com/s/photos/react?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

React Native 已经和我们在一起有一段时间了。它于 2015 年 3 月推出，这意味着本月它将迎来五岁生日。三年前，当它还处于初始阶段时，我就开始研究它了。它变得流行有三个主要原因:

1.  Airbnb 正在使用它，他们在[媒体](https://medium.com/airbnb-engineering/tagged/mobile)上写了关于它的博客。
2.  它由脸书*使用并开源。
3.  React 是一个很棒的 JavaScript 库。

**需要澄清的是，脸书并不是全部都是用 React Native 构建的，只是应用的一部分。*

我不知道我是如何在一个使用 React Native 近三年的项目中结束的。最棒的是，我不能说我后悔，因为那次经历让我走出了舒适区，让我成为了一名更好的开发人员。身处移动世界的两端给了我一个更大的视角，直到今天，我一直看到许多公司愿意使用或已经在使用 React Native。

如果你有疑问，只要去 AngelList 这样的初创公司页面，你就会看到有多少初创公司在寻找 React 原生工程师。

# React Native 是什么？

首先，我们应该看看 React Native 的定义。问题是，如果你看他们的网站和文档，没有关于部分或正式的定义。我能找到的最接近它的是 GitHub 库[的介绍行](https://github.com/facebook/react-native):

> “React Native 为 iOS 和 Android 带来了 [React 的](https://reactjs.org/)声明式 UI 框架。有了 React Native，您可以使用本机 UI 控件，并拥有对本机平台的完全访问权限。”

所以基本上，React Native 是一个 JavaScript 库，允许你使用原生 UI 控件和原生平台中的一切。

![](img/626d945c958fb9a7cb3c5d594d72dca7.png)

由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/react?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 反应原生状态，“通过它们构建的移动应用与‘真正的’移动应用没有区别”

以上摘自 [React Native 的网页](https://react-native.org/)——以下是完整的引用:

> “有了 React Native，你不用构建‘移动网络应用’、HTML5 应用’或‘混合应用’。”您构建了一个真正的移动应用程序，它与使用 Objective-C 或 Java 构建的应用程序没有什么区别。React Native 使用与常规 iOS 和 Android 应用相同的基本 UI 构建块。您只需使用 JavaScript 和 React 将这些构件放在一起。"

我已经做了八年的移动开发者，直到今天，我都不知道什么是“真正的”移动应用。据我所知，App Store 里的所有应用，不管是什么技术，都是真实存在的。

他们这么说的意思是，他们的大多数 React 组件在 Objective-C 或 Java 中都有对等物，当应用程序呈现时，结果是本机代码。

# 真的比开发原生代码快吗？

在科技行业和我们整个社会中，人们对速度有点痴迷。像移动应用这样的项目是复杂的。我们忽略了这样一个事实，即开发人员正在精心制作一些东西，而速度是一个主观的概念。

![](img/083302f24cbbeaf0cf356b1cc095a802.png)

但是除了*快*或*慢*之外，从更专业的角度来说，React Native 并不比它更快，它只是让你能够用相同的代码将你的应用程序部署到两个平台(也可能是其他平台)上。

但当涉及到软件开发中最耗时的 bug 修复活动时，问题将在 Android 和 iOS 中分别找到。根据我使用 React Native 的经验，在两者中部署所节省的时间将会浪费在修复特定于平台的错误上。

# 你只需要一个懂 JavaScript 的开发者，他们就能完成工作

在我在 React Native 中的第一个项目之后，我帮助建立了其他 React Native 团队，还面试了一些关于这项技术的角色。我经常从招聘人员和在 React Native 工作过的人那里听到这样一句话。

> “是的，你需要知道 JavaScript，但我们也希望团队中有人也做过原生。”

![](img/171e0664c3e09735b79400f1a086f6a0.png)

照片由 [Franck V.](https://unsplash.com/@franckinjapan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/mix?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

## 如果 React Native 这么好说话，为什么还需要做过 Native 的人？

1.  Android 和 iOS 的一些原生功能在 React Native 中是不可用的，如果你的需求非常具体，你需要有人用原生代码添加东西，然后将它们桥接到 JavaScript。
2.  原生开发者更了解在 Android 和 iOS 中调试应用的工具。即使 React Native 生成了所有代码，您仍然需要使用本机工具来处理特定的错误。
3.  本地开发人员简单地了解他们的平台。当你每天与一个平台打交道时，你知道它的挫折。他们知道来自一种型号硬件的缺陷。他们知道操作系统更新后会出现的问题。他们凭经验知道什么或哪里需要修理。JavaScript 环境中的 JavaScript 开发人员也是如此——他们就是知道。但在这里你试图将三种不同的侧写混合到一个人身上。

# 如果你知道在选择 React Native 时你在处理什么，这是一个很好的库

像许多移动开发者一样，我曾在许多不同的平台和语言中工作过。我曾经有一个老板，他说伟大的软件工程师能够在大约两周的持续专注学习中掌握一项新技术的基础知识。也许这有点夸张，但是对学习和项目工作有热情的人会度过难关。

![](img/7b9009efa32d459b21f1f36588471a55.png)

丹尼尔·利维斯·佩鲁西在 [Unsplash](https://unsplash.com/s/photos/confusion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

问题不在于技术。开发者也不是问题。问题是，我们一直在寻找快速简单的解决方案，让我们无需努力或投资就能成功度过难关。

那是绝对不会发生的。React Native 是一个替代，是的。如果你想在一个项目中使用它，一定要记住这一点。React Native 是一种替代技术，像其他任何技术一样有优点和缺点——这并不意味着它是一种捷径。

感谢阅读。