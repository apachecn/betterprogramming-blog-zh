# 在 React 中用拖放式动态部件构建一个仪表板

> 原文：<https://betterprogramming.pub/build-a-dashboard-with-drag-and-drop-dynamic-widgets-in-react-1f012eeecd8b>

## 创建交互式仪表板

![](img/3971667bba36fc52c2201187e9d10a6f.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

很多时候，当创建一个 web 应用程序时，您需要创建一个交互式仪表板。这个仪表板可以有开发人员可以想象的许多功能。我们今天将实施几个:

*   拥有用户可以玩的动态部件。
*   允许用户拥有一个包含默认小部件的默认视图。
*   允许用户通过从单独的视图拖放来添加新的小部件。
*   允许用户在仪表板画布上的任何地方拖放现有的小部件。
*   允许用户调整这些部件的大小。

为了启用这些特性，开发人员可以摆弄代码，实现一个本质上是动态的、对 web 包大小没有太大影响的解决方案。

首先，让我们收集所有需要的材料:

*   React 17(或任何版本≥ 16.6)
*   我们将用来管理小部件的库: [React-Grid-Layout](https://github.com/react-grid-layout/react-grid-layout)
*   最后，一个可选的成分:打字稿

以下是完整代码的堆栈:

现在让我们一步一步地实施它:

# 1.创建仪表板组件

该组件将负责呈现和管理小部件的网格。这也将负责保存当前小部件的布局。

*   我们将使用来自`react-grid-layout`的`GridLayout`来实现这一点。对于响应视图，使用`GridLayout.Responsive`。
*   我们将为它的一些属性提供值，比如`preventCollision`，以定制网格行为。
*   `GridLayout`提供了一些回调函数，比如`onDrop`，我们将使用它们向网格动态添加新的小部件，或者将布局保存在组件状态和一些持久存储中，比如`localStorage`。
*   我们还将使用第二个组件`DashboardWidget`，动态加载我们将在下一步中创建的小部件组件。

# 2.创建 DashboardWidget 组件

该组件将负责在运行时动态加载组件，并在重新呈现时将其保存在状态中。

对于这个组件，我们将使用 React 16.6 中引入的 React 特性，[代码分割](https://reactjs.org/docs/code-splitting.html):

> “对你的应用进行代码拆分可以帮助你‘延迟加载’用户当前需要的东西，这可以极大地提高你的应用的性能。虽然你没有减少应用程序中的总代码量，但你避免了加载用户可能永远不需要的代码，并减少了初始加载期间所需的代码量。”—反应文档

这个 React 特性将帮助我们在构建时将小部件代码分割成单独的文件，从而减小主包的大小。因此，浏览器将只加载用户在定制布局中实际选择的小部件。

*   对于加载组件，我们将使用[动态](https://reactjs.org/docs/code-splitting.html#import) `[import()](https://reactjs.org/docs/code-splitting.html#import)` [语法](https://reactjs.org/docs/code-splitting.html#import)结合`[React.lazy](https://reactjs.org/docs/code-splitting.html#reactlazy)` [函数](https://reactjs.org/docs/code-splitting.html#reactlazy)和`Suspense`组件。
*   我们必须将来自`GridLayout`的引用传递给 div 元素，我们将在其中呈现我们的小部件。这是允许`GridLayout`将 div 标识为可拖动元素所必需的。在本例中，我们使用了`forwardRef`并将所有的 props 和 ref 转发给第一个 div 元素。

# 3.创建小部件选择器

该组件将负责管理新的小部件，并允许用户将小部件拖放到仪表板上的网格中。

*   为了允许用户拖放小部件，我们将使用 [HTML 拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API) 。这个 API 允许界面用鼠标拖动*可拖动的*元素，并通过释放鼠标按钮来放下它们。
*   为此，我们将使用`[onDragStart](https://developer.mozilla.org/en-US/docs/Web/API/Document/dragstart_event)` [事件](https://developer.mozilla.org/en-US/docs/Web/API/Document/dragstart_event)和`[dataTransfer](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer)` [对象](https://developer.mozilla.org/en-US/docs/Web/API/DataTransfer)来允许网格将元素识别为新的小部件。
*   您可以在此自定义可用小部件的显示方式。

# 4.创建小部件

这将是一个自给自足的独立组件，可以管理自己的状态和数据。您可以根据自己的选择定制其界面。

创建小部件时唯一要记住的是，您必须使用[默认导出(而不是命名导出)](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export#syntax)来导出组件。这是由于`React.lazy`功能目前只支持[默认导出](https://reactjs.org/docs/code-splitting.html#named-exports)。

就是这样。

现在，您已经创建了一个动态仪表板，它将允许您的用户定制小部件及其布局。您还可以在运行时动态加载小部件。

此外，您可以添加一些改进，为小部件添加道具，这些小部件可以在运行时被网格更改(有助于为显示的数据添加过滤器)，或者使网格根据可用的宽度做出响应。

以下是 GitHub 回购的链接:

[](https://github.com/kgrvr/React-Dashboard-Widget-Example) [## kgr VR/React-Dashboard-Widget-示例

### 由斯塔克布里兹·⚡️.创作通过在…上创建帐户，为 kgr VR/React-Dashboard-Widget-Example 开发做出贡献

github.com](https://github.com/kgrvr/React-Dashboard-Widget-Example) 

感谢您的阅读。