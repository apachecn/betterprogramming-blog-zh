# 一种自以为是的构建 React 应用的方式

> 原文：<https://betterprogramming.pub/an-opinionated-way-to-structure-react-apps-10f87bf29952>

## 根据我建造几个大项目的经验

![](img/82ee8a6a6c5491d0ee377e38a0019b6d.png)

照片由[戴恩·托普金](https://unsplash.com/@dtopkin1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄。

当我们第一次开发 React 应用程序时，我们可以将每个组件放在一个文件夹中，它就可以工作了。但是当涉及到更大的项目时，如果我们继续这样使用 React，可能很难在文件之间找到我们的方法。

那么我们如何处理一个更大的项目呢？[丹阿布拉莫夫有办法](https://react-file-structure.surge.sh/)。你不觉得这很有帮助吗？事实上，它是。这是找到满足您需求的完美架构的最佳方式，但代价是文件夹创建和删除的多次迭代。

今天，我将介绍我的许多举措的结果，为寻求提高自身能力的人们建立一个基础结构。

# 初步考虑

在我们开始之前，我想指出的是，我提出了一种自以为是的构建应用程序的方法。在一些项目中，我不得不做不同的事情，因为应用的核心概念太不一样了。对你来说也是如此。

另外，我想介绍几个想法，这样你会更好地理解为什么。

首先我用[原子设计](https://atomicdesign.bradfrost.com/)。有些组件只是可视的。基本上，它涉及到我的[故事书](https://storybook.js.org/)中的每一个组成部分。我把它们叫做 ui 组件。原子设计也带来了模板组件。

其他一些组件被赋予了表单域的特定行为，例如增强的表单域为浏览器默认表单域提供了验证模式。他们是原子设计中的有机体。

最后，我正在使用 React 上下文 API 而不是 redux，正如我在之前的一篇文章中解释的那样。我创建顶级组件，我称之为提供者。

# 根文件夹入门

使用 [create-react-app](https://create-react-app.dev/) ，我的应用程序的根文件夹是`src`文件夹，我在其中放置了几个文件夹:

*   `App` —放置主组件的文件夹，包含全局提供者和主路由。
*   `components` —应用程序的每个 React 组件所属的位置。
*   在其中我可以找到我可以在应用程序中提出的 GraphQL 请求的每一部分。
*   `libs` —这个有点乱，但是包含了其他的一切。它通常由不到十个文件组成，所以我从来没有更好地分割它们。

这是我在基础结构的简单性和代码分割之间找到的更好的比例。由于 React 是一个组件框架，你很容易想象到`components`文件夹会更复杂一点。

其他三个文件夹我就不详细解释了。您可以查看本文底部的示例树，以了解更多关于放在那里的文件类型的信息。

# 组件文件夹

我们到了:应用程序的主要部分。这个文件夹由更多的子文件夹组成。记住，如果你复制了这个结构，如果它在你的项目中没有意义，你不需要完全使用它们。例如，`ui`文件夹在 [Material-UI](https://material-ui.com/) 应用程序中没有意义。

*   `hooks` ——我在应用程序中放置了大量的挂钩。我有很多这样的文件来体现可重用性，所以我还创建了子文件夹来说明它们所属的工作。例如，我经常有一个`useInterval`钩子来处理循环作业。我还在那里放置了一个`useUser`钩子，它给了我当前连接的用户信息。
*   这重新组合了我项目中的每一个模态。我曾经把它们放在别的地方，但是我实际上发现我在应用中经常会多次使用它们，而且数量相当多。有了他们自己的文件夹，我在上面工作变得更简单了。
*   `organisms` —我放置之前提到的功能组件的文件夹。如果有太多的子文件夹，可以将它拆分成子文件夹，这种情况经常发生。
*   `providers`-包含全局数据或特征逻辑的组件。为了更多地了解提供者是什么样子的，我邀请您看一看以前的一篇文章[,在这篇文章中我用它们替换了 redux。](https://medium.com/better-programming/how-i-dropped-redux-for-the-context-api-7338d481e179)
*   `svg` —自从 create-react-app [以来，应用程序中使用的每个图标的主页可以本地包含它们](https://create-react-app.dev/docs/adding-images-fonts-and-files/#adding-svgs)。你可能有设计师，但万一你没有，我真的很喜欢[材料设计图标集](https://materialdesignicons.com/)，在那里我总能为我的应用找到完美的图标。
*   `templates`——其中有我的原子设计应用程序的页面布局。它不是应用程序中最丰富的文件夹，但考虑到布局的用途，它们最好被隔离。
*   `ui` —我的应用程序的原子和分子在哪里。这是应用程序中最重的文件夹之一，所以它被域子文件夹分开。
*   `pages` —这对应于我的应用程序中定义的页面。这是最复杂的文件夹，因为它是递归的。我们将在这一章之后的特定章节中讨论它。

这是很多文件夹，对吧？我的完美文件夹结构中最困难的部分是保持简单([亲亲！](https://en.wikipedia.org/wiki/KISS_principle))，但没有把苹果和橘子混在一起。这就是为什么我把原子设计的原子和分子放在同一个文件夹中，但我也经常有域子文件夹。

# Pages 子文件夹

在谈到文件夹结构之前，我们先来谈谈 URL。我发现，将应用程序中的每个 URL 分为两部分(域名和页面)是构建页面路径更简单、更可靠的方法。

我也可能有额外的参数来显示特定的详细页面。这些数量不限。

例如，我有这样几页:

*   `/user/login`
*   `/user/account`
*   `/todo/list`
*   `/todo/details/123`
*   …

但是我没有这些:

*   例如`/user`会重定向到`/user/dashboard`。
*   `/`可能也会重定向到`/user/dashboard`。

这些 URL 给你一个关于文件夹结构的提示。毫无疑问，我们有第一个文件夹是域，第二个文件夹是页面。

正如我前面提到的，页面文件夹也是递归的。为什么？原因很简单，有时内容对应用程序来说不是全局的。一个`useTodoList`钩子只在`/todo/list`页面和`TodoItem`组件中使用。

因此，在页面文件夹中，您还可以找到一个`components`文件夹，其中包含除了`pages`之外的所有先前定义的文件夹。

# 把所有的放在一起

这是一个很大的词来定义整体结构。但是一个例子往往比文字更好，所以这里是:

```
src
 |- App
 |   |- App.jsx
 |- components
 |   |- hooks
 |   |   |- useInterval.jsx
 |   |- modals
 |   |   |- AddTodoModal.jsx
 |   |- organisms
 |   |   |- PrivateRoute.jsx
 |   |   |- forms
 |   |   |   |- TextInput.jsx
 |   |- pages
 |   |   |- todo
 |   |   |   |- list
 |   |   |   |   |- TodoList.jsx
 |   |   |   |   |- components
 |   |   |   |   |   |- hooks
 |   |   |   |   |   |   |- useTodoList.jsx
 |   |   |   |   |   |- organisms
 |   |   |   |   |   |   |- TodoItem.jsx
 |   |   |- user
 |   |   |   |- login
 |   |   |   |   |- UserLogin.jsx
 |   |- providers
 |   |   |- UserProvider.jsx
 |   |   |- TodoProvider.jsx
 |   |- svg
 |   |   |- check.svg
 |   |- templates
 |   |   |- LoggedPage.jsx
 |   |   |- LoginPage.jsx
 |   |- ui
 |   |   |- alert
 |   |   |   |- Alert.jsx
 |   |   |   |- Alert.module.css
 |   |   |   |- Alert.stories.jsx
 |   |   |   |- Alert.test.js
 |   |   |- button
 |   |   |   |- Button.jsx
 |   |   |   |- Button.module.css
 |   |   |   |- Button.stories.jsx
 |   |   |   |- Button.test.jsx
 |- gql
 |   |- todo
 |   |   |- TodoCreate.gql
 |   |   |- TodoDelete.gql
 |- libs
     |- preload.js
```

即使这个例子非常简单，它也包含了说明前面解释的所有内容。

# 结论

即使 React 的这个文件夹结构是多年来关于如何组织项目的工作，它也可能不适合所有的需求。然而，到今天为止，它满足了我所有项目的需求，让我在工作中特别有效率。

如果你自己遇到了一些问题，我将很高兴听到你关于这个提议是如何给你带来麻烦的。但是记住，正确的文件夹结构不一定是我的，而是适合你的项目的。毕竟:

> "移动文件直到感觉合适为止."— [丹·阿布拉莫夫](https://react-file-structure.surge.sh/)