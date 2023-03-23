# 跟进:如何在 Angular 中处理异步提供者

> 原文：<https://betterprogramming.pub/follow-up-how-to-handle-async-providers-in-angular-54957c7349c4>

## 在接下来的文章中，我会看到一个更优雅的解决方案

![](img/d8dde1eaac149dc394dc04e721cdc5db.png)

[Olu Famule](https://unsplash.com/@kehfam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/web-development?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

几周前，我在 Angular 上发表了一篇关于异步提供者的[文章。](https://medium.com/better-programming/how-to-handle-async-providers-in-angular-51884647366)

在这篇文章中，我们将看看кошеверов向我提出的另一个解决方案。

# 我们上次做了什么？

在我之前的解决方案中，我们使用`APP_INITIALIZER`来加载配置，同时 Angular 引导我们的应用程序。此外，我们使用一个`InjectionToken`来提供值。这个解决方案有几个问题，是我在工作中将它添加到项目中时发现的——其中之一是初始化顺序。例如，当另一个模块也在使用`APP_INITIALIZER`时，它经常在我们的模块之前被调用，导致值未定义。所以准备好迎接更好的版本吧。

# platformBrowserDynamic 可以做得更多！

在实现之前的解决方案时，我忽略了一点——我想我应该通读文档——函数 platformBrowserDynamic()将一组提供者作为参数([查看文档](https://angular.io/api/platform-browser-dynamic/platformBrowserDynamic))。完美！

现在，我们可以执行以下操作:

1.  使用本地的`fetch` -API 在`main.ts`中加载我们的配置。
2.  在我们的应用程序被引导之前提供该值，从而确保配置总是可用的(假设源代码也是可用的)。

因为这个解决方案更简单，我们只需要编辑`main.ts`文件。

1.  我们将一切都包装在一个`async function`中，以利用 async/await。
2.  我们使用 JavaScript native`fetch`-API(L12+L13)加载任意配置。
3.  将提供者数组传递给`platformBrowserDynamic()` (L17)。

就是这样。你可以在 GitHub 上查看完整和更新的示例 [repo。](https://github.com/beyerleinf/angular-async-module-providers)

# 结论

总是阅读文件。

感谢阅读！