# 当反应过度时，AlpineJS 会起作用

> 原文：<https://betterprogramming.pub/when-react-is-too-much-alpinejs-will-do-the-trick-c165a2da4701>

## 如果你对 React 感到不知所措，读读这个

![](img/b0b8a9b78aa1c73355b1b4b2f6f6486a.png)

照片由在 [Unsplash](https://unsplash.com/s/photos/hammer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

React 是世界上最流行的前端框架。它非常健壮，功能丰富，但是对于较小的项目来说可能有点太多了。这就是 AlpineJS 发挥作用的地方。它比 React 更小、更快、更容易学习，这使得它非常适合特定的用途。

# 什么是反应？

我相信您非常清楚 React 是什么，但是现在还不要跳过这一部分。我将使用这些定义来比较 React 和 AlpineJS，以便弄清楚它们到底解决了什么问题。

![](img/9914d3d718fd6b26c2df215ac2881476.png)

图片由 [janjf93](https://pixabay.com/users/janjf93-3084263/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3614766) 发自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3614766) 。

正如脸书所说，React 是一个“用于构建用户界面的 JavaScript 库”这就是了。React 不是一个框架，它不是一个用于构建应用程序或网站的库。它是一个构建接口的库。其余的特性来自第三方库(如 [redux](https://github.com/reduxjs/react-redux) )、第一方库(如 [react-native](https://github.com/facebook/react-native) )或原生绑定(浏览器 API 或 Android/IOS API)。因此，出于本文的目的，我将只考虑 React 的界面构建功能。

那么，React 是如何构建接口的呢？它通过将 JSX 代码转换成普通的 JS 代码来实现这一点——从技术上来说， [transpiler 会这样做](https://babeljs.io/docs/en)——使用这些代码来构建[虚拟 DOM](https://github.com/Matt-Esch/virtual-dom) ，并使用[复杂的协调算法](https://reactjs.org/docs/reconciliation.html)将其应用到实际的 DOM。

虽然这听起来令人困惑，但这是一个高度优化的过程，需要一些时间来完成。此外，随着[异步渲染](https://reactjs.org/blog/2019/11/06/building-great-user-experiences-with-concurrent-mode-and-suspense.html)的引入，性能将进一步提高。

# 什么是 AlpineJS？

![](img/71e65c067810a82a6a235dcc69159c31.png)

[AlpineJS](https://github.com/alpinejs/alpine) 也是一个用于构建用户界面的库。它吹嘘“像 Vue 或 React 这样的大型框架的反应性和声明性本质，而成本却低得多”，但是它到底如何实现它的承诺呢？

秘密在于 AlpineJS 不使用虚拟 DOM，而是完全在实际的 DOM 中操作。您可以跳过传输文件的步骤、渲染过程和协调算法。我认为这是一个迷人的黑客，我对此感到非常兴奋。

不仅引擎盖下简单，使用起来也非常简单。只有 13 个*指令*(想道具)和 5 个*魔法属性*(想钩子，但不完全是)。整个库的文档都包含在 GitHub 上的 [readme 中。](https://github.com/alpinejs/alpine)

因为您从 DOM 操作 AlpineJS，所以您直接用 HTML 编写您的逻辑代码，根本不需要 JS 文件。这是一个有争议的做法，但我还是赞成的。

# 为什么要选择 AlpineJS？

1.  AlpineJS 非常适合小型轻量级项目。最明显的例子是简单的 UI 元素，比如下拉菜单、抽屉、模态等等。该库占地面积小(React 中为 5KB，而 React 中为 34.8KB)且简单，这将使您的移动速度更快，并让您的客户满意。
2.  只需导入脚本，AlpineJS 就可以轻松集成到任何现有的网站/应用程序中。这意味着您可以将客户端渲染引入到您的传统服务器端渲染网站(ASP。NET，WordPress 等。)对代码库几乎没有改变。
3.  AlpineJS 和 React 都是为构建接口而设计的，但在我看来，AlpineJS 以一种更直接的方式实现了这一点。
4.  你会喜欢的。如果你觉得你刚刚和 React 一起工作了一段时间，需要一些新鲜空气，给 AlpineJS 一个机会。

# 为什么不应该选择 AlpineJS？

1.  你知道一个事实，你正在建立一个大的，面向生产的项目。AlpineJS 使用起来非常有趣，但是它的设计并没有考虑到大型项目(事实上正好相反)。
2.  你需要移动支持。React 有 react-native，而 AlpineJS 没有。就这么简单。
3.  AlpineJS 和 React 都是为了构建接口而设计的，但是 React 可以扩展到构建任何东西(甚至是 [VR 体验](https://facebook.github.io/react-360/))，而 AlpineJS 则不能(目前)。
4.  你不喜欢开发或学习新东西。虽然 React 有一个庞大的社区和企业参与，并且已经存在了很长时间，但 AlpineJS 是新出现的。它没有为所有事情编写的库，也没有解释如何解决你遇到的每个问题的教程。如果你用 AlpineJS 开发，你要比用 React 开发时更依赖自己。

# 有没有用 AlpineJS 写的项目？

虽然我找不到任何，但有一些用阿尔卑斯山写的很酷的演示。以下是要检查的代码笔:

*   [带有下拉菜单的示例页面](https://codepen.io/bennash/pen/mdyamBz)
*   [待办事宜 app](https://codepen.io/tlgreg/pen/vYEbGMV)
*   [列表/网格布局](https://codepen.io/brbcoding-the-selector/pen/RwNjXaK)

# 代码示例

现在我将向您展示几个例子，这样您就可以对 AlpineJS 的开发有一个大致的了解。

## 数据绑定

任务很简单:询问用户的姓名并问候他。使用 React:

使用 AlpineJS:

## 事件

下面是 React 处理各种事件的方式:

使用 AlpineJS:

## 条件渲染

这是您在 React 中进行条件渲染的方式:

使用 AlpineJS:

你可以看到 Alpine 在每个例子中都有更少的代码行。这种趋势会随着您的成长而继续，但只会持续到某个时候，直到 React 的可重用特性(可重用组件)开始发挥作用。

# 我如何开始？

有两种方法可以开始。如果你想先阅读一个教程，并舒服地试用它， [Alpine Toolbox 有一个极好的摘要](https://www.alpinetoolbox.com/guides/)。如果你想直接进入开发并开始构建一个项目，[这个 repo 有一个初学者工具包](https://github.com/bep/hugo-bulma-hero)(想想`create-react-app`，但是对于 Alpine)。

# 结束语

感谢您的阅读。我希望你喜欢这首曲子。请在评论中告诉我你对 AlpineJS 的看法，以及你用它做了哪些很酷的项目！

# 资源

*   [从 AlpineJS 开始](https://dev.to/nugetchar/starting-with-alpinejs-hjn)
*   [AlpineJS Github](https://github.com/alpinejs/alpine#x-bind)
*   [阿尔卑斯工具箱](https://www.alpinetoolbox.com/)