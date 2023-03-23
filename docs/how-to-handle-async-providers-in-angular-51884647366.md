# 如何在 Angular 中处理异步提供者

> 原文：<https://betterprogramming.pub/how-to-handle-async-providers-in-angular-51884647366>

## 解释从配置文件加载基本 API URL 的值并将其与`InjectionToken`一起使用的问题

![](img/4158d53d50ee630fdbf8ff76105746a6.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/angular-web?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你不确定这个标题是什么意思，不要担心。让我解释一下。
我最近遇到了一个问题，我们需要从一个配置文件中加载一个基本 API URL(例如，http://localhost:9000)的值，并在我们的应用程序中使用带有`InjectionToken`的值。

我将在这篇博文中解释我最终做了什么。

# APP_INITIALIZER 拯救世界

你可能对`APP_INITIALIZER`很熟悉。如果没有，我推荐阅读这篇关于角度深度的文章。

`APP_INITIALIZER`让你在应用程序的其余部分加载之前，就可以进入 Angular 的引导过程。

## 服务

让我们创建加载配置的服务。

很简单，对吧？`getBaseUrl()`对`/assets/config.json`执行一个`GET` 请求，并使用一些 [RxJS](https://rxjs-dev.firebaseapp.com/) 魔法从结果 JSON 中提取属性`baseUrl`。

它还将结果值存储在私有属性`baseApiUrl`中，该属性由 getter `baseUrl`公开。我添加了`loadConfig()`以防需要加载多个值。`loadConfig()`也是我们将在`APP_INITIALZER`中使用的。

## 应用程序模块

`AppLoadModule`将提供`APP_INITIALIZER`。代码也非常简单。

我们添加了一个工厂函数，它将我们的服务作为第一个参数。在这个函数中，我们返回一个返回承诺的函数——这个承诺就是我们想要做的事情。

感谢 RxJS，我们可以在整个应用程序中使用 RxJS 的优点，并在需要时将其转化为承诺。我们也可以在工厂函数中直接使用`HttpClient`,但是你可以在你的应用程序中共享的代码越多越好。

接下来我们要做的是`provide` `APP_INITIALIZER`。

*   `useFactory`告诉 Angular 使用哪个工厂功能。你也可以用内联方式写。
*   `deps`告诉 Angular 处理工厂需要哪些服务或其他注入。您输入`deps`的顺序会影响您对函数参数的排序。

`AppLoadModule`到此为止。

## 配置模块

我们需要的最后一个模块是`ConfigModule`。在我看来，为此创建一个单独的模块是没有必要的，但是可以在更大的应用程序中清理这些东西。

我们在这里所做的就是提供一个名为`BASE_API_URL_TOKEN`的`InjectionToken`。我在整个应用程序中使用它来告诉服务 API 服务器在哪里。

我们使用工厂是因为我们需要访问`ConfigService`。`useFactory`和`deps`做的事情和上面一样，不同的是提供者的工厂不支持承诺，这就是这篇文章之所以必要的全部原因。

现在剩下要做的就是`import`你选择的模块中的两个模块，你就可以开始了。

# 摘要

我不禁想到，有或者应该有更好的方法来做到这一点。我也认为我在这里描述的解决方案没有那么糟糕。

如果您想查看我的完整示例，我创建了一个 [GitHub 库](https://github.com/beyerleinf/angular-async-module-providers)，您可以自己克隆、运行和检查它。

总而言之，我们真正需要做的就是在应用程序的其余部分可用之前，使用 Angular 的`APP_INITIALIZER`来加载配置，并将它们保存在服务中。一旦我们在应用程序中包含了它们，提供它们就相当容易了。

如果你对此有任何建议或者更好的解决方案，请写在下面。

感谢阅读！