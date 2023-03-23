# 用 React 钩子和 Redux 钩子构建一个全局对话模态基础设施

> 原文：<https://betterprogramming.pub/global-dialog-modal-infrastructure-with-react-hooks-and-redux-hooks-1b6bedd052a6>

## 让你的模态出现在你喜欢的任何地方

![](img/320c84e8c9b3275b6553e987c1a2bc65.png)

罗恩·惠特克在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

由于对话模式几乎与每个 web 应用程序密不可分，所以您需要明智地构建它的基础设施。使用 React hooks(版本 16.8 及以上)，您可以轻松优雅地跨应用程序重用逻辑。在本文中，我们将介绍一种使用 React 钩子和 Redux 钩子创建对话模态基础设施的全局管理方法。

# 选择正确的方法

受[原子设计方法](http://atomicdesign.bradfrost.com/chapter-2/)的启发，最佳实践场景是使用外部/内部可重用组件库将模态容器从应用程序中分离出来。然后你需要问自己，你要去哪里管理 Modal 的逻辑？你在使用全球管理库吗？在哪里呈现它——在 DOM 中预先确定的位置，还是动态追加？

创建模态基础设施的方法有很多，您可以使用以下方法管理其逻辑。

## 本地管理

保存一个全局模态组件，并管理使用它的每个组件的局部状态。您可以使用 [React Portal](https://reactjs.org/docs/portals.html) 来呈现它，React Portal 直接在消费组件内部呈现，并提供了一种在父组件的 DOM 层次结构之外的 DOM 节点中呈现子组件的方法。另一种方法是全局包含模态对话框元素，并以旧的方式动态添加其子元素。

如果您在应用程序中很少使用对话模式，并且在构建可靠的基础设施上的投资是负面的，那么您可以选择这种快速实现。

## 全球管理

如果您已经在使用状态管理库(比如 Redux ),并且在整个应用程序中使用对话框模态，那么您最好在全局状态中管理它的逻辑，创建一个可重用的共享逻辑并将其呈现为一个全局元素。如今，用钩子很容易做到——让我们开始吧！

# 带有 React 和 Redux 挂钩的全局模式

假设您在一个**外部库**中托管您的通用和可重用组件，您应该创建一个模态目录，该目录具有一个`<Modal />`组件，该组件将其子组件:`<ModalTitle />`、`<ModalHeader />`、`<ModalBody />`和`<ModalFooter />`呈现为子组件和原子组件。

在应用程序中使用模态组件是通过创建一个`<ModalWrapper />`容器来实现的，这个容器使用全局状态管理钩子(例如 Redux 钩子)来呈现模态组件并管理它的可视状态。容器将有它的视图、缩减器、常量和自定义钩子文件。

首先，将`<ModalWrapper />`包含在全局级别中:

索引. jsx

然后，创建一个`<ModalWrapper />`容器，根据商店的参数(大小、类型、主体、标题等)呈现模式对话框。).

显示一个对话框模态是通过使用带有你所请求的参数的定制钩子调度`SHOW_MODAL/HIDE_MODAL`动作来完成的。

ModalWrapper.jsx

您可以使用 [react.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy) 动态地呈现一个定制组件，该组件采用一个函数，该函数必须调用一个动态`import()`并且必须返回一个`Promise`，该函数解析为一个带有包含 react 组件的`default`导出的模块。

modalWrapperReducer.js

如果你有任何特殊的处理，例如，对于一个确认对话框，你可以为它创建一个可重用的钩子，并且基本上为你所有的可重用模态的逻辑。

modalWrapperHooks.js

# 用法示例

*   渲染一个基本模态将来自组件钩子文件，带有带有所有必要参数的`useShowModal`钩子。
*   在模态内部呈现动态组件是通过传递绝对路径和可选的组件属性来完成的。

modalUsageHooks.js

就是这样！

# 摘要

模态是常见的，如果您正在使用最新的 React、Redux 和 React-Redux 版本，您可以很容易地以模块化和可重用的方式创建基础设施。