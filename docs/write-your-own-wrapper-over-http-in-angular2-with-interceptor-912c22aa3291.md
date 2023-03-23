# 用 Angular 2+和 Interceptor 编写自己的 HTTP 包装器

> 原文：<https://betterprogramming.pub/write-your-own-wrapper-over-http-in-angular2-with-interceptor-912c22aa3291>

## 在您的应用程序中创建自己的 HTTP 服务

![](img/79c8c2446d00deb2ad864f14a857b6b5.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇文章中，我们将创建我们自己的 HTTP 服务，这将是非常容易的，可重用的，你需要在你的应用程序中编写更少的代码。

同样在本文中，我们将创建我们自己的本地存储服务，它将用于我们自己的 HTTP 服务。该服务将拦截我们的请求，并将数据(令牌)保存在本地存储中。

那么，让我们开始…

# 创建本地存储服务

我们需要首先创建一个本地存储服务，为此，在您的`app/src`目录中创建一个名为`services`的文件夹。因此，您的目录将是:`app/src/services`。

在服务目录中，创建一个名为`localStorage.service.ts`的文件，并粘贴以下代码:

在上面的代码中，我们创建了我们自己的本地存储服务，它有类似于`get()`、`set()`、`clear()`和`remove()`的方法，这些方法将在任何组件中使用。

# 在我们的应用中注册本地存储服务

让我们在我们的`app.module.ts`文件中注册这个服务。

`app.module.ts`:

# 创建 HTTP 服务

现在，让我们创建我们的 HTTP 服务。在这个服务中，我们将使用我们的`LocalStorage`服务。为此，在目录`src/app/services`中创建一个文件`http.service.ts`并粘贴以下代码:

`http.service.ts`:

在上面的代码中，我们创建了自己的 HTTP 服务。我们还创建了一个拦截器，用于每个 HTTP 请求/响应。

在上面的代码中，我们创建了自己的方法(GET、POST、PUT、DELETE ),就像 HTTP 方法一样。现在我们将在组件中调用我们自己的方法。

# 在 app.module.ts 中注册 HttpService

在文件`app.module.ts`中，我们已经创建了一个方法`httpFactory()`，它使用了上面的`providers[]`数组。

# 用更少的代码使用我们自己的 HTTP 服务

让我们在现有组件中使用该服务:

完成了，我们已经创建了自己的 HTTP 服务，只需导入我们的组件就可以使用它。

感谢阅读。