# 使用 React 路由器创建自定义链接并防止转换

> 原文：<https://betterprogramming.pub/creating-custom-links-and-preventing-transitions-with-react-router-c64caa4693f4>

## 继续之前提示用户的自定义链接

![](img/c7b51b01d7faa67097d51ffd6ff454a4.png)

贾斯汀·劳伦斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

要构建单页面应用程序，我们必须有某种方法将 URL 映射到 React 组件来显示。

在本文中，我们将了解如何使用 React Router 创建自定义链接并防止转换。

# 创建我们自己的链接

我们可以通过创建一个呈现`Link`组件的组件来创建一个链接。

例如，我们可以这样写:

在上面的代码中，我们有`FancyLink`组件:

它需要一个`label`、`to`和`activeOnlyWhenExact`道具。

`label`道具用于链接标签。

然后用`to`道具设置路径。而`activeOnlyWhenExact`用于设置路径和实际 URL 完全匹配时匹配是否只是匹配。

我们使用`match`值来设置`div`的`className`。为了确定 URL 是否匹配我们的路由路径，我们使用了`useRouteMatch`钩子。

它让我们无需实际呈现一个`Route`就可以访问匹配数据。

然后我们可以在`App`中使用我们的`FancyLink`组件。当`match`为`true`时，`>`字符显示在`Link`之前。

# 防止过渡

我们可以使用 React 路由器防止用户转换到另一个路由。

要做到这一点，我们可以呈现`Prompt`组件来显示一条消息，询问用户是否想离开某条路线。

例如，我们可以如下使用`Prompt`组件:

在上面的代码中，我们有`Form`组件，它有一个输入元素。当输入值改变时，我们设置`text`状态。

当`text.length`为 0 时，`Prompt`组件将呈现一个`confirm`对话框，正如我们在`when`中指定的，我们点击 Foo 链接转到`/foo`路线。

`Prompt`需要以下道具:

*   `message` —当用户试图离开时显示消息的字符串，或者返回消息的函数
*   `when` —一个布尔值，当我们传入的条件是`true`时，它有条件地呈现`Prompt`组件，以相应地阻止或允许导航

否则，因为不满足`when`中的条件，所以`confirm`对话框不会显示。

![](img/df6184e9bd58cce05b6a4c399fcb6584.png)

乔恩·弗洛布兰特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以通过创建一个呈现`Link`的组件来创建自己的链接组件。我们可以用道具和状态来定制。

为了防止转换，我们可以使用`Prompt`组件，它接受`when`属性，该属性接受在屏幕上显示的条件。