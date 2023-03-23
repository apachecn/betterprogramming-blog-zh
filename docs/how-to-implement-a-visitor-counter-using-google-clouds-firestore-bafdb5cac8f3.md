# 如何使用谷歌云的 Firestore 实现访客计数器

> 原文：<https://betterprogramming.pub/how-to-implement-a-visitor-counter-using-google-clouds-firestore-bafdb5cac8f3>

## 使用字段值安全地增加值

![](img/f1e0a4d2b8c3f3fb711f1a6348a3d181.png)

马特·赫尔比格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

在本教程中，我将演示如何实现一个访问者计数器，它在每次页面刷新时递增。该计数器将使用一个[云 Firestore](https://cloud.google.com/firestore/) 数据库和一个基于 [Express](https://expressjs.com) 的 web 应用程序来实现。

Cloud Firestore 是一个托管的 NoSQL 文档数据库服务，可从[谷歌云平台](https://cloud.google.com)获得。该服务有一个免费层，允许每天存储高达 1gb 的数据和数万次文档读写。API 和 web 控制台都非常容易使用。

自动增加一个值是一个简单的概念，但是如果你使用一个并发处理多个请求的 web 框架，或者如果你的应用程序被部署到多个实例，这就很容易出错。对于传统的 SQL 数据库，您需要确保在单个事务中读取、递增、然后写入值，以避免可能的数据丢失。

有了像 Firestore 这样的 NoSQL 数据库，您还有另外一个选择:[字段值](https://googleapis.dev/nodejs/firestore/latest/FieldValue.html)。这些是用`set()`、`create()`或`update()`编写文档时可以使用的特殊标记值。我们可以使用`[increment(n)](https://googleapis.dev/nodejs/firestore/latest/FieldValue.html#.increment)`字段值在数据库中增加一个值，而不必先读取它。

这里描述的方法应该适用于任何基于 Express 的 web 应用程序，但是如果你想要一个简单的方法来跟进，请前往 [Glitch](https://glitch.com/edit/#!/remix/hello-express) 来重新混合`[hello-express](https://glitch.com/~hello-express)`项目。

我们首先将`@google-cloud/firestore`模块作为依赖项添加到项目中。如果您使用 Glitch，在项目编辑器中选择`package.json`文件并点击“Add Package”按钮，搜索该模块，并选择它以将其添加到项目中。

接下来，我们创建一个 Firestore 数据库，并确保数据库服务帐户凭证被设置为环境变量`FIREBASE_SERVICE_ACCOUNT`的值。查看[本教程](https://medium.com/better-programming/how-to-use-google-cloud-firestore-with-glitch-e92464f00ee7)获取逐步说明。

然后我们将下面的代码添加到`server.js`:

这段代码用从`FIREBASE_SERVICE_ACCOUNT`环境变量中读取的凭证初始化 Firestore 数据库对象。然后代码用一个空对象初始化一个文档，如果它不存在的话。然后，代码定义一个函数来增加计数并返回新值。该函数使用`increment(n)`字段值来更新该值，而不必先检索它。最后，代码为`/count`路由定义了一个处理程序，该处理程序调用函数并返回增加的值。

接下来，我们将以下标记添加到`index.html`:

这定义了一个跨度，我们可以通过编程来设置它的值。

最后，我们将以下代码添加到`script.js`:

这段代码在页面加载时从`/count` route 获取值，然后将其设置为`visitorCount` span 的值。

查看访客柜台的[这个真实例子](https://frequent-languid-catboat.glitch.me/)。每次重新加载页面时，数字都会增加。