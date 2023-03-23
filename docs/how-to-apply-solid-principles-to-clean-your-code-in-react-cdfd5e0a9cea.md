# 如何在 React 中应用坚实的原则来清理代码

> 原文：<https://betterprogramming.pub/how-to-apply-solid-principles-to-clean-your-code-in-react-cdfd5e0a9cea>

## 审视单一责任原则在实践中的运用

![](img/99fed45da1b2bc8a4a62a14d9cc9804e.png)

照片由[MockupEditor.com](https://www.pexels.com/@mockupeditor-com-56805?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[派克斯](https://www.pexels.com/photo/silver-imac-near-white-ceramic-kettle-205316/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄。

[坚实的](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)原则的主要目的是为关心他们的技术的软件专业人员提供指导。以构建经得起时间考验的设计精美的代码库为荣的人。

今天，我们将从一个糟糕的代码示例开始，应用 SOLID 的第一个原则，看看这如何帮助我们编写小而漂亮、干净且职责明确的 React 组件。

让我们开始吧。

# 什么是单一责任原则？

单一责任原则告诉我们，每个类或组件都应该有一个存在的目的。

组件应该只做一件事，并且做得很好。

让我们用这个原则重构一段糟糕但有效的代码，让它变得更干净、更好。

# 让我们从一个不好的例子开始

我们先来看一些违背这个原则的代码。添加注释是为了更好地理解:

singleresponsibility violation . js

## 这段代码做了什么

这是一个功能组件，我们从远程数据源获取数据，过滤数据，然后在 UI 中显示它。我们还检测 API 调用的加载状态。

为了更好地理解，我把这个例子写得很短。但是您几乎可以在同一个组件的任何地方找到它们！这里发生了很多事情:

1.  远程数据获取
2.  数据过滤
3.  复杂状态管理
4.  复杂的 UI 功能

因此，让我们来探索如何改进代码的设计，使其更加紧凑。

# 1.将数据处理逻辑移出

永远不要将 HTTP 调用放在组件内部。这是经验法则。有几种策略可以用来从组件中删除这些代码。

您至少应该创建一个自定义挂钩，并将您的数据获取逻辑移到那里。例如，我们可以创建一个名为`useGetRemoteData`的钩子，如下所示:

useGetRemoteData.js

现在，我们的主要组件将如下所示:

FirstRefactor.js

看看我们的组件现在如何更小更容易理解！这是在错综复杂的代码库中可以做的最简单的第一件事。

但是我们可以做得更好。

# 2.用于数据提取的可重用挂钩

现在，当我们看到我们的`useGetRemoteData`钩子时，我们看到这个钩子正在做两件事:

1.  从远程数据源获取数据
2.  过滤数据

让我们将获取远程数据的逻辑提取到一个名为`useHttpGetRequest`的独立钩子中，该钩子将 URL 作为一个组件:

useHttpGetrequest.js

我们还将 reducer 逻辑移到了一个单独的文件中:

LoadingReducer.js

所以现在我们的`useGetRemoteData` 就变成了:

`**useGetRemoteData.js**`

干净多了，对吧？我们能做得更好吗？当然，为什么不呢？

# 3.分解 UI 组件

看看我们的组件，其中显示了用户的详细信息。为此，我们可以创建一个可重用的`UserDetails` 组件:

用户详细信息. js

最后，我们的原始组件变成:

用户. js

我们将代码从 60 行精简到了 12 行！我们创建了五个独立的组件，每个组件都有明确的职责。

# 让我们回顾一下我们刚刚做了什么

让我们回顾一下我们的组件，看看我们是否实现了 SRP:

*   `Users.js` —负责显示用户列表
*   `UserDetails.js` —负责显示用户的详细信息
*   `useGetRemoteData.js` —负责过滤远程数据
*   `useHttpGetrequest.js` —负责 HTTP 调用
*   `LoadingReducer.js` **—** 复杂状态管理

当然，我们可以改进很多其他的东西，但是这应该是你的一个好的起点。

# 结论

这是一个简单的演示，展示了如何减少每个文件中的代码量，并利用 SOLID 的强大功能创建美观且可重用的组件。

# 本系列的其他文章

1.  [开启关闭原理](/applying-the-open-closed-principle-to-write-clean-react-components-4e4514963e40)
2.  [利斯科夫替代原理](/applying-the-liskov-substitution-principle-in-react-3a0614a42a08)
3.  [界面偏析原理](/how-to-apply-interface-segregation-principle-in-reactjs-fadf77113c5d)
4.  [依存倒置原则](/apply-the-dependency-inversion-principle-in-react-c20a0afc3d64)

**通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) **或我的** [**个人网站**](https://www.mohammadfaisal.dev/) **与我取得联系。**

[](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7) [## 快速反应应用的 7 大库

### 摇滚明星开发人员的必备工具

better 编程. pub](/top-7-libraries-for-blazingly-fast-reactjs-applications-c0069e87c8b7) [](/21-best-practices-for-a-clean-react-project-df788a682fb) [## 清洁 React 项目的 21 个最佳实践

### 提高代码质量的实用建议

better 编程. pub](/21-best-practices-for-a-clean-react-project-df788a682fb)