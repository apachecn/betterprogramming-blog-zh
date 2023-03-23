# Yew vs VueJS + WebAssembly:搭建 Rust App 哪个好？

> 原文：<https://betterprogramming.pub/yew-vs-vuejs-webassembly-which-is-better-for-building-a-rust-app-8fa6c45c879f>

## 对利弊的讨论

![](img/d523f370735583ea0abc2b614c628edf.png)

图片来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=5291766) 的 [Micha](https://pixabay.com/users/pixxlteufel-117549/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=5291766)

这个故事展示了使用 Rust 开发一个计算工作量大的网站的两种不同方法的比较:(a)使用 [Yew](https://yew.rs/) 全力以赴使用 Rust(和 WebAssembly)或者(b)使用 [VueJS](https://vuejs.org/) 框架和 WebAssembly。

我的经验是后者。它是高性能的，使用全面的工具，并且设置和完成工作相当简单。但是开发人员的体验受到代码库分成 VueJS 和 Rust 部分的影响。

这个故事是对之前没有经验的 Yew 的一个实验，以评估开发人员的体验是否得到了改善，并发现可能的折衷。

本文中开发和展示的源代码可以在 [GitHub](https://github.com/juliendecharentenay/yew-investigation) 上获得。https://www.charentenay.me/yew_investigation/index.html[有现场版。](https://www.charentenay.me/yew_investigation/index.html)

Yew 和 VueJS 之间的比较基于 Fibonacci 序列的第 40 项的计算的实现，如[https://fazlurnu . com/2020/09/18/python-vs-Julia-speed-test-on-Fibonacci-sequence/](https://fazlurnu.com/2020/09/18/python-vs-julia-speed-test-on-fibonacci-sequence/)中所述。

给出了三个实现:(a)普通 VueJS——我想比较 WebAssembly 和 JavaScript 的执行速度，(b) VueJS + WebAssembly，以及(b) Yew。使用 [tailwindcss](https://tailwindcss.com/) 框架。不是为了它的外观和感觉，而是为了研究不使用 NodeJS 生态系统的缺点。

比较着眼于以下标准:

*   开发者体验——这是一个主观标准，这个测试项目非常简单
*   碳足迹——使用[网站碳计算器](https://www.websitecarbon.com/)测量
*   首次渲染和下载大小的时间——使用[网页测试](https://www.webpagetest.org/)测量
*   执行速度—计算斐波那契数列第 40 项的处理时间

# VueJS 和 VueJS + WebAssembly

实现普通的 VueJS/JavaScript 和 VueJS + WebAssembly 网页相当简单，因为我以前有过使用该框架的经验。

从开发者的经验来看，VueJS + WebAssembly 实现的难点在于设置 VueJS 和 Rust/WebAssembly 的边界。在当前的上下文中，计算斐波那契数列的函数调用处有一个自然边界。这种分离将实现分为在 VueJS 中实现的 UI 端和在 Rust 中实现的计算端。

# 紫杉

我开始按照[红豆杉教程](https://yew.rs/docs/tutorial)和[红豆杉样本应用](https://yew.rs/docs/getting-started/build-a-sample-app)撰写。评估斐波那契数列第 40 项的页面实现相当简单，因为页面功能非常基本。

但这足以表明紫杉框架需要一些时间来调整。在我看来，一个缺点在于如何编写应用程序的 HTML 部分。它需要一些操作——没有挑战性，但是有一些认知超载——这使它不同于编写普通的 HTML。例如，需要添加`{ "..." }`来声明纯文本。“hello world”教程采用以下形式:

```
<p>{ "hello world!" }</p>
```

顺风 CSS 的设置如文章[如何用紫杉和树干设置顺风 CSS](https://dev.to/arctic_hen7/how-to-set-up-tailwind-css-with-yew-and-trunk-il9)中所述。这允许将顺风 CSS 编译成单个 CSS 文件，该文件仅包括项目中使用的顺风 CSS 元素，从而减小 CSS 文件的大小。

这个过程必须在 Yew 实现中手工完成，而它是在 VueJS 设置中构建的。一种更简单的替代方法是将`index.html`链接到 tailwind CSS CDN，但代价是导入整个 tailwind CSS 库，增加下载文件的大小，以及第一次渲染的时间变慢。

我在使用 Yew 时遇到了以下限制——但是请考虑这是基于在这个项目中获得的非常有限的经验，一个更有经验的人可能知道如何解决这些问题。

# 测试

测试铁锈很简单。但是我还不知道如何对 HTML 逻辑进行单元测试——我承认[文档](https://yew.rs/docs/more/testing)声明，“我们正在努力使组件测试变得容易，但是这是目前正在进行的工作。”https://github.com/yewstack/yew/issues/1413 的[中已经强调了对这一功能的需求。](https://github.com/yewstack/yew/issues/1413)

# 多页

我不太喜欢单页应用程序。Yew 为单页应用程序提供了一个路由器，但没有提供从一个板条箱生成多页的“开箱即用”选项。https://github.com/yewstack/yew/issues/1060 的[中描述了一些选项，将每一页分割成一个单独的板条箱，或者使用 cargo 的功能。](https://github.com/yewstack/yew/issues/1060)

# 比较

斐波纳契数列第 40 项的计算实现使用以下指标进行比较:

*   开发人员体验——与我对在该框架中开发所获得的快乐和满足感的感受相关——这是非常主观的，需要有所保留
*   speed-报告计算斐波那契数列第 40 项的执行速度。在所有实现中，执行速度都是使用 web [性能 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance) 的全局实例来计算的
*   开始渲染和大小—使用 [WebPageTest](https://www.webpagetest.org/) 站点性能测试进行评估，配置如下:位于美国弗吉尼亚州的带电缆连接的台式机
*   CO2 —估算与单页视图相关的碳排放量。这个估算是使用[网站碳计算器](https://www.websitecarbon.com/)完成的。

下表显示了在[https://www.charentenay.me/yew_investigation/index.html](https://www.charentenay.me/yew_investigation/index.html)部署的三个实施选项的比较——由 AWS S3 托管并通过 CloudFront 提供服务:

```
+----------------+-----------+--------------+----------+
  |   Option       |   VueJs   |  VueJs/Wasm  |    Yew   |
  +----------------+-----------+--------------+----------+ 
  |  Experience    |     +     |       =      |     +    |
  +----------------+-----------+--------------+----------+
  |  CO2           |   0.02g   |     0.02g    |   0.10g  |
  +----------------+-----------+--------------+----------+
  |  Start Render  |    0.5s   |      0.6s    |    0.9s  |
  +----------------+-----------+--------------+----------+
  |  Size          |     87kB  |       90kB   |    279kB |
  +----------------+-----------+--------------+----------+
  |  Speed         |    3.8s   |       0.9s   |    0.9s  |
  +----------------+-----------+--------------+----------+
```

# 我的感知

我的开发者体验偏向于纯 VueJS 实现，因为它感觉舒适和简单。使用 Yew 框架在 pure Rust 中开发的体验是令人满意的——但它目前有点落后，因为该框架尚未达到 NodeJS 生态系统和 VueJS 提供的成熟度。

我对开发 VueJS/WebAssembly 网页的看法不太满意，因为它需要在 JavaScript 和 Rust 语言之间切换，这增加了一些认知负荷/上下文切换。

从性能角度来看，当算法编译到 WebAssembly 时，Fibonacci 序列第 40 项的计算速度明显加快，而 Yew 和 VueJS/WebAssembly 之间没有明显差异。

从页面呈现的角度来看，VueJS 和 VueJS/WebAssembly 实现的得分与 Yew 实现相似，但略好于后者。与这两种 VueJS 实现相比，Yew 实现在渲染方面确实显示了轻微的滞后。

总的来说，使用紫杉有好处，我可能会迁移一些项目。但是有些方面，即缺乏测试 HTML 逻辑的能力，无法利用 NodeJS 生态系统，以及渲染时间稍慢，可能会让我暂时坚持使用 VueJS/WebAssembly 组合来完成更大的项目。

# 其他可能感兴趣的项目

从 AWS S3/CloudFront 服务 WebAssembly:在这个练习中，以下问题变得很明显。WebAssembly `wasm`文件被上传到 S3，MIME 类型为`octet/binary-stream`——在之前的[故事](https://julien-decharentenay.medium.com/vuejs-rust-webassembly-webworker-hosted-on-aws-s3-an-example-7e917ff5edd2)中已经讨论过。本练习使用 CloudFront 函数来更改 MIME 类型，而不是更改 S3 对象的 MIME 类型，从而解决了这个问题。

使用 tailwind CSS CDN:我最初使用 tailwind CSS CDN 来实现 Yew。这增加了渲染时间，直到它变得显而易见——特别是与其他实现相比，并且考虑到页面的简单性。当使用 tailwind CLI 生成特定于项目的 CSS 文件时，这个问题不太明显。

*感谢阅读！敬请关注更多内容。*