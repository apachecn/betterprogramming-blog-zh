# React 中的样式组件——组织样式的更好方式

> 原文：<https://betterprogramming.pub/styled-components-in-react-a-better-way-to-organize-your-styles-591c43fa98f8>

## 如何以及为什么在 React 中使用*样式化组件*而不是普通 CSS

![](img/6a74243b83e2793668ddc58f89f31d1f.png)

马库斯·加纳尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着你的 React 应用变得越来越大，你可能会遇到一些 CSS 成长的烦恼。即使你试图保持你的样式组织良好，你也可能会遇到一些常见的问题，比如名称空间冲突、样式改变的意外影响、内容在多个地方被设计样式等等。虽然通过良好的纪律和团队协作可以避免这些问题，但是简单的 CSS 很容易遇到这些问题。

虽然有一些工具可以帮助避免这些问题，但是最强大的工具之一是`styled-components`。在本文中，我们将介绍什么是样式化组件，它们是如何工作的，以及如何使用它们来恢复 React 中 CSS 样式的合理性。

# 什么是样式组件？

`Styled-components`，顾名思义，是一种将样式直接附加到 React 组件的方式。让我们快速看一下一个使用 CSS 编写的简单组件，然后修改成使用`styled-components`。在这个简单的例子中，我们有一个简单的组件容器和一个头:

没有样式组件的简单组件

现在，用`styled-components`做同样的例子。我们没有使用带有`className`属性的`div`和`h3`，而是创建标签的样式版本，并将其用作组件:

与上面相同的组件，用样式化组件重写

请注意，虽然为了简单起见，我们的示例将`styled-components`与主组件显示在同一个文件中，但是最好将这些样式保存在单独的文件中，尤其是当有很多样式时。

除了简单地重构我们放置 CSS 的地方，样式化组件还让我们在样式中引入逻辑，这在 CSS 中是不可能的。考虑一个场景，当`selected`值为真时，组件的样式发生变化。使用普通 CSS 类，您可能会基于对象状态生成一个类名列表。代码如下:

使用类名应用选定样式的组件

这种 CSS 类名操作很麻烦，当逻辑变得更复杂时，例如，当可能需要应用多个“可选”类时，可能很难使用。

使用`styled-components`，我们可以将组件状态(如`selected`布尔值)视为样式组件的属性，并在样式中使用逻辑来显式修改样式，而无需使用类名。代码如下:

使用样式组件应用选定样式的组件

样式化组件的另一个有助于保持代码组织良好的特性是，通过简单地将新样式应用于现有的`Styled`组件来扩展样式的能力:

对现有样式化组件进行样式化

这涵盖了`styled-components`的主要优点，但是还有其他几个特性，比如对动画、主题、CSS 伪选择器等的支持。有关这些功能的完整文档或了解更多关于`styled-components`如何工作的信息，参见[样式组件文档](https://styled-components.com/docs/basics#adapting-based-on-props)。

# 为什么要使用样式化的组件(为什么不呢)

在我们开始在项目中安装样式化组件之前，让我们来看看您应该尝试它们的一些原因:

*   保持样式更接近代码:`styled-components`与 CSS 相比，鼓励您保持样式与代码更紧密地集成，在 CSS 中，类名和嵌套选择器的使用会在远离预期位置的地方产生影响
*   保持样式的语义性:不描述组件的外观，`styled-components`保持样式附加到组件本身。
*   显式样式范围:原始 CSS 的最大问题之一是它的全局性质——有意或无意地对 CSS 进行影响深远的更改太容易了。`styled-components`显式地将样式限定在组件范围内

为了公平起见，让我们看看你可能不想使用`styled-components`的一些原因:

*   移植工作:尽管`styled-components` 很简单，但是移植一个现有的代码库可能需要大量的工作。逐渐迁移是可能的，但是既有 CSS 文件又有影响样式的`styled-components`是混乱的。
*   供应商和工具锁定:CSS 文件是样式化的最小公分母，而使用`styled-component`构建将固有地以一种耦合到您正在使用的工具集(React、styled-components 等)的方式构建您的代码。).我不认为这是一个巨大的缺点，除非你一开始就不确定你的架构。
*   个人喜好或造型哲学:我承认在上面列出的一些好处上有不同意见的空间。特别是语义样式，一些框架(比如 Tailwind)有意采取相反的方法。实际上，我认为这两种技术可以结合起来(例如，用 Tailwind 类来帮助构建样式化的组件)以达到两全其美。

# 如何在 React 中使用样式化组件

如果您已经做到了这一步，并且想要亲自尝试样式化的组件，这很简单——只需使用您最喜欢的包管理器将`styled-components`添加到您的项目中:

```
yarn add styled-components
```

然后重新启动开发服务器，尝试一个简单的样式化组件:

```
import styled from "styled-components"
const StyledP = styled('p')`color: #fff;`
```

就这么简单！但是如果你遇到任何问题，你可以查看[安装指南](https://styled-components.com/docs/basics#installation)以获得更详细的说明。

# 总结:开始编码，用风格做出反应

在使用 React 时，样式组件是帮助保持样式有序的宝贵工具。它们很好地集成到 React 代码库中，使您的样式代码保持独立，但与您的组件结构完全同步。如果你正在努力管理不断增长的 CSS 文件，我强烈推荐你看看`[styled-components](http://styled-components.com)`！