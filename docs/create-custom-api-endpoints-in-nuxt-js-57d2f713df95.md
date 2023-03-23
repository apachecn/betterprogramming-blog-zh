# 在 Nuxt.js 中创建自定义 API 端点

> 原文：<https://betterprogramming.pub/create-custom-api-endpoints-in-nuxt-js-57d2f713df95>

## 了解如何在 Nuxt.js 中创建自定义 API

![](img/d6e395b02008c61431185d0ec687e2df.png)

道格拉斯·洛佩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

您知道可以在 [Nuxt.js](https://nuxtjs.org/) 中创建自定义 API 端点吗？这意味着您不必运行独立的服务器。相反，您可以在您的 Nuxt 应用程序中直接这样做！

在本文中，您将看到如何做到这一点。您将创建一个简单的 Express 服务器，它带有一个端点，允许您从数据库中获取数据。

让我们看看如何设置自定义 API 服务器！

# serverMiddleware 属性

Nuxt 有一个名为`serverMiddleware`的属性，允许您在应用程序内部创建额外的 API 路由。

它是如何工作的？Nuxt 创建了一个`connect`的实例，它是 Node.js 的中间件层，由于有了`connect`实例，你可以创建一个具有不同端点的自定义 API。

当您希望将 API 放在与 Nuxt 应用程序相同的位置时，`serverMiddleware`属性非常方便。当您想要构建一个概念验证或者当您有一个小的 API 时，它也是一个有用的特性。

# 如何创建 API

第一步是在项目的根目录下创建一个新文件夹。按如下方式创建新文件夹:

```
mkdir api
```

在这个文件夹中，创建一个名为`courseAPI.js`的新文件。`courseAPI.js`文件是构建 Express 服务器的地方。

对于这个例子，让我们假设您有一个存储 web 开发课程的数据库。因此，您将构建一个允许您从数据库中检索所有课程的端点。

打开文件`courseAPI.js`并编写以下代码:

这段代码中发生了很多事情。你是:

*   导入构建 API 服务器所需的包(`Express`和`graphql-request`)。
*   创建一个新的 Express 实例并使用`express.json()`中间件。
*   创建一个传递虚拟端点的新 GraphQLClient 实例。
*   构建`/getCourses`端点。
*   使用新创建的 GraphQLClient 发出请求，同时传递`allCourses`查询。
*   从数据库中返回课程。
*   正在导出应用程序。

现在 Nuxt 中有了一个简单的 Express 服务器。下一步是注册`serverMiddleware`。转到`nuxt.config.js`文件并添加以下属性:

```
export default {
  serverMiddleware: ['~/server-middleware/courseAPI.js']
}
```

现在，如果您启动应用程序并转到`/getCourses`路线，您应该会看到数据库中的所有课程。

*注意:如果要添加特定的路径或前缀如* `*/api*` *，需要使用对象形式并指定路径。下面的代码片段演示了带有特定路径的对象表单。*

```
export default {
  serverMiddleware: [
    { path: '/api', handler: '~/server-middleware/courseAPI.js' }
  ]
}
```

在本教程中，您将看到没有对象表单的第一个选项。但是，请随意使用适合您需求的选项。

# 如何使用 API 端点

既然已经有了自定义 API 端点，就可以开始使用它了。下面的代码说明了获取数据的一种方式。

下面的组件从数据库中获取课程，并将它们呈现在页面上。这是非常基本的，其目的是简单地说明如何使用自定义端点。在现实生活中，你可以用更好的方式呈现数据。

值得注意的是，在现实世界的应用程序中，您不会硬编码 URL。同样，当你使用`fetch`钩子时，你需要传递绝对 URL。所以像`fetch('/getCourses')`这样的操作会给你一个错误。

# 结论

在本文中，您在 Nuxt 中构建了一个简单的 Express 服务器。但是，您可以在它的基础上创建一个更复杂的 API。

但是问题是——特别是如果它是一个复杂的 API——这是正确的方法吗你想在 Nuxt 里面有 API 吗？

有几点需要考虑，比如:

*   扩展 API
*   具有单一故障点

感谢阅读。