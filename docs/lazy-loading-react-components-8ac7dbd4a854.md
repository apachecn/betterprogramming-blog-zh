# 延迟加载 React 组件

> 原文：<https://betterprogramming.pub/lazy-loading-react-components-8ac7dbd4a854>

## 通过代码拆分优化生产版本

![](img/ee618f599e53f591f27ce6b9bd6e5dbe.png)

照片由[在](https://unsplash.com/@drew_beamer?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上绘制的光束器

当涉及到减少构建规模时，代码分割是一个非常有用的工具。它允许您将代码解析(或拆分)成不同的包，这些包可以根据当前的需要有条件地加载。

假设您有一套适用于不同班级和不同教育水平的电子学习应用程序。您可以将每个应用程序的所有代码编译成一个构建文件，当您点击登录页面(包含所有应用程序的链接)时，该文件将提供给浏览器。

只要您的构建文件不太大，这种编译方法并不是一个坏主意。但是，如果您向项目中添加新的特性、应用程序或包，那么这个包文件可能会变得非常大。

如果您的构建文件太大，它会降低 React 应用程序的加载时间。这可能会对用户体验产生负面影响。你不讨厌缓慢的加载时间吗？

想象一下:你有一个非常小的用于登陆页面的捆绑包文件(它只需要呈现无数电子学习应用的链接),然后是每个应用的其他小捆绑包，如果你进入一个特定的应用，它们只需要*加载*。

当你只想使用英语 I 时，为什么需要英语 II(或套件中的其他应用程序)的包文件？代码分割可以通过根据您的入口点分离出相关的代码来补救这种情况，从而从主包中取出代码并减小其大小。

在本文中，我将介绍如何使用内置的 React 函数和组件实现代码拆分，而不需要库或额外的配置。

首先，这里是我们应用程序的所有路线:

通过添加一个内置的 React 函数和组件，我们只需两步就可以将上面代码的捆绑包拆分成单独的捆绑包。下面是我们将要使用的函数，名为`React.lazy`。

# 懒惰的

`React.lazy`函数允许您将动态导入作为常规组件呈现。典型的导入可能如下所示:

```
import Home from './Home';
```

如果使用`React.lazy`，您的导入看起来会是这样:

```
const Home = React.lazy(() => import('./Home'));
```

我们的第二步涉及到`Suspense`组件。

# 焦虑

通过在`Suspense`中包装惰性加载的组件，我们可以在等待惰性组件加载时显示一些后备内容(比如加载指示器)。见下文:

```
<Suspense fallback={<div>LOADING</div>}>
  {ROUTES}
</Suspense>
```

让我们看看更新后的`App.js`组件，包括我们对`lazy`和`Suspense`的使用:

就是这样！现在，您已经将主包分成了多个更小的包，只有当路由与延迟加载组件的路由相匹配时，这些包才会被加载。当单独的包被提供给浏览器时，`Suspense`组件充当占位符，向用户保证应用程序将随时可用。

您的新构建文件可能类似于`1.js`、`2.js`、`3.js`等。除了原始/主构建文件之外。

用`lazy`和`Suspense`进行代码分割可能是在不重新配置的情况下减少包大小的最简单的方法。不过，我建议你也重新配置你的捆扎机，以减少更多的建设规模。但是还有其他方法可以减少构建规模，包括更多的重新配置。

阅读[这篇文章](https://medium.com/@rajaraodv/two-quick-ways-to-reduce-react-apps-size-in-production-82226605771a#:~:text=js%20(the%20dev%20version)%20for,is%20just%20libraries%20in%20node_modules!)了解更多关于如何使用 webpack 和 gzip 减小包大小的信息。

本文的源代码可以在 [GitHub](https://github.com/macro6461/medium-react-lazy-load) 上找到。

[*点击此处，将您的免费媒体会员升级为付费会员*](https://matt-croak.medium.com/membership) *，每月只需 5 美元，您就可以获得数千名作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！*

# 参考

[](https://reactjs.org/docs/code-splitting.html) [## 代码分解-反应

### 大多数 React 应用程序会使用 Webpack、Rollup 或 Browserify 等工具“捆绑”文件。捆绑是一个过程…

reactjs.org](https://reactjs.org/docs/code-splitting.html) [](https://medium.com/@rajaraodv/two-quick-ways-to-reduce-react-apps-size-in-production-82226605771a) [## 减少 React 应用程序生产规模的两种快速方法

### 如果您正在构建一个包含 Webpack 的 React + Redux 应用程序，那么您可能已经注意到 final 的大小…

medium.com](https://medium.com/@rajaraodv/two-quick-ways-to-reduce-react-apps-size-in-production-82226605771a) [](https://github.com/macro6461/medium-react-lazy-load) [## macro 6461/中等反应惰性负载

### 这是我的帖子“Lazy AF(Lazy Loading React Components)”的源代码，GitHub 拥有超过 5000 万个

github.com](https://github.com/macro6461/medium-react-lazy-load)