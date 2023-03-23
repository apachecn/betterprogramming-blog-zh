# Angular: HttpContextToken —处理 HTTP 请求

> 原文：<https://betterprogramming.pub/angular-tweaking-a-http-request-with-httpcontexttoken-41920f3d1e24>

## 用途和用法示例

![](img/269bff93f7d5b615e373265428215d33.png)

在应用程序中，我们经常需要处理一些不同条件的`http`请求。这些条件会导致代码中不同的逻辑。

通常，在 Angular 中，我们在一个实现了 [HttpInterceptor](https://angular.io/api/common/http/HttpInterceptor) 接口的类中这样做。当然，有许多关于服务注入和条件检查的标准实践。但是我们也可以用有角的力量。还是用`HttpContextToken`吧。

> 从名字中可以看出`HttpContextToken`的用途——这个令牌允许您向请求上下文添加额外的数据。

# 一个例子

假设我们有一个应用程序，它的服务器请求带有授权权限。如果我们没有权限，请求就不会被发送。但是我们还必须处理对几个开放的、不需要授权检查的 API 的请求。

解决这个问题一个方法是拥有一个开放 API 的列表，并检查要包含在这个列表中的每个请求 URL。但是那有点开销，让我们用`HttpContextToken`来解决这个问题。

我们有非常简单的`ApiInterceptor`类，带有哑权限检查:

这是一个有请求的服务:

现在我们需要用`HttpContextToken`修改我们的代码。因此，让我们用默认值`false`创建一个`HttpContextToken`令牌的实例，并在拦截器类中使用它。

用代码`request.context.get`你可以得到上下文的参数。因此，我们完成了主要步骤——在请求中添加对开放 API 的检查。

现在，如果`OPEN_API_HTTP_TOKEN`令牌值为`true`，请求将被调用。

让我们进行最后一步，通过设置令牌来修改我们的服务:

就是这样！现在我们的应用程序已经可以使用开放 API 了。

希望你喜欢这一课，谢谢，并与 Angular 呆在一起。