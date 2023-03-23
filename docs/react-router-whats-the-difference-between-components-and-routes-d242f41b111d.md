# React 路由器:组件和路由有什么区别？

> 原文：<https://betterprogramming.pub/react-router-whats-the-difference-between-components-and-routes-d242f41b111d>

## 无忧无虑，像专业人士一样享受 React 路由器

![](img/a622ee10a0b0f5d9bf004c0eba593872.png)

[66 号公路印在从](https://www.pexels.com/photo/drive-empty-highway-lane-210112/)[Pexels.com](http://Pexels.com)出发的公路上

[React 路由器(DOM](https://reacttraining.com/react-router/web/guides/primary-components) )是一个令人惊叹的 NPM 包，让你逃离 SPA 的地狱。根据我的教学经验，我发现人们并不经常钻研文档。相反，他们会看到一些代码示例，并通过创建他们的应用程序来进行模式匹配。这完全没问题…直到它不是。

我注意到新程序员对`render={this.renderComponent}`或`component={ComponentName}`有强烈的习惯和偏好。随后，他们遇到了麻烦。虽然两者都起作用，但它们的行为不同。我来解释一下原因。

# 设置

首先，让我们看一些代码。让我们假设我们有一个类组件，它从它的父组件获得一个用户数组。

如你所见，我们在第 20 行渲染了`homepage`组件，然后在下一行渲染了`Profile`组件。我们将用户对象的附加属性传递给`Profile`组件。您可以使用`render` *或* `component`将一个给定的 React 组件与一个路径相关联——但是您应该使用哪个*？*

# *组件还是渲染？*

*现在，想象你首先访问你的个人资料，然后你去主页，然后你再次访问你的个人资料。在这种情况下:*

*   *`render`使组件只挂载一次，然后在需要时重新渲染。组件留在后台——这意味着您放入`componentDidMount`、`constructor`或`shouldComponentUpdate`的任何内容都将只运行一次！此外，由于组件没有卸载，您可能会遇到数据泄漏。您可以强制组件重新呈现，但是这种果汁值得压榨吗？*
*   *另一方面，`component`会在每次访问时重新实例化该组件(该组件会被装载、卸载，如果您进行了访问，还会再次装载)。这个语法是对`React.createElement` — **的抽象，因此在性能方面效率较低，但在某些情况下更有必要。***

**一般来说，* `*render*` *最适合功能组件，因为它们默认没有生命周期方法，而* `*component*` *最适合类组件。**

# *获取路线道具和道具*

*当涉及到访问[路由道具](https://reacttraining.com/react-router/web/api/Route/route-props)，即`history`、`location`和`match`时，你可以用 React Router 在两个选项中默认免费获得它们，即使你没有明确地通过它们。传递道具也是一样——使用`render`或`component`不会影响你传递道具的能力。*