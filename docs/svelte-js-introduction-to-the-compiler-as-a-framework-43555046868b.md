# Svelte.js —作为框架的编译器简介

> 原文：<https://betterprogramming.pub/svelte-js-introduction-to-the-compiler-as-a-framework-43555046868b>

## 一个新兴框架的概述

![](img/d85fc5b0ee6f3bc30364217a79fe4ff0.png)

图片来源: [https://svelte.dev](https://svelte.dev)

**注 1:** 本条有[法文版。](https://medium.com/@nilmanduil/svelte-js-le-compilateur-en-guise-de-framework-5473f1d727f8)

自 2019 年 4 月发布其版本 3 以来，[苗条](https://svelte.dev/)“框架”越来越被人们谈论。由于它的轻便性及其方法与 React 或 Angular 等主要框架不一致，Rich Harris 开发的后起之秀向您承诺简单性和速度，以获得更好的用户和开发人员体验。而你，会被 Svelte.js 诱惑吗？

**注 2:** 本文重点介绍 Svelte.js 的非技术方面，更多技术点可以参考 Svelte 速成班或者参考我的 Pomodoro 应用 POC(文末链接)。

# 先讲一点历史

《纽约时报》的前台开发人员 Rich Harris 在追求性能和良好的用户体验的过程中，在 Mike Taylor 于 2012 年发表的一篇[推文](https://twitter.com/miketaylr/status/227056824275333120)之后，终于意识到“删除 JPEG 以节省加载时间”并不等同于删除相同大小的 JavaScript。

于是在 2016 年，他开始了一个小实验。他的想法是:编写一个 JavaScript 编译器，它能产生高质量的代码，尽可能的轻，没有 DOM 的抽象，有超快的加载时间和非常快的性能。一切都进行得很快。2016 年 11 月中旬，他第一次犯了；四天后，第一个测试版面世，这个测试版十天后，2016 年 11 月 26 日，他发表了[第一篇详细阐述他的概念](https://svelte.dev/blog/frameworks-without-the-framework)的博客文章:Svelte 诞生了。这篇文章发表三天后，v1.0.0 就发布了。然后，在一次大清理期间，2018 年 4 月发布了 v2.0.0，一年后的 2019 年 4 月，一次彻底的检修允许了 3.0.0 版本的发布。

正是有了这个 3.0.0 版本，Svelte.js 开始变得重要起来，被人们谈论，并慢慢地在 JavaScript 世界发起了一场根本性的变革。

# 但接下来是框架还是编译器呢？

好问题。事实上，它有点介于两者之间，至少与目前流行的*技术框架*的定义相比是这样的。

Svelte 提供了一个真正的轻量级开发框架，具有强大的功能和语法糖来促进开发人员的工作，因此使它成为一种框架。

然而，Svelte 有自己的语法，这无疑在 Twitter 上创造了一些讽刺，Vue.js 的创始人尤雨溪推出了 SvelteScript 的讽刺想法。但这些 Svelte 代码随后被 Svelte 编译成普通的 JavaScript(因此使其成为编译器)。简而言之，苗条在边界。

# 好吧，但是苗条怎么会有趣呢？

Svelte 的最大优势是，顾名思义，编译时应用程序的重量非常低，这保证了加载速度，这是 Angular、Vue.js 或 React 等框架无法实现的，这些框架除了嵌入应用程序的逻辑代码外，还嵌入了运行时。有了苗条，你的代码只有适量的苗条，所以你不会超载。

然而，Svelte 在执行过程中也很快。事实上，它编译您的代码，可能比您单独完成的事情更优化(同时使您的苗条代码源文件完全可读和可维护)，尤其是:它没有虚拟 DOM，并且少了一层来反映页面上的变化。

然后，正如我在上一段中提到的，Svelte 给了你一个可读性更好、可维护性更强的面向组件的代码。为什么？首先，因为您将与组件相关的所有内容都放在同一个文件中:逻辑(JavaScript)、结构(HTML)和样式(CSS)——所有内容都放在那里！其次，因为 Svelte 的理念鼓励你写尽可能简洁易懂的代码。

最后，一些小的但是值得注意的优点:因为每个组件的风格都是孤立的，只影响组件，而不影响它的父组件或子组件，所以使用 Svelte 几乎没有图形副作用的风险。最后一件小事，Svelte 兼容 ES2018，所以是的，你可以使用箭头功能，`const`，和其他漂亮的现代 JS 小功能。

# 这很好，但你的奇迹产品，它有一些缺陷，对不对？

是的，生活中没有什么是完美的。首先，最大的问题是它与 TypeScript(我最喜欢的语言，说我是否痛苦！).然而，社区如此要求它，Rich Harris 已经计划将它集成到他的未来开发中，一些成员已经正视这个问题，并开始尝试在实验项目中协调这两者。

【编辑 08/07/2020:svelite is[终于正式兼容 TypeScript](https://svelte.dev/blog/svelte-and-typescript) ！]

然后，我们必须承认，苗条的社区没有 Angular，React，或 Vue.js 的重要，它看起来不太像，像那样说，但要走出一个陷阱，找到已经做好的插件或组件，或有资源进步，这不是很快乐。

最后，语法可能会让更经典框架的爱好者感到困惑。

# 我们可以期待未来的苗条身材是什么样的？

在我看来，最重要的是，Svelte 的未来是更成熟、更大的社区和更容易获得的资源。

但具体而言，有些领域的工作已经确定，有些甚至已经开始。

首先，Rich Harris 开始集成 web 可访问性，这样不同的残障人士就不会因为访问一个用 Svelte(顺便说一下，这提高了 SEO)制作的应用程序而受到太大的伤害。但他也向社区寻求其他大项目的帮助:国际化(一些社区项目是存在的)；命令行界面，或行话中的 CLI，如 Angular CLI 或 create-react-app；与 RxJS 的兼容性；或者如前所述，TypeScript 的集成。

最后，一些奇异的项目将 Svelte 带出了经典 web 应用程序的领域。我们可以列举一些:

*   尝试在智能手机上使用 NativeScript 运行苗条的应用程序，接近 Ionic 或 React Native
*   [Sapper](https://sapper.svelte.dev/) ，一个使用服务器端渲染的衍生框架
*   [GL](https://github.com/sveltejs/gl) ，一个在瘦身中使用 WebGL 的实验

# 走得更远

要更深入地探索这个主题，您可以使用以下一些有趣的链接:

*   苗条的官方网站，特别是官方教程(比文档更好)，官方文档，例子，常见问题，官方博客，最重要的是，最好的沙盒:REPL！
*   【EN】官方的[GitHub 库](https://github.com/sveltejs/svelte)
*   李晓茹的苗条速成班:[文章](https://dev.to/hexrcs/svelte-crash-course-with-pics-27cc)，[视频](https://www.youtube.com/playlist?list=PLyNmzPg2z6OiXylVCmsUOpaXdZm7lrzCt)。信息量很大
*   一份包含大量有用资源的令人敬畏的列表
*   里奇·哈里斯官方推特账号
*   一个[引导](https://medium.com/@teimurjan/guide-to-the-svelte-framework-41d9f84d18ff)在媒体上变苗条，更具技术性的观点
*   与 React 在介质上的技术比较
*   我开发的一个 Pomodoro 应用程序，作为一个 POC: [GitHub](https://github.com/Nilmanduil/SveltePomodoro) ， [Demo](https://public.nilmanduil.now.sh/)

和一些法语资源:

*   [FR] Alexis Jacomy 在 DevFest Nantes 2019 上的大会，更有“数据即”导向的角度:[视频](https://www.youtube.com/watch?v=FY0VkYFZb3k)，[幻灯片](https://slides.com/jacomyal/svelte#/)。我强烈推荐。
*   [FR]发展理论频道的快速介绍:[视频](https://www.youtube.com/watch?v=GvFp9SfcWGo)
*   [FR]一个 Zenika RemoteClazz 更类似于我的观点:[视频](https://youtu.be/b4U2vxdADg4)。我推荐。

# 结论

总之，Svelte.js 是一个越来越受欢迎的框架，尽管它还很年轻，还不完全成熟，但它是一个开放的贡献框架，提供了轻量级、简单性和性能。

在不久的将来，它将不再仅仅是一个 web 应用程序框架，许多开发人员一致认为，它很可能是未来框架的灵感。

简而言之，这项技术在理念、方法和使用的技术上与目前的 star 框架有着根本的不同，它让我着迷，让我觉得它是一个具有巨大潜力的工具，是我们必须关注的工具。

而你，你会动心吗？