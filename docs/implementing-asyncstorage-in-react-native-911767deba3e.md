# 在 React Native 中实现异步存储

> 原文：<https://betterprogramming.pub/implementing-asyncstorage-in-react-native-911767deba3e>

## 构建智能异步应用程序

![](img/79c96f503a3cdc74fcc186bc33846cd3.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

对于构建 React 原生应用来说，是非常重要的一部分。你可能会问，“什么是`AsyncStorage`？”这是一种在应用程序上全局存储数据的方式，该应用程序也可以脱机保存。如果您熟悉 web 开发，可以把它看作是会话的 React 本机版本。

数据通过未加密的键-值对存储在应用程序级别，允许开发人员在其中存储他们的应用程序工作所需的特定数据。最基本的例子是让用户在登录到应用程序后保持登录状态。您可以将他们的`userId`存储在`AsyncStorage`中，这将允许您在整个应用程序中使用该 ID。

在本文中，我将介绍如何使用它的示例，以便您可以在应用程序中利用它。

# 1.安装异步存储

要在您的应用程序中使用`AsyncStorage`，您首先需要安装它:

```
npm install @react-native-async-storage/async-storage
```

如果您使用的是 yarn，那么命令是:

```
yarn add @react-native-async-storage/async-storage
```

最后但同样重要的是，如果您使用 Expo CLI:

```
expo install @react-native-async-storage/async-storage
```

# 2.插入异步存储

在我们用`AsyncStorage`做任何事情之前，我们需要插入它。下面是对`AsyncStorage`进行简单插入的语法:

添加到异步存储

上面的代码用于将一个键-值对插入到存储中。你不需要把它放在一个单独的函数里。相反，您可以直接调用:

```
await AsyncStorage.setItem(key, value);
```

请记住，键和值对都必须是字符串！所以，如果你打算像上面的例子那样存储一个`userId`，一定要把它转换成一个字符串。或者，如果您计划存储一个布尔值，则该值必须是`"true"`或`"false"`。

如上所述，一个非常常见的用例是在用户登录时存储他们的 ID。这可用于为用户提取特定信息(例如显示用户名)。只要密钥存在，ID 就会一直存在。只有当密钥从存储器中删除时(或当应用程序被删除时)，它才会从`AsyncStorage`中消失。删除它的常见情况是在注销时。

这方面的另一个用例是，如果您希望能够在用户第一次登录后保持登录状态。您可以向`AsyncStorage`添加某种认证令牌，以便在整个应用程序中使用。您将拥有仅基于该身份验证令牌的条件呈现。

# 3.从异步存储中读取

所以现在我们已经插入到`AsyncStorage`中，让我们从存储器中读取。

从异步存储中读取

从存储器中读取可能有点棘手，因为它是一个`Promise`。这意味着当您计划调用这个函数时，您将需要确保适当地处理它，因为您得到的值可能没有及时准备好。下面是一个呼叫的示例:

使用 _getUserId()函数。

调用这个将处理`Promise`并为您获取`userId`。请记住，`userId`可能不会完全出现在您的代码中，这取决于您如何调用它。这里应该使用正确的`Promise`处理。关于`Promises`的更多信息可以在文档中阅读[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

# 4.从异步存储中删除

从`AsyncStorage`中删除实际上相当简单。有一些方法允许您从存储中删除。

如果您只想从存储器中删除一个密钥，可以这样做:

从异步存储中删除单个项目

记住使用字符串作为键！

如果我们希望清除整个`AsyncStorage`(例如在应用程序注销时)，那么可以这样写:

清除整个异步存储

与本文提供的任何其他示例一样，实际上您可以直接调用它，而不是创建额外的函数。

# 结论

对于创建任何类型的应用程序来说,`AsyncStorage`是一个非常强大的——也可以说是必需的——组件。能够在设备上全局存储数据对于向用户提供功能至关重要。如果没有这一点，很难构建复杂的应用程序。

如果您有任何问题，请随时留下评论，以便我们进行讨论。

下一集再见！