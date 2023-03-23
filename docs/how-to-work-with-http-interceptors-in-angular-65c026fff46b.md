# 如何在 Angular 中使用 HTTP 拦截器

> 原文：<https://betterprogramming.pub/how-to-work-with-http-interceptors-in-angular-65c026fff46b>

## 它们是什么，为什么你应该使用它们？

![](img/dadd92d7b36594ccaa8d5762adbc3ef3.png)

我从事 Angular 已经有一段时间了，几乎开发过它的每个版本。令人惊讶的是每个版本都有新功能。引起我注意的一个特征是`HttpInterceptor` **。**它最初是在 Angular 4.3 中引入的，非常有用。

# 拦截器简介

拦截器提供了一种拦截传出请求或传入响应的方法。除了前端之外，它们非常类似于中间件，具有类似 Express 的框架。拦截器对于缓存和日志记录等功能非常有用。

> HttpInterceptor 提供了一种方法来拦截 HTTP 请求和响应，以便在传递它们之前对它们进行转换或处理。

# 基本实现

创建拦截器非常简单。您必须创建一个`@injectable` 的类，并实现`HttpInterceptor`:

```
@Injectable()
export class ApiInterceptor implements HttpInterceptor { constructor() {}intercept(req: HttpRequest<**any**>, next: HttpHandler): Observable<HttpEvent<**any**>> {
    **const** authReq = req.clone({
        //add original request and/or set/add new headers 
    });
});
  **return** next.handle(authReq);
 }
}
```

这将`req` 和`next` 作为参数，并将`HttpEvent` 作为可观测值返回。

之后你需要在`AppModule` 中这样提供它:

```
@NgModule({
  bootstrap: [AppComponent],
  imports: [...],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: ApiInterceptor,
      multi: true
    }
  ]
})
export class AppModule {}
```

这是一个非常简单的`HttpInterceptor`的基本实现。

# 为什么应该使用 HttpInterceptors？

`HttpInterceptors`可以执行各种任务，从认证到日志记录、设置或添加头、HTTP 请求和响应、通知、错误处理，以及在每个 HTTP 请求上显示加载器。如果没有拦截器，开发人员将不得不为每个 HTTP 客户端调用手动添加这些任务。

## 1.设置标题

我们可以很容易地添加**授权**头，这样我们就不必在每个请求中分别添加它们:

```
const addHeaders= req.clone({ 
   headers: req.headers
     .set('Authorization', `Bearer $token`)
     .set('Content-Type', 'application/json')
});
return next.handle(addHeaders);
```

正如您所看到的，在拦截器中操作头很简单。

## 2.显示加载程序

我们可以在拦截器中为每个请求添加一个加载器。为此，我们添加了一个具有显示和隐藏加载器方法的加载器服务。然后，我们可以显示每个请求的加载程序，并在请求完成时隐藏它:

```
const loaderService = this.injector.get(LoaderService);

loaderService.show();

return next.handle(req).pipe(
  delay(5000),
  finalize(() => loaderService.hide())
);
```

这是一个更好的解决方案——我们不必为每个 API 请求调用加载器服务。

## **3。通知**

我们可以在每次响应状态为 200 或 201 时显示一个通知，这表示成功。

下面是一个对象创建的示例:

```
return next.handle(req).pipe(
  tap((event: HttpEvent<any>) => {
    if (event instanceof HttpResponse && event.status === 201) {
      this.toastr.success("Object created.");
    }
  })
);
```

每当响应状态为 201 时，这将显示成功烤面包机。这非常有用，因为您不必在每个 API 请求中显式添加 toaster。

## **4。错误处理**

有多种情况会导致拦截器出错。对于 http 请求，我们可以在管道函数(它结合了多个函数)中使用 retry 操作符。

我们使用来自`**RxJS**` 的`Retry`来重新订阅可观察值，作为回报，我们得到一个成功的结果:

我们可以检查错误的状态，并在此基础上决定如何处理它。

在上面的例子中，我们在检查异常状态之前使用了`retry`操作符。如果状态不是 401，那么我们将抛出一个烤面包机错误。这就是我们在拦截器中处理错误的方式。

有了`HttpInterceptors` **，**我们还可以做其他事情，比如分析、URL 操作等等。

差不多就是这样。

感谢您的阅读。希望有帮助！