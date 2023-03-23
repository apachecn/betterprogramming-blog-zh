# Kotlin 悬挂功能与改造如何结合

> 原文：<https://betterprogramming.pub/a-solution-for-combining-kotlin-suspend-function-retrofit-8d17e1f4886>

## 简化功能的简要指南

![](img/5ffb3f58345eb1faecabf496627117ba.png)

照片由[查德·斯特姆布里奇](https://unsplash.com/@cstembridge?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Kotlin 协程之前，当我们使用 Before 时，我们通常将它与 RxJava 结合使用，通过 Single 来表示创建的请求。

RxJava 中的 Single 表示只包含一个事件的流，所以作为网络请求使用相对合理。

但是，由于是 HTTP 网络请求，返回值必须只有一次，否则会引发异常。用流来形容似乎不太合适。毕竟，由单个事件组成的流听起来很奇怪。

所以对于发起网络请求形式的函数，直接返回数据或者抛出异常更实际。

所以对于科特林来说，这种函数就是挂起函数。并且改造后还支持了暂停功能。

我们来看看`suspend` +改造的网络要求是什么样子的。

这样看起来更直观，调用请求函数直接返回响应数据，或者抛出异常。

但似乎还是有点问题，有点啰嗦，还得写 try-catch，不够简洁优雅。

其实也可以换成`Flow`，但感觉还是不是最好的方案。`Flow`的设计不适合用在这里，肯定有更好的解决方案。

前段时间，我在切换到 Kotlin 协程时遇到了这个问题。当时想到了一个看起来不错的解决方案，分享到这里。

目前要解决的问题是，我们期望使用 suspend 函数发起网络请求，直接返回响应数据，但同时又不想写 try-catch。

我的想法是在改型得到请求后，构建一个响应类来包装数据和异常，调用者通过这个类得到数据或异常。其他的还是可以按照前面的逻辑，写法和下面差不多:

`Response`就是上面说的包装器类，`onSuccess`和`onError`代码块对应成功或失败，看起来比以前简单多了。

下面是怎么做的。

由于需要添加返回类型进行改造，所以需要先创建一个与`Response`对应的`CallAdapterFactory`。获得响应数据或异常后，构造一个`Response`,并将数据或异常存储在其中。大致就是这个思路，比较简单。那么我们就一步步来看一下具体的实现。

# 反应

我们先来看看上面说的`Response`是怎么定义的。

这个比较简单，`success`表示请求是否成功，这个是在`Response`构造好之后外部分配的。泛型`S`代表请求成功后返回的数据类型，泛型`E`是请求成功但接口返回错误数据的类型。

`ErrorResponse`定义如下。

考虑到对调用者的友好性，错误数据被限制在至少一个`errorMessage`。

# 创建 CallAdapterFactory

首先，创建一个扩展`CallAdapter.Factory`并实现`get`方法的`ResponseCallAdapterFactory`。

改型将调用这个方法来尝试获得一个对应类型的`CallAdapter`，并使用它来构造最终的返回值。

# 确定请求返回类型是否为回应

在这个`get`方法中，你需要首先确定它是否是返回类型。我们需要处理输入的参数，否则，返回`null`。

上面的代码主要判断是不是挂起函数，返回值是不是我们上面说的`Response`。此外，您需要获得特定的`Response`类型(`Response`被设计为可继承的)和泛型类型。

# 构建 CallAdapter

代码按照上面的逻辑来到这里，意味着我们遇到了一个需要处理的函数。这时我们需要结合上面得到的类型，创建并返回对应的`CallAdapter`对象。

上面返回的`ResponseCallAdapter`也是我们创建的类。

这个类主要用于确定处理类型和建立一个`Call`对象。

# 创建响应呼叫

`ResponseCall`需要继承`Call`并设置泛型类型。

大部分方法可以委托给`delegate`对象，其中`execute`会直接抛出异常，因为这里是`suspend`函数。

主要逻辑还是在`enqueue`功能里。

仍然首先调用 delete 方法的 enqueue 方法。`Callback`将包含请求结束的`retrofit2.Response`，我们将让它继续构建我们的`Response`。

另外，一般情况下，服务的接口数据类型是约定不变的，即所有响应数据都有相同的`ErrorEntry`。然后为了方便，可以定义一个具体的服务`Response`，服务的`Error`泛型类型就是这个`ErrorEntry`。这就是为什么上面的`Response`设计成`open`的原因。

例如，idea 的公共 API 具有相同的`Error`类型。

我们可以为概念服务定义一个`NotionResponse`。

# 结论

前一段时间学 Compose 的时候，我拿了一个观念 API，做了一个开源项目叫 NotionLight(一个轻量级的观念客户端)。这篇文章的内容也是我在做这个项目的时候。遇到的问题，上面所有的代码，具体的用例也都在这个开源项目里。

```
**Want to Connect?**Here's [my GitHub](https://github.com/0xZhangKe/NotionLight) to see the specific code.
```