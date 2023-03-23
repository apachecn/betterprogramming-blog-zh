# React 原生 0.64 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-react-native-0-64-3d25979829d4>

## 了解此版本的新增功能和影响

![](img/07cbab12dc67c079aa39124112ccc30a.png)

拉胡尔·查克拉博蒂在 [Unsplash](https://unsplash.com/s/photos/smartphone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

React Native 0.64-rc1 在几天前发布了，稳定版很快就会推出。让我们来看看这个新版本给我们带来了什么变化。

# iOS 上的爱马仕

[Hermes](https://hermesengine.dev) 是一个开源的 JavaScript 引擎，针对在 Android 上运行 React 原生应用进行了优化。对于许多应用程序来说，启用 Hermes 将会缩短启动时间，减少内存使用，并缩小应用程序的大小。

从 0.60.4 版本开始，Hermes 就可以在 Android 上使用了。但在这个新版本中，你也可以在 iOS 上使用 Hermes。

# 将 Hermes 跟踪配置文件转换为 Chrome 格式

我们继续使用 Hermes，使用一个新命令将 Hermes 采样分析器下载到本地机器。

当然，这不是什么新鲜事。你已经可以[描述你在 Hermes 上运行的 React Native](https://reactnative.dev/docs/next/profile-hermes) 应用了。但是这个新命令帮助用户将一个 Hermes 跟踪配置文件转换成 Chrome 跟踪配置文件，并将其拖到本地机器上。

```
react-native profile-hermes [destinationDir] <flag>
```

所有这些细节都可以在 GitHub 的 [PR 上找到。](https://github.com/react-native-community/cli/pull/1246)

# 默认情况下，内联要求已启用

在 React Native 0.59 中，测试版中发布了一个名为 Inline Requires 的新功能。它允许 Metro 识别可能懒惰的组件，从而根据需要加载必要的资源，而不是减慢启动。你可以在 React Native 博客上找到对它的解释。

从 0.64 版开始，默认情况下将启用该功能。这将允许许多应用程序无需做任何事情就能获得性能。很酷吧。

# 使用 React v17

在这个新版本中，React Native 使用 React 的最新主要版本。

但是如果你已经自学了一点，你就会知道 React 的这个新版本并没有带来什么特别的东西——它只是一个垫脚石版本。
让我们耐心等待其他版本吧！

# 和许多其他的东西

Hermes 现在将有代理支持，支持的最低节点版本现在是 12，建议使用 Xcode 12——这些只是最重要的变化。

这个新版本还附带了许多其他的小东西，它们可能会影响到你。检查[完整变更日志](https://github.com/react-native-community/releases/blob/master/CHANGELOG.md)以查看所有的变更。

如果你还没有使用 0.63 版本，我邀请你阅读我的文章“[React Native 0.63 的新特性](https://medium.com/better-programming/whats-new-in-react-native-0-63-d17b710368b5)

# 来源

*   [React 原生版](https://github.com/facebook/react-native/releases)
*   [爱马仕](https://github.com/facebook/hermes)
*   [将 Hermes 采样分析器添加到本地机器后，本地社区公关做出反应](https://github.com/react-native-community/cli/pull/1246)
*   [“发布 React Native 0.59”通过 React Native 博客](https://reactnative.dev/blog/2019/03/12/releasing-react-native-059#-faster-app-launches-with-inline-requires)