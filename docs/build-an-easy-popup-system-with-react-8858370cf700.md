# 用 React 构建一个简单的 Popin 系统

> 原文：<https://betterprogramming.pub/build-an-easy-popup-system-with-react-8858370cf700>

## 揭秘用 React 创建一个简单、可定制和可访问的 popin 系统的方法

![](img/2267b0b06fc176f8cd48b67fd45f271d.png)

# 对现有系统的担忧

有很多 popin 系统，但是它们通常不能满足我对用户界面和开发简单性的高质量要求。

当我将 popin 添加到网站时，对我来说重要的是系统:

*   **简单易用**:作为一名开发者，我不想花时间创建大量的组件和状态来激活弹出窗口。开发人员最好把时间花在领域特性上，而不是无脑的任务上
*   **可定制**:这通常是我的主要复杂点，因为 popin 系统几乎总是带有样式化组件，这使得它们看起来更难像你的 UI 设计师想象的那样。
*   可访问性(Accessible):可访问性通常被从系统中分离出来，因为它需要更多的工作，即使它不需要那么多的工作。

根据这些需求，我总是发现很难找到我需要的库，而且阻塞点常常太痛苦而无法解决。

即使这可能不是直观的，最后一个站得住脚的选择是创建我们自己的系统，这样将确保与我们的需求完美匹配

说够了，让我们开始创建一个 popin 组件系统。

# 我们在建造什么

在这个弹出系统中，我们需要一些东西:

*   将负责 popin 样式的自定义模式组件，包括背景、位置和关闭按钮
*   一个易于使用的模式组件，带有一个简单的切换系统，将负责 popin 的功能部分。
*   使 CSS 模态柔和出现的过渡状态
*   支持人们拥有一个激活了减少运动的浏览器
*   处理模式上的可访问性，以告诉残障人士弹出窗口已经出现，并告诉他们单击哪里，这样弹出窗口将被关闭
*   当我们点击时，一个可点击的背景覆盖关闭弹出窗口
*   使用退出键关闭 popin

有很多事要做，所以我们最好开始做。

# 要求

拥有模态系统的第一件事是要有一个模态根，系统将在那里发生。为此，我们只需要在根文档中有一个新的`div#modal-root`元素。

这一部分很重要，因此可以很容易地设计模态。有了一个单独的根元素，我们就可以确定 modal 的父元素没有样式，这使得我们很难达到完美的样式。

为了确保模态总是在文档的顶部，我们只需要在应用程序根和模态根上添加正确的`z-index`。

此外，由于模态行为将被打开并直接占据整个浏览器的页面，我们向模态系统添加了一个 [ARIA live region](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) ，这样它就可以向用户公布了。

aria-live 区域被设置为 assertive，因为我们希望读者具有与浏览器相同的行为，浏览器将 popin 置于所有其他内容之上。

# 模态分量

模态分量分为三个不同的分量:

*   一个`ModalPortal`组件，它将我们的模态链接到`div#modal-root`元素
*   一个旨在处理组件可见部分的`ModalView`组件
*   一个`ModalAnimated`组件，它将处理 popin 域和 popin 系统的 CSS 外观效果

## ModalPortal 组件

`ModalPortal`组件的存在是为了将我们的弹出窗口链接到我们已经创建的`div#modal-root`元素。

它由四部分组成:

*   一个`ref`对应于一个简单的`div`元素，目标是保存弹出内容。我们不直接使用根元素，所以如果我们想堆叠它们，我们可以创建两个或者更多不同的弹出窗口。
*   创建`div`元素的第一个`useEffect`钩子。这是一种安全措施，使系统也能在诸如 NextJs 或 Gatsby 等 SSR 系统上工作。
*   另一个`useEffect`钩子，用于在活动时将之前创建的`div`添加到门户中，在不活动时将其移除。这将防止`div#modal-root`元素包含大量的空 div。
*   呈现部分，如果创建的`div`元素不存在或者 popin 当前不活动，则该部分为空。

## ModalView 组件

这个基本上是一个布局组件，所以我们可以用我们想要的方式来设计 pop。

即使我只展示了一个模板，您也可以根据需要使用它，例如:

*   人口信息系统
*   原生`alert`和`confirm`模态的设计替代品
*   通知系统
*   无论你能想象到什么

当前组件只是一堆具有某些样式的本地元素，分为两个部分:

*   一个覆盖按钮，所以弹出窗口可以在点击时关闭
*   popin 内容本身，包括一个关闭按钮

这两个块是兄弟，因为我们不希望点击事件从一个传播到另一个。

出于可访问性的原因，覆盖和关闭按钮都是带有`aria-label`属性的本地按钮元素。

在 CSS 部分，我使用了各种 positionnement 技术，您可以根据自己的需要自由调整。

## ModalAnimated 组件

系统的最后一部分，我们需要一个组件，它是一个模态控制器。

该组件有几个任务要处理:

*   它必须加载`ModalView`组件。我选择默认使用`ModalView`组件，但是我也给了这个组件一个道具来改变它
*   它还必须管理模态门户组件，以便将我们的内容包含在`div#modal-root` DOM 元素中
*   它为我们提供了一个退出键来关闭模式。
*   最后，它处理一个非强制性的但很好的过渡效果。

CSS 有一个奇怪的 CSS 模块语法来处理全局类，但也使用`prefers-reduced-motion`媒体查询来关闭人们要求的动画。

如果最后一部分可以对所有元素进行全局设置，那么在组件中进行说明会更好。

## 使用转义钩

为了提高可用性，我们可以通过添加一个可以关闭 popin 的转义监听器来为我们的 popin 系统添加另一个很棒的特性。

为此，ModalAnimated 组件中有一个`useEscape(active, onClose);`代码，但这还没有实现。

钩子很简单:它由两块积木组成:

*   一个`onEscape`回调函数，通过监听`keyCode`中的退出键`27`来记忆键盘事件
*   一个`useEffect`方法，将它绑定到窗口文档，并在模态被卸载后立即解除绑定

## 用法

用法非常简单:如果我们想要一个定制的`ModalView`组件，我们需要带有 2 个道具的`ModalAnimated`组件。

popin 本身的内容只是传递给`ModalAnimated`的子元素。我通常将内容放在另一个组件中，以使页面尽可能轻便。

# 结论

通过创建 3 个 light 组件和一个简单的定制钩子，我们能够得到一个非常模块化和可定制的 popin 系统。

如果还可以改进的话，我们已经实现了一个让你的 UI 设计者满意的系统，它实现了可访问性基础。

我们检查了所有的初始需求了吗？

*   简单易用:是
*   可定制:我们可以非常容易地定制视图
*   可访问性:我们确实在代码中包含了 a11y

任务完成！现在轮到你在你的项目中使用和改进它了。

编码快乐！