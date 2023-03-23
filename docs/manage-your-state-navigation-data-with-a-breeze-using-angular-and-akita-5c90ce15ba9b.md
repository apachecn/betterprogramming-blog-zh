# 使用 Angular 和 Akita 管理您的州导航数据轻而易举

> 原文：<https://betterprogramming.pub/manage-your-state-navigation-data-with-a-breeze-using-angular-and-akita-5c90ce15ba9b>

## 提供给秋田用户的简洁套装

![](img/b725194bd7edb216022cbf5c28a9db53.png)

[秋田 ng-router-store](https://github.com/datorama/akita-ng-router-store) 是一个提供给秋田用户的简洁包，它能够将 Angular 路由器状态绑定到秋田商店。这种方法的好处是:

*   路由器状态存储在一个名为`router`的 Akita 存储中，它是 URL、状态数据参数、查询参数等信息的唯一来源。
*   我们的组件不需要熟悉数据源。可以在任何一个查询中注入`RouterQuery`来创建一个封装的派生选择器。
*   该库公开了 Akita 风格的查询，使您不必处理所有必需的样板代码。
*   我们可以在 Redux devtools 中检查当前路由器状态，并执行“时间旅行”。

首先，安装软件包并将其以及 Akita 的 devtools 模块添加到 app 模块:

此时，您应该在 Redux devtools 中看到路由器存储，并且您可以随意使用下面的查询 API:

例如，假设我们想要基于 URL `id`参数查询当前实体，我们可以在我们的`ArticlesQuery`类中执行以下操作:

并在我们的组件中使用它:

瞧，轻松的导航管理！

最后一点:秋田的新版本刚刚登陆 Github！你可以在这里查看突破性的变化和新功能。