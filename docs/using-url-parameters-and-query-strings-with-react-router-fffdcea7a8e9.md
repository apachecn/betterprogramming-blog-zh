# 如何在 React 路由器中使用 URL 参数和查询字符串

> 原文：<https://betterprogramming.pub/using-url-parameters-and-query-strings-with-react-router-fffdcea7a8e9>

## 让您的用户去他们要去的地方

![](img/610fb05fdcca6900af912f3079b5a6b0.png)

马特·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

要构建单页面应用程序，我们必须有某种方法将 URL 映射到 React 组件来显示。

在本文中，我们将研究如何在 React 路由器路由中访问 URL 参数。

# 定义接受 URL 参数的路由，并获取它们

为了定义一个带有将`id`作为参数的`path`的`Route`,我们可以在参数名前加一个冒号。

例如，我们可以写:

```
<Switch>
    <Route path="/:id" children={<User />} />
</Switch>
```

然后我们可以使用`useParams`钩子来获取 URL 参数。

我们可以写一个完整的例子如下:

在上面的代码中，我们将`App`中的`Switch`组件和里面的`Route`作为子组件。`path` prop 被设置为`/:id`，这意味着我们稍后可以使用`useParams`钩子来获取 route 参数。

然后在`children` prop 中，我们传入我们想要显示的`User`组件。

此外，我们还添加了带有链接的`Link`组件，我们可以单击这些链接转到带有`to`属性中给定 URL 参数的 URL。

然后在`User`组件中，我们用`useParams`钩子从 URL 参数中获取`id`。

最后，我们在`User`路线中显示`id`。

![](img/d00eb2811794fd92eda6f58c45bc14e9.png)

乔治·希尔斯在 Unsplash 上的照片

# 获取和设置查询字符串

我们可以在组件中定义并使用一个`useQuery`钩子来获取查询参数。要传入查询参数，我们只需像往常一样将它们添加到`Link` s `to`道具中。

例如，我们可以这样写:

我们首先定义了`useQuery`钩子，通过`URLSearchParams`构造函数获取 URL 的查询参数。我们得到了`useLocation()`的`search`财产。

然后我们创建一个`QueryScreen`组件来调用`useQuery()`，并获得分配给`query`的`URLSearchParams`实例。在这里，我们可以使用`query`通过调用传入了`'name'`的`get`来获取`name`查询参数。

然后我们将`query.get(“name” )`返回的值作为道具传递给`User`组件，并显示在那里。

# 结论

我们可以使用`useParams`钩子来获取在`Route` s 中定义的 URL 参数。

我们可以在`Route`中接受 URL 参数，方法是在`path`参数中的参数名前加一个冒号，就像`path=”/:id”`一样。

要在路径中添加查询字符串，我们只需将它直接追加到路径中。

然后我们可以将查询字符串转换成一个`URLSearchParams`实例，并且我们可以使用它的`search`属性来获取查询字符串。

然后我们可以使用带有键名的`get`方法来获取值。