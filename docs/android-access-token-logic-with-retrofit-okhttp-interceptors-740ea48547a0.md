# Android 访问令牌逻辑和改进的 OkHttp 拦截器

> 原文：<https://betterprogramming.pub/android-access-token-logic-with-retrofit-okhttp-interceptors-740ea48547a0>

## 使用 OkHttp 拦截器的访问令牌头逻辑

![](img/239145d78c4af59727b8bb9346f85f32.png)

房间图片，来自 [OkHttp 拦截器文档](https://square.github.io/okhttp/features/interceptors/)

[翻新](https://square.github.io/retrofit/)是一个强大的库，用于处理 Android 应用程序中的 HTTP 请求。

> “Android 和 Java 的类型安全 HTTP 客户端。”根据[改装官方文档](https://square.github.io/retrofit/)

如果没有 OkHttp 库[的话，这个改进就不会那么强大。`OkHttp`是一个 HTTP 客户端，帮助你更快的加载资源，节省带宽。它还允许您拦截请求、监控它们并重写它们。](https://square.github.io/okhttp/)

今天，我将向您展示我们如何使用`OkHttp Interceptors`来减少样板代码。请求的某些部分是相同的，所以使用拦截器，您可以添加它们，而不必真正担心它们。本文的一个例子是添加和刷新`Access Token`。

# OAuth 简介

OAuth 是一种让用户在某些特定操作中验证自己身份的方法。大多数服务器使用这种类型的身份验证，最常见的实现是使用令牌。每个用户都会收到两种类型的令牌:

*   访问令牌(用于向服务器发送请求时进行身份验证)
*   刷新令牌(用于在访问令牌到期时刷新它)

因此，在每个请求中，您需要提供访问令牌来验证您自己，并被允许调用特定的 API。访问令牌最多持续 30 分钟。要看执行。刷新令牌最长可持续 200 天，具体取决于实施情况。

使用拦截器，我们不需要为每个请求手动添加一个访问令牌；拦截器可以为我们做到这一点。此外，当访问令牌过期时，它可以刷新它，然后再次发送请求。在示例中，我们将看到如何做到这一点。

# OkHttp 拦截器

拦截器有两种类型:

*   应用程序级拦截器(使用方法`addInterceptor()`添加在应用程序代码和 OkHttp 核心库之间)
*   网络层拦截器(在 OkHttpCore 库和服务器之间添加，使用一种方法`addNetworkInterceptor()`)

对于访问令牌，包括和刷新，我们将使用应用程序级拦截器。首先，让我们添加一些依赖项:

```
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.okhttp3:okhttp:5.0.0-alpha.3"
```

**注意:**检查这些依赖项是否有更新的版本。

现在我们要通过实现`Interceptor`接口来创建一个拦截器:

**注意:**在这个例子中，我使用的是 Dagger-Hilt，这超出了本文的范围。我选择这个是因为这是最常用的 DI 库。

现在我们要像这样实现`intercept`函数:

通过`chain.request()`,我们获得当前请求，然后获得访问令牌。我们通过添加一个访问令牌作为头部来处理请求，并发送该请求。

现在，如果响应代码具有值`HTTP_UNAUTHORIZED`或`401`，这意味着访问令牌无效，因此我们尝试刷新访问令牌。但是首先，我们通过比较当前和共享首选项中保存的最后一个来检查其他线程是否获得了新的访问令牌。如果它们相同，我们刷新令牌并继续请求。

然后，当我们想要将它添加到 OkHttpClient 时，这是在添加到改型之后，我们这样做:

```
OkHttpClient.Builder()
    .addInterceptor(authInterceptor)
    .build()
```

这是非常重要和相当简单的逻辑。希望你喜欢！
你可以在[我的 GitHub repo](https://github.com/Giga99/MediumRepos) 中找到所有的源代码。

# 结论

有很多用例可以使用拦截器，不仅仅是为了刷新访问令牌。它们是一种方便的机制。例如，您可以使用它们进行错误处理，添加除访问令牌之外的其他头，gzip 您的请求，等等。

```
**Want to Connect?**You can connect with me on:
[GitHub](https://github.com/Giga99)
[LinkedIn](https://www.linkedin.com/in/igor-stevanovic/)
[Twitter](https://twitter.com/igor_s1999)
[Portfolio website](https://giga99.github.io/portfolio-website/)
```

如果您想了解更多关于 Jetpack Compose 的信息，请阅读以下文章:

[](/implement-horizontal-and-vertical-viewpager-in-jetpack-compose-a7a91f2ac746) [## 在 Jetpack Compose 中实现水平和垂直 ViewPager

### 在 Jetpack 撰写中实现 ViewPager

better 编程. pub](/implement-horizontal-and-vertical-viewpager-in-jetpack-compose-a7a91f2ac746) [](/jetpack-compose-request-permissions-in-two-ways-fd81c4a702c) [## 在 Jetpack Compose 中请求权限的两种方法

### 在 Jetpack Compose 中请求权限的最佳方式

better 编程. pub](/jetpack-compose-request-permissions-in-two-ways-fd81c4a702c) 

# **资源**

 [## 加装

### 接口方法及其参数上的注释表明了如何处理请求。请求方法每隔…

square.github.io](https://square.github.io/retrofit/)  [## 概述- OkHttp

### HTTP 是现代应用程序联网的方式。这是我们交换数据和媒体的方式。高效做 HTTP 让你的东西…

square.github.io](https://square.github.io/okhttp/) [](https://square.github.io/okhttp/features/interceptors/#choosing-between-application-and-network-interceptors) [## 拦截器- OkHttp

### 拦截器是一种强大的机制，可以监控、重写和重试调用。这里有一个简单的拦截器，它记录…

square.github.io](https://square.github.io/okhttp/features/interceptors/#choosing-between-application-and-network-interceptors)