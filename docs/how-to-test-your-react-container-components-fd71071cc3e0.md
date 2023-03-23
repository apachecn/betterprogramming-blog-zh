# 如何测试 React 容器组件

> 原文：<https://betterprogramming.pub/how-to-test-your-react-container-components-fd71071cc3e0>

![](img/2d4e64361c2e60d7f02f0385399347a0.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/containers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在使用 [React](https://reactjs.org/) 和 [Redux](https://redux.js.org/introduction/getting-started) 时，您面临着测试您的智能组件(通常称为容器)的挑战。有几种方法可以做这件事。

本文假设你对 [Jest](https://jestjs.io/) 、 [Enzyme](https://airbnb.io/enzyme/) 和 JavaScript 有基本的了解。

在接下来的例子中，我将首先向您展示非连接方法(#1)，然后是连接方法(#2)。最后一个比前面一个有更多的好处，我将在后面演示。

## 该组件

我们将使用以下组件作为示例。

#片段 1

# #1 —不相关的方法

**测试你的组件和容器相互隔离。**

假设您从全局状态获得标题和描述。您可以通过 props 传递预期的参数来测试组件。

Redux 最后就是这么做的，为什么不直接做，断言最终结果呢？

对此的一个基本测试是直接传递 props，正如组件所期望的那样。

#片段 2

这里的问题是，我们并没有断言来自商店的映射输出了那些道具。

所以，最后，您测试了组件，但是您不确定容器是否映射了正确的属性，所以您通过如下方式单独测试它:

#片段 3

这种方法有几个缺点:

*   为了进行测试，您必须导出`mapStateToProps`、`mapDispatchToProps`和`Component`。
*   你还有另一组测试要做。
*   容器测试是乏味的，并且无法使用 [BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) 思维模式进行测试。
*   您没有测试组件和连接一起工作的真实场景。

另外，让我演示一下这种方法是如何容易出错的。假设您的 Redux 商店中有一些逻辑。

例如:

#片段 4

在这里，您检查浏览器是否大于 SM(断点)以返回完整的标题值，否则，您将返回该道具的短标题值。

使用第一种方法，您将无法在`Header.test.js`上测试这个场景。

你仍然可以使用`HeaderContainer.test.js` 来测试它，但是用更多的道具和更多的逻辑来扩展这个组件将会把它变成一个测试的噩梦，并且会非常混乱。

# #2 —关联方法

**一起测试组件和容器。**

我们已经看到，第一种方法有点乏味，不是很有效，并且它没有测试与 Redux store 集成的 React 组件。

对于下一个方法，您需要使用`redux-mock-store`的`configureStore`。

将您的`initialState`作为一个参数传递给具有`configureStore`属性的常量，并将其保存在另一个常量中。

#片段 5

注意，我们使用`.dive`和`toJson`来获得组件本身的清晰快照，而不使用 mount。

这样，我们就测试了到 props 和组件本身的 Redux 映射。

**有几个优点:**

*   更精确的测试。
*   连接测试。(每一个测试，除了他们自己的目的之外，还测试商店是否返回预期的结果。)
*   更可靠的测试和更不容易出错的测试。
*   不必要的容器测试文件。
*   智能组件中只有一个导出(连接)。

# 奖金

您想用不同的状态进行测试，而不重新创建上面的 *# Snippet 5* 中显示的样板文件吗？

使这个助手函数:

#片段 6

然后，不用重复 *# Snippet 5* 中所示的步骤，只需将初始状态更改为您想要的状态，并使用`mockStore`功能。

#片段 7

# 结论

总而言之，测试您的 React-Redux 集成组件，因为它们将在真实情况下工作。

请记住，测试应该覆盖组件的预期行为，也包括 Redux 存储。

祝测试愉快！