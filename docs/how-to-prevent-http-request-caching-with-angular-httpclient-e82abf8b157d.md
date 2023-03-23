# 如何用 Angular HttpClient 阻止 HTTP 请求缓存

> 原文：<https://betterprogramming.pub/how-to-prevent-http-request-caching-with-angular-httpclient-e82abf8b157d>

## 通过使用拦截器来避免缓存 HTTP 请求的奇怪行为

![](img/5dce616535838d0d5931e36091ea980c.png)

照片由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我最近在 Internet Explorer 的 Angular 应用程序中发现了一个问题。在浏览应用程序时，我看到一些来自后端的错误数据。使用浏览器开发工具，我注意到我们得到了 GET 请求的缓存结果。这种行为至少可以说是令人惊讶的。显然，这个问题可能发生在任何 web 项目中，而不仅仅是 Angular 应用程序。

为了解决这个问题，我们可以在 HTTP 请求中添加某些头来防止缓存。例如: [Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control) 头，当 HTTP 1.1 服务器指定了非缓存值时，它会阻止对特定 Web 资源的所有缓存。虽然向 HTTP 请求添加新的头很容易，但是在任何发出 HTTP 请求的地方都需要这样做，这意味着有很多文件需要添加这些头。

幸运的是，Angular 有一个方便的概念叫做[拦截器](https://angular.io/guide/http#http-interceptors)，它适合这种任务。拦截器可以转换传出的请求。自从 Angular 团队在 Angular 4.3 中引入了 [HttpClient](https://angular.io/api/common/http/HttpClient#description) 以取代旧的 Http 模块以来，Angular 拦截器就一直可用。请注意，只有使用 HttpClient 发出的 HTTP 请求才能以这种方式被拦截。例如，用标准的[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 或 [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 发出的 HTTP 请求就不能以这种方式被拦截。然而，没有什么理由不在 Angular 应用程序中使用 Angular HttpClient，所以这不是问题。

# 如何创建一个拦截器来禁用 HTTP 请求缓存

1.  创建一个实现`[HttpInterceptor](https://angular.io/api/common/http/HttpInterceptor)`接口的拦截器类。
2.  通过向请求对象添加必要的头来修改`[intercept](https://angular.io/api/common/http/HttpInterceptor#intercept)`函数中的请求。
3.  将拦截器添加到 AppModule 中，为整个 Angular 应用程序注册一次。

```
import { HttpHandler, HttpInterceptor, HttpRequest } from '@angular/common/http';
import { Injectable } from '@angular/core';@Injectable()
export class NoCacheHeadersInterceptor implements HttpInterceptor {intercept(req: HttpRequest<any>, next: HttpHandler) {
    const authReq = req.clone({
      // Prevent caching in IE, in particular IE11.
      // See: [https://support.microsoft.com/en-us/help/234067/how-to-prevent-caching-in-internet-explorer](https://support.microsoft.com/en-us/help/234067/how-to-prevent-caching-in-internet-explorer)
      setHeaders: {
        'Cache-Control': 'no-cache',
        Pragma: 'no-cache'
      }
    });
    return next.handle(authReq);
  }
}
```

在 AppModule 的 [providers](https://angular.io/api/core/NgModule#providers) 部分注册拦截器一次。现在，拦截器应该在每次使用 HttpClient 发出 HTTP 请求时自动追加必要的请求头。

```
providers: [
  {
    provide: HTTP_INTERCEPTORS,
    useClass: *NoCacheHeadersInterceptor*,
    multi: true
  },
  ...
]
```

# 结论

感谢您阅读这篇短文。正如您所看到的，在进行 HTTP 请求时，Angular HTTP 拦截器是一个简单易用但功能强大的工具。此外，拦截器不仅仅可以用于添加头部。