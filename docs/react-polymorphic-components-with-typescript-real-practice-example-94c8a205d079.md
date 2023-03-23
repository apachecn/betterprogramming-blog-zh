# 用类型脚本反应“多态组件”

> 原文：<https://betterprogramming.pub/react-polymorphic-components-with-typescript-real-practice-example-94c8a205d079>

## 一个真实的实践例子

![](img/6d9c36b36cf80066badee73dce7a9013.png)

(来源:[**pexels.com**](https://www.pexels.com/))

在本文中，我们将学习如何在 React 中实现多态组件。此外，我们将根据我们想要呈现的元素类型对我们的道具进行强类型化。在我们的例子中，我们将创建一个多态按钮*。*

# 那么什么是‘多态成分’呢？

简而言之，它是一个组件，让我们指定想要使用哪个 React 元素作为它的根。如果你用过一些 UI 库，比如 [**Material UI**](https://mui.com/material-ui/api/button-base/#props) ，你就已经遇到多态组件了。

> 例如，MUI 有一个[“排版”](https://mui.com/material-ui/react-typography/)，它能够为根节点呈现任何 HTML 元素。这是一个非常强大的想法，给了我们更健壮和可重用的组件。

# 我们想要达到的结果

我们想要创建一个按钮，它将能够主要表现为一个普通的按钮、锚和反应路由器链接(或者任何其他 HTML 标签|组件，取决于您的需求)。此外，我们希望有所有需要的打字。比如平常的按钮有“type”属性，没有“href”，但是锚按钮有相反的道具。

我们的使用案例

这段代码向我们展示了如何使用我们的按钮。

# 制作我们的组件

让我们创建名为`Button.tsx`的文件，并定义基本的 react 组件。

没什么特别的，只是基本组件

那么，我们应该做些什么来为按钮支持多种类型的 HTML 元素呢？我们可以通过定义属性“as”来实现这一点，它将是`React.ElementType` 的类型，然后使用它来呈现我们的根元素。

现在我们的组件是多态的

> 注意，我们必须将“Tag”大写，否则它将被视为一个普通的 HTML 元素<tag>。</tag>

现在我们有了一个多态按钮，但是我们没有任何用于各种“as”值的属性类型。但是，如果它们依赖于动态“as”prop，我们将如何得到它们呢？

这就是我们应该使用[类型脚本泛型](https://www.digitalocean.com/community/tutorials/how-to-use-generics-in-typescript)来正确键入所有内容的确切情况。因此，让我们尝试编写一些更高级的类型。

自有道具类型

我们已经创建了一个新的类型`ButtonOwnProps`包含按钮基础道具。此外，它使用泛型`E`，后者扩展了`React.ElementType`，并为‘as’赋值。

接下来，我们应该创建一个名为`ButtonProps`的类型，它将我们的基础道具与通用元素道具结合在一起。

此外，我们应该从已经在基类中声明的泛型组件中排除 props。

拥有的属性与通用组件类型的属性联合

在第一行，没有什么特别的事情发生。我们声明了一个类型，它接收扩展了`React.ElementType`的泛型`E`。

在第二行中，我们只是将其传递给我们的基础道具以获得正确的`as`类型。

在第三行中，我们省略了通用组件的所有属性，这些属性已经存在于我们的`ButtonOwnProps`中。

# 决赛成绩

最后，我们需要将泛型传递给我们的`ButtonProps`类型，并给它一个默认值。因此，最终的组件将如下所示:

现在我们的按钮组件终于完成了

[](https://javascript.plainenglish.io/how-to-become-frontend-dev-in-4-months-ec3f61576251) [## 如何在 4 个月内成为前端开发人员

### 寿命测试指南

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-become-frontend-dev-in-4-months-ec3f61576251) [](https://javascript.plainenglish.io/5-react-usestate-mistakes-that-will-get-you-fired-b342289debfe) [## 对那些会让你被解雇的错误做出反应

### 5 种常见的反应使用状态错误以及如何避免它们

javascript.plainenglish.io](https://javascript.plainenglish.io/5-react-usestate-mistakes-that-will-get-you-fired-b342289debfe) [](https://javascript.plainenglish.io/frontend-architectures-classic-approach-no-architecture-d3c839e46403) [## 前端架构:“经典”方法(无架构)

### 了解最常见前端架构的优缺点

javascript.plainenglish.io](https://javascript.plainenglish.io/frontend-architectures-classic-approach-no-architecture-d3c839e46403) 

本文到此为止。现在我们的`Button`组件已经可以使用了。

我希望你喜欢这篇文章，我能够给你一些新的东西。如果你有任何问题或建议，请写在评论里。

祝你好运！