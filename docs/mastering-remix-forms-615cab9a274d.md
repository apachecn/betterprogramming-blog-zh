# 掌握混音形式

> 原文：<https://betterprogramming.pub/mastering-remix-forms-615cab9a274d>

## 看看关键的混音功能

![](img/84e09ce2fcfcded024f16fb9d08af525.png)

作者图片

Remix 已经迅速成为我最喜欢的 React JavaScript 框架之一。为什么？它包含了众所周知的已建立的 web APIs 及其渐进增强方法。

开箱即用，Remix 确实执行服务器端渲染。因此，当 JavaScript 不可用时，我们的用户不会看到空白页面。他们将获得相同的 HTML 页面，但与原始的 HTML 体验。页面不会被水合，因为没有 JavaScript 将被执行。

最大的问题是。那么 Remix 如何处理表单呢？通过使用支持良好的浏览器 HTML 表单功能作为基础。只有用户启用了 JavaScript，它才会进一步增强用户体验。

这在实践中是如何运作的？在这篇文章中，我们将看看这个可怕的和聪明的功能。我们不仅向客户交付更少的代码，还支持更广泛的设备。

# 基础知识

如前所述，Remix 包含了默认的 HTML 表单特性。规范很简单，它只是一个发送到服务器的包含用户表单数据的`post`请求。

它有两种味道:

*   **启用 JavaScript 时—** 通信将通过`AJAX`进行。表单提交通过`event.preventDefault()`方法被禁用。然后对表单进行序列化并提交。然后将结果数据返回给客户端。仍然可以执行类似`redirect`的操作。
*   **当 JavaScript 被禁用时—** 它将执行传统的`POST`请求。响应将是 React 服务器呈现的 HTML。

混音将如何处理表单提交？我们需要在表单`root`页面上公开一个`action`函数。

为什么这个函数需要调用`action`？进一步包含 HTML 表单特性，并匹配它的`action`参数。

```
<form **action="/books"**>
```

我们在哪里举办这个？我们可以在边缘上托管它，并根据需要将操作委派到任何地方。我们如何获得用户提交的信息？通过包含在`[request](https://developer.mozilla.org/en-US/docs/Web/API/Request)`对象中的`[formData](https://developer.mozilla.org/en-US/docs/Web/API/Request/formData)`对象。这些也符合网络规范🚀。

让我们看一个微不足道的例子:

在上面的代码中，`action`命名函数是从`Book.js`页面导出的。表单处理程序需要总是放在页面根文件中。表单需要始终与页面相关联，否则，它将无法访问。

# 表单组件

Remix 团队确实提供了他们自己的声明性`Form`抽象。

## 关于它的行为:

*   提交`<Form >`后，所有加载器将被重新加载。
*   它序列化表单元素。
*   它通过`useTransition`钩子提供一些状态反馈。

## 属性

*   **动作:**表单将被提交到的位置。这在大多数情况下可以省略，因为我们将使用相同的渲染页面作为目标。当匹配多个路由时，将只使用最深的匹配路由。我们可以使用`index`参数发布到索引路径。

```
**url -> route action** /accounts?index  -> routes/accounts/index.js
/accounts -> routes/accounts.js
```

*   `**method**` **:** 它决定了要使用的 HTTP 动词:`get`、`post`、`put`、`patch`、`delete`。默认值为`get`。本地 HTML 表单仅支持⚠️的`get`和`post`。其他动词将打破非 JavaScript 方法。
*   `**encType**` **:** 默认为`application/x-www-form-urlencoded`。使用`multipart/form-data`上传文件。
*   `**replace**` **:** 告知浏览器替换历史堆栈中的当前条目。
*   `**reloadDocument**` **:** 即使启用了 JavaScript，也会执行非 JavaScript 方法。

# 错误处理

我们之前已经讨论过一个简单的例子。它没有经过任何验证。表单验证将在哪里以及如何进行？

由于 Remix 确实支持`AJAX`和`Traditional Form Requests`，我们不能依赖客户端来执行这些验证。当 JavaScript 被禁用时，这是不可能的。无论如何，只依赖客户端验证是一种不好的做法。

混音拥抱愚蠢的前端风格。所有的处理和验证都将在服务器上进行。

客户端如何知道一个错误？作为一个好的实践，我们可以返回一个带有键`errors`和所有错误细节的对象。

让我们看一个例子:

React 组件是如何知道的呢？使用`useActionData`挂钩。这个钩子包含了由`action`表单返回的信息。如果确实发生了任何错误，我们应该能够访问`errors`对象。

如基础部分所述，在非 JavaScript 方法中，返回的 HTML 页面会打印出错误。它将由服务器呈现。

验证工具呢？Remix 团队通过开放扩展简化了这个过程。`zod`和`up`都有官方适配器。这是两个最著名的验证库。

# 表单挂钩 API

在上一节中，我们已经看到了如何使用`useActionData`钩子从服务器获得响应。还有更多值得了解的 hooks API。

*   `useSubmit` —返回以编程方式提交表单的函数。该函数采用一个具有`method`、`action`、`encType`或`replace`属性的选项对象。

*   `useTransition` —它告诉我们需要了解的关于表单提交状态的所有信息。我们可以显示加载状态，禁用表单提交，乐观更新…等等。⚠️:重要的是不要把这个和`React.useTransition`混淆。

*   `useFormAction` —使用 React 路由器的相对路径解析`<form action>`的名称。那什么时候有用？当按钮改变`action`和`method`的行为时。

# 包裹

在这篇文章中，我们已经看到了混音形式是多么强大和功能齐全。他们帮助我们快速起步。它们的 API 变得很直观，因为它们非常接近 HTML web 规范。这真的让人放心，因为他们没有试图提出自己的规格，而是拥抱现有的规格。

他们的渐进增强方法确实需要一点时间来适应。然而，我越用它，我就越喜欢它。

我们也不受节点工具的限制或约束。我们所需要的是一个支持`fetch` API 的边缘。这样更容易找到合适的地方举办。

干杯

[](/next-js-vs-remix-analyzing-key-aspects-and-differences-8674beaba695) [## Next.js vs. Remix:分析关键方面和差异

### 通过比较 Remix 和 Next.js 的最新版本，知道哪一个适合你

betterprogramming.pub](/next-js-vs-remix-analyzing-key-aspects-and-differences-8674beaba695)