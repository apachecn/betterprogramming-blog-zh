# 角 HTTP 拦截器介绍

> 原文：<https://betterprogramming.pub/angular-http-interceptors-what-are-they-and-how-to-use-them-52e060321088>

## 它们是什么以及如何使用它们

![](img/70f75f82d8c850bfab8468d332f219df.png)

泰勒·米利根在 [Unsplash](https://unsplash.com/search/photos/ruby?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在这篇短文中，我将解释什么是 HTTP 拦截器以及如何最好地使用它们。我分享的信息是基于 Angular 的官方文档，我强烈建议你看看。你可以在这里找到:[拦截请求和响应](https://angular.io/guide/http#intercepting-requests-and-responses)。

如果你想知道，这篇文章的内容对 Angular 2，4 和 6 有效，现在只称为 Angular。1.0 版本，或 AngularJS，现在已经过时了。

# 拦截器简介

使用 HTTP 拦截器有很多好处。您可以使用它们来设置标题(例如 JWT)，重定向错误响应，并且通常会帮助您避免代码重复。

让我们来看看如何设置 HTTP 拦截器。我将在这里跳过如何创建 Angular 项目的细节，但是如果你需要帮助，你可以参考文档。我会在这篇文章的结尾链接完整的代码，所以在你读完之后可以随意查看。

# 实现拦截器

编写 HTTP 拦截器非常简单，您所要做的就是遵循以下步骤:

1.  创建一个实现`HttpInterceptor`的类，然后用`@Injectable`来修饰它
2.  提供拦截器

如果你是一个 Angular 开发者，你已经知道如何提供服务，但是有一个问题，对于`HttpInterceptor`，你必须在`AppModule`中提供服务。

你也可以这样做:

```
{ provide: [HTTP_INTERCEPTORS](https://angular.io/api/common/http/HTTP_INTERCEPTORS), useClass: NoopInterceptor, multi: true }
```

遵循官方文档中的说明并创建一个新的数组来存储所有的拦截器是一个很好的实践，这样您只需要向数组中添加新的拦截器，然后在`AppModule`的根中提供数组。

拦截器的工作方式是首先查看请求，然后调用 next。以下拦截器是一个无操作拦截器，它什么也不做:

```
intercept(**req**: HttpRequest<any>, next: HttpHandler):
    Observable<HttpEvent<any>> {
    return next.handle(req);
  }
```

据我所知，你不能配置拦截器在某些条件下激活(如果你这样做，请让我知道如何！).您可以通过检查 **req** 参数来配置一个拦截器，使其仅针对某些路由激活，如下例所示:

如果您有一个使用 JWT 作为身份验证方法的应用程序，您可以编写一个 HTTP 拦截器来设置 JWT 报头(如果可用的话)。使用 **intercept** 方法，您可以像下面这样做:

感谢阅读！我希望这能对你有所帮助。

完整的代码可以在这里找到:【https://stackblitz.com/angular/kmeqdgdrenj】T2