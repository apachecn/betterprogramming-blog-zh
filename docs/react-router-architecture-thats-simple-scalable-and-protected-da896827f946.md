# 简单、可扩展且受保护的 React 路由器架构

> 原文：<https://betterprogramming.pub/react-router-architecture-thats-simple-scalable-and-protected-da896827f946>

## 在单页 React 应用程序中设置高级路由的教程

![](img/89c25bcf69929b513550290cc9732741.png)

[迭戈·希门尼斯](https://unsplash.com/@diegojimenez)在 [Unsplash](https://unsplash.com/s/photos/route) 上的原图

React 中的路由，通常使用流行的 [React 路由器](https://reacttraining.com/react-router/web/guides/quick-start)库，会很快变得混乱。

*   我如何管理所有这些路线？
*   实现认证/保护路由的最佳方式是什么？
*   我应该如何处理嵌套路由？

虽然这些问题显然有很多很好的答案，但在我将在本教程中介绍的方法之前，我已经尝试了几种不令人满意的方法，我发现对于我正在开发的一个[开源 React/Redux 样板](https://github.com/ryanjyost/react-spa-starter)来说，这是一个很好的实现。

# 先决条件

*   对现代反应堆(钩子等)有扎实的理解。).
*   熟悉 [React 路由器](https://reacttraining.com/react-router/web/guides/quick-start)。
*   [使用路线配置的代码示例](https://reacttraining.com/react-router/web/example/route-config)。

# 设置项目

用 [create-react-app](https://github.com/facebook/create-react-app#readme) 和 cd 创建一个新的 React 应用程序到项目中。

```
npx create-react-app routing-tutorial 
cd routing-tutorial 
npm install
```

安装 React 路由器库。

```
npm install react-router-dom
```

启动应用程序，并在教程的剩余部分保持运行。

```
npm start
```

为了能够在我们的应用程序中使用路线，我们需要将应用程序包装在`react-router`的`<BrowserRouter />`组件中。所以，打开`index.js`文件，用下面的代码更新它。

我们还希望一些基本的 UI 结构最终显示所有路线链接的菜单，然后显示我们所在的特定路线的内容。因此，打开`App.js`文件，用以下代码替换其中的代码:

代码更新后，您应该会在浏览器中看到以下内容:

![](img/02cd87723711fc9d281d18dcfb94d55e.png)

# 路线配置

React 中实现路由的基本方法是直接渲染`<Route/>`组件，如下面的[示例](https://reacttraining.com/react-router/web/example/basic):

虽然这种方法没有任何问题，但在构建一个重要的应用程序时，它可能会变得非常冗长/重复，使其难以重构，并且只是简单地跟踪您的路由发生了什么。

实现路由的一种更程序化和更有组织的方式是设置路由配置，即路由配置的数组和对象，它们可以循环通过并呈现为`<Route/>`。

用`react-router` [docs](https://reacttraining.com/react-router/web/example/route-config) 的话说:

> 有些人发现了集中式路由配置的价值。路由配置只是数据。React 擅长将数据映射成组件，而`<Route/>`就是一个组件。
> 
> 我们的 route config 只是一个带有`path`和`component`道具的逻辑“routes”数组，排序方式与在`<Switch/>`中相同。

让我们为这个应用程序创建自己的基本路线配置，具有以下特征:

*   `/` —用户可以登录应用程序的索引路线。
*   `/app` —认证用户的路由。

创建一个文件`routes.js`，并将下面的代码粘贴到其中。

请注意上面代码的以下内容:

*   `ROUTES`数组的结构模仿了 app 的路由结构。嵌套路由只是嵌套的`routes`数组。
*   每个特定的路由配置对象都具有以下属性:
*   `path` —该配置处理的路线。
*   `key` —路线的唯一标识符。我们在教程中不会真正使用它们，但是当您想要引用/链接到没有硬编码路径的路径时，它们会变得非常有用，因为这些路径可能会被重新配置。
*   `exact` —一个布尔值，它确定`path`是应该完全匹配，还是部分匹配。点击此处了解更多信息。
*   `component`—app 在特定`path`时呈现的组件。

# 渲染路线

我们需要根据配置实际呈现路线，这需要一些额外的有用组件。

## 助手组件

在`routes.js`中，进行以下更新。

如果适用，该组件允许呈现子路线。用官方文件的话说:

> 一个用于`<Route/>`的特殊包装器，它知道如何通过在`routes` prop 中将“子”路由传递给它所呈现的组件来处理它们

使用上面组件，我们可以呈现一条单独的路线，但是我们还需要能够呈现一组在我们的`ROUTES`配置中指定的路线。

因此，在`RouteWithSubRoutes`声明下面，为新组件`RenderRoutes`添加下面的代码。

注意这里的“未找到”路线，如果在`Switch`中没有匹配的路线，它将被渲染。

这种架构在每个嵌套层处理未定义的路线，所以理论上你可以留在应用程序的嵌套部分(如`/app`)，而不是有一个通用的顶级“404-未找到”页面。

您也可以自定义未定义路径和不同部分的功能。

## 更新路线配置

我们需要对这里的`routes.js`文件进行的最后一次更新是使用`RenderRoutes`作为`APP`配置的`component`，该配置在`/app`路径名空间中声明了嵌套路由。

因此，用下面的代码更新`ROUTES`。

## 实际渲染路线

现在，打开您的`App.js`文件，并将`RenderRoutes`组件添加到应用程序的内容部分。这是顶层`ROUTES`进入`RenderRoutes`组件的地方。

任何路由配置的嵌套数组也使用这个`RenderRoutes`组件，如前一步所示。

## 测试您的基本路由

现在，您应该能够手动更新应用程序`localhost:3000`之后的路径名，以查看应用程序右侧呈现的正确内容。

*   `/`应显示“登录”。
*   `/fake`应显示“未找到”。
*   `/app`应显示“应用程序索引”。
*   `/app/page`应显示“应用程序页面”。
*   `/app/fake`应显示“未找到”。

这是 React 中路由的基础！请继续阅读，以实现一个根据路由配置以编程方式构建的菜单，并保护路由免受未授权用户的访问。

# 呈现任意嵌套的路线菜单/树

大多数应用程序都有一个链接菜单，便于用户导航应用程序，所以让我们实现一种方法来呈现一个菜单，该菜单反映了用`ROUTES`配置定义的嵌套路线结构。

因为我们不能确定嵌套路由有多深，所以每次路由配置有另一个`routes`数组时，我们都需要使用一些递归。

下面是完全更新的`App.js`。

通读`displayRouteMenu`函数的注释，感受一下它是如何工作的。

有了上面的更新，你应该能够点击左侧菜单中的链接，并在应用程序中导航。

# 限制登录用户的路由

大多数应用程序都有某种身份验证，以便访问应用程序的某些区域。让我们做同样的事情(以一种非常基本/虚假的方式来保持教程的简单)并且只允许“登录”用户访问`/app`路线。

我将使用`localStorage`来保存一个用户名，并模仿该应用程序跟踪一个认证用户。

> 显然，在合法的应用程序中，会有一些实际的登录机制、服务器端或第三方身份验证/授权、集中式用户/状态管理、保存到本地存储的任何身份验证令牌的到期日期，以及超出本教程范围的其他安全协议。
> 
> 本教程是为了让事情进展得过于简单，但不应该成为现实生活中应用程序的蓝图。拉斐尔·布扎托·德·坎波斯指出了我之前解释中的一些混乱🙌

## 登录组件

首先，我们需要制作一个非常基本的登录组件。因此，创建一个名为`Login.js`的新文件，并将下面的代码粘贴到其中。

该组件让用户提供用户名并点击“登录”，将用户名保存到`localStorage`并将用户重定向到`/app`，即允许他们进入应用程序。

为了在`/`路径上呈现`Login`组件，您需要更新`routes.js`中正确的路径配置。

## 注销按钮

为了让测试应用程序和路由如何工作变得简单，我们还可以在`App.js`文件的左侧栏添加一个注销按钮。

## 测试路线功能(到目前为止…)

好的，让我们看看到目前为止路由是如何工作的。

*   转到`/`路线，输入姓名，点击“登录”。你应该被引导到`/app`路线。
*   访问`ROOT (/)`路线，您应该会被重定向回`/app`，因为您已经登录。
*   点击“注销”按钮，这将把您重新引导回`/`路线。
*   尝试进入`/app`路线*——哎呦！我们仍然需要限制“未经认证”的流量。*您已不再登录，因此应该无法访问这些路线！！！

## 不允许未经身份验证的用户访问应用程序路由

为了防止未经身份验证的用户(在本例中，在`localStorage`中没有`user`的任何访问者)，我们需要进行更新，将未经身份验证的流量重定向回登录屏幕。

*你也可以显示一个警告屏幕来代替重定向，但是在这个例子中我们只是重定向。*

我见过的许多例子都使用了某种类型的`ProtectedRoute`高阶组件，我发现这是次优的，而且在使用大量路由时处理起来有点烦人。

因此，与其保护每一个单独的路由，我们可以通过用一个组件包装它们来保护路由的一部分，该组件检查经过身份验证的用户，然后正常地呈现路由或者重定向到另一个页面。

对于这个简单的例子，我们所要做的就是更新当前只使用`RenderRoutes`组件重定向未认证流量的`APP`路由配置。

因此，打开`routes.js`，导入`Redirect`组件，并用以下内容更新`ROUTES`:

现在，确保你已经退出并尝试访问`/app`路线，它不会让你，胜利！

# 就是这样！

显然，这种范式可以扩展到处理任何数量的高级/奇怪的路由设置，但希望本基础教程向您展示如何在 React 中开始使用可靠的路由。

更高级的例子，你可以[看看这个回购](https://github.com/ryanjyost/react-spa-starter)。

# 资源

*   [React 路由器官方文档](https://reacttraining.com/react-router/web/guides/quick-start)
*   [React SPA 启动器](https://github.com/ryanjyost/react-spa-starter)
*   [React 路由器快速启动](https://reacttraining.com/react-router/web/guides/quick-start)
*   [React 路由器路由配置示例](https://reacttraining.com/react-router/web/example/route-config)
*   创建-反应-应用程序
*   [React 路由器基本示例](https://reacttraining.com/react-router/web/example/basic)