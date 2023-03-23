# 建模 Typescript 接口以支持角度 HTTP 请求

> 原文：<https://betterprogramming.pub/modeling-typescript-interfaces-to-support-angular-http-requests-add452e15b7f>

## 如何在 AJAX 中建模数据响应

![](img/accd2f2f5755e80ca45b17d2359c55bc.png)

照片由[负空间](https://www.pexels.com/photo/gray-laptop-computer-showing-html-codes-in-shallow-focus-photography-160107/)上的[像素](https://www.pexels.com/photo/gray-laptop-computer-showing-html-codes-in-shallow-focus-photography-160107/)拍摄

Angular 单页应用程序使用大量 Ajax 请求与 web 服务器通信，并从数据库中检索数据。在本文中，我将给出一些关于如何管理数据响应的提示。这将帮助您为 Ajax 流建立一个良好的设计层。

![](img/5510ed1603dd175f8ae205bc0930114e.png)

# 基础知识:Observables 和 HttpClient

从 Angular 4 到 Angular 8，框架开始大量使用 Observables，并承诺处理从 AJAX 获取的数据。Angular 的一些版本似乎更喜欢承诺，其他一些版本更喜欢观察，但目标基本上总是相同的。我们有一个服务方法，从我们的 Angular 组件调用，我们希望在服务器弹出数据时得到通知。

承诺和可观察是完全不同的。请记住，**承诺是简单的对象**，其中*满足*和*拒绝*可能的状态，而**可观察值是请求的“流”**。如果您需要控制这些请求，Observables 的用途会更加广泛。我不会在承诺上花更多的时间，但是一定要在可观察到的和承诺之间小心选择。我们将集中讨论 Observables 的实现。我们之所以关注 Observables，是因为 Observables 在当前官方的 devs 角度文档中是作为默认实现出现的。

# HTTP JSON 响应:非结构化版本

当您的目标是从 Angular 服务中发出 Ajax 请求时，您可以开始编写如下代码:

来自 Angular 服务的基本 HTTP 请求，返回 observable <any>。</any>

这里的`getCandies()`方法非常简单，没有结构。我们使用类型推断向调用者返回一个通用的可观察对象，因为它是 HttpClient 中`get()`方法的返回类型。那么，这里的可观察对象处理的是什么类型呢？它只是一个打字稿“任何。”

这段代码可以工作，但是当组件订阅了`getCandies()`方法后，它会从中获得什么呢？如果 HTTP 状态代码是 500 或任何其他错误，则肯定是一个错误。如果成功了呢？很简单，你得到一个“任何”对象。就像说我们要用 Java 里的 Object 类。当我们访问对象的字段时，我们不知道里面有什么。然后，我们很容易得到未定义的值(比如 Java `NullPointerException`)。

# 处理响应的可观察接口:第一种方法

因此，我们的目标是使用 Typescript 接口来表示提取的数据的结构。假设我们想从 JSON 中获取糖果:

```
[
 {
    color : “yellow”,
    flavour : “lemonade”,
    calories : 24
 },
 {
    color : “brown”,
    flavour : “chocolate”,
    calories : 89
 },

  ...]
```

很容易编写这样的代码:

并像这样更新服务方法实现:

一个更好的实现，但仍然太松散。

所以，现在我们在第一次代码修改中获得了一些好处。现在我们有一个接口来映射字段和定义糖果结构。这很好，因为我们的组件现在将接收一个糖果，而不是一个通用的“任何”对象。组件将很高兴知道它得到什么和访问什么:

这是我在几个 Angular 项目中见过的最流行的实现。它之所以好，是因为它编码快如闪电，易于理解，而且大多数时候我们不再需要它了。

# 为什么这还不够:最终方法

当您的应用程序开始增长时，我们开始编写各种各样的接口，它们之间没有任何共享。Typescript 不是 Java，但它对 OOP 操作和层次结构提供了很好的支持。我们可以使用继承，假设有一个响应的泛型实现，然后子类化这个泛型类。这是个好主意，但我们可以做得更好。

如果你擅长类建模，你知道我们可以接近类似于 Java 集合的东西。这些结构可以处理和容纳几乎任何东西。唯一的要求是数据必须是对象子类。为了工作，Java SDK 使用泛型。泛型是某种特定类的“占位符”。因此，如果我们定义一个泛型的数组列表，我们可以很容易地在数组列表声明中选择要处理的类。

在 Angular HTTP 请求中，泛型是处理与使用相同模型生成的 JSON 具有相同结构的数据的最佳选择。这种实现实际上取决于您的 web 服务规范，并且不是强制性的。但是如果你可以控制你的后端(或者你可以和后端开发者讨论和定义通用的响应结构)，你可以使用泛型来开发前端应用。假设您的后端希望在任何情况下对所有调用做出如下 JSON 响应:

```
{
    success : true,
    errorMessage : "Something went wrong", // optional
    data : [...] // changes according to WS specs
}
```

为所有 web 服务调用建立一个公共的 HTTP 响应可能是一个好主意。它将代表一种对你的微服务的默认响应，并且很容易读写文档。

那么，回到前端，我们如何在 HttpClient Observables 中处理它呢？这里的问题是数据——它有许多形式，并且与不同的模型相关。使用泛型来映射“数据”类型、布尔类型以及共享和公共字段的字符串:

好方法:我们的默认 AjaxResponse 接口。

在这段代码中，我们定义了一个 AjaxResponse 接口，它将泛型类型反映到数据字段中。很百搭。您甚至可以考虑一个以上的通用实现，因此它非常适合各种用途。现在，每当我们需要生成一个包含糖果、饼干或其他任何东西的可观察对象时，我们只需指定一般类型:

我们的更新`getCandies()`方法

泛型非常有趣，并且提供了一种很好的方法来“概括”类型的实现。请记住，泛型是改变类型或适应特定环境的唯一方法——“任何”类型都不是一回事。方法中的“any”参数可以承载任何种类的参数类型，但它会使您的编译器丢失类型引用。这是因为对象转换为“任何”

JSON 请求、参数和响应确实没有类型，因为 JSON 是一种不匹配的 JavaScript 类型。但是如果你想设计一个好的应用程序，如果你不想失去数据控制和管理，你就不能避免接口。

亲自尝试一下，您会喜欢 Typescript 泛型实现。