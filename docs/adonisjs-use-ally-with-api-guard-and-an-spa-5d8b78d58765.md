# 如何使用 AdonisJS 在单页面应用程序中集成社交认证

> 原文：<https://betterprogramming.pub/adonisjs-use-ally-with-api-guard-and-an-spa-5d8b78d58765>

## 将 Ally 与 API guard 和 SPA 一起使用

![](img/c61450b7b38d489eb13c9e6d9d38b681.png)

照片由 [Pathum Danthanarayana](https://unsplash.com/@pathum_danthanarayana?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[AdonisJS](https://adonisjs.com/) 是 Laravel 的 Node.js 版本。与其他框架不同，AdonisJS 的目标是使后端开发变得容易，而不会有开发人员遇到的麻烦。

AdonisJS，就像 Laravel 一样，是一个 MVC 框架(并且是 Typescript 友好的)，这意味着您将不会纠结于采用哪种设计模式。当然，您可以定制/添加名称空间。

核心团队和社区成员已经创建了许多包来为框架添加多个特性。[这是所有包的列表](https://packages.adonisjs.com/)。

其中一个名为 [Ally](https://docs.adonisjs.com/guides/auth/social) 的软件包提供了通过社交网络认证用户的可能性，并支持 Github、Google、Twitter、脸书、Discord、Spotify 和 LinkedIn。

在 MAP(多页应用程序)中使用 Ally 是没有痛苦的。然而，当涉及到 SPA(单页应用程序)时，事情就变得棘手了。在本文中，我们将了解如何在没有会话的情况下使用 SPA 进行社交认证。

我们不会在您的 SPA 上安装处理社交认证的包，而是将我们的 AdonisJS 应用程序用作提供 URL 和认证用户的单一真实来源。

我们将从创建一个新的 AdonisJS 项目开始。为此，请运行以下命令:

```
npm init adonis-ts-app@latest ally-spa
```

说到项目结构问题，选`api`。

现在，我们将建立盟友。

```
npm i @adonisjs/ally
```

安装后，我们需要配置软件包，我们只需运行一条命令即可完成:

```
node ace configure @adonisjs/ally
```

上述命令将:

*   创建合同文件`contracts/ally.ts`
*   创建一个配置文件`config/ally.ts`
*   更新`.env`和`.env.example`文件
*   用封装类型更新`tsconfig.json`文件
*   最后，用提供者列表中的包名更新`.adonisrc.json`文件

您还可以验证包驱动程序所需的环境变量。在我们的例子中，我们将使用 Google。您可以查看 [Ally 的指南](https://docs.adonisjs.com/guides/auth/social)以了解如何使用具有所需配置的其他驱动程序。

打开`env.ts`并添加以下代码:

现在，打开`config/ally.ts`文件，添加您的客户端 id 和客户端密码。要获得您的 ID 和密码，您需要在 [Google 开发者控制台](https://console.developers.google.com/)中设置一个 Google OAuth 2.0 应用程序，并使用 Google 帐户为 OAuth 客户端创建凭证。

一旦创建了应用程序，就必须创建凭证。在`Credentials`页面，点击`CREATE CREDENTIALS`按钮，选择 OAuth 客户端 ID。选择 Web 应用程序作为应用程序类型。为您的应用程序选择一个名称。然后，你需要为`Authorised JavaScript origins`添加一个 URI。肯定是你 SPA 的地盘，在我这里就是`http://localhost:9000`。最后，您必须为重定向添加另一个 URI。在`Authorised redirect URIs`中，添加一个类似于`http://localhost:9000/auth/google`的 URL(这取决于您的 SPA 域和端口，我使用 localhost 来测试一切是否正常)。

在`config/ally.ts`中，添加相同的重定向 URL:

使用上面提供的 Google 配置，我们的 AdonisJS 应用程序现在可以生成一个重定向 URL，该 URL 可以发送到我们的 SPA。

打开`start/routes.ts`并添加以下代码:

我们使用一个通用的路由(使用通用参数`:provider`)，这样我们就可以为每个配置的驱动程序获得一个重定向 URL。如果用户已经登录，我们将通过状态代码= `406`(不可接受)的 respondig 拒绝该请求。否则，我们会将重定向 URL 发送到我们的 SPA。

我们使用`stateless`方法，因为我们没有使用 CSRF 验证。

> 如果使用 Vue 作为前端框架，可以使用 axios 向上述路由发送请求，或者在 onMounted hook 的回调内部获取 API，获取 URL 并将其添加到一个按钮组件中。

现在我们有了重定向 URL，我们可以从 SPA 访问它。点击它，你会看到一个同意表格，你可以选择你的谷歌账户。选择您的帐户后，您将被重定向到提供的回调 URL(在我的例子中是[http://localhost:9000/auth/Google](http://localhost:9000/auth/google))。

谷歌提供商将追加一个查询字符串到我们的重定向网址。我们将使用这些附加数据向服务器发送请求，以便 Ally 可以对用户进行身份验证。

下面是回调路由处理程序的代码。

如您所见，我们使用 AdonisJS 的不透明访问令牌(OAT )(记住，这是一个 SPA，我们没有使用会话)来授权后续请求。提供商令牌(来自 Google、Twitter 等)用于在提供商中执行其他操作，例如:从 GitHub 获取所有用户的存储库。

在最后一部分，我们需要授权后续请求。我们将创建一个新的中间件，从 cookies 中获取不透明的访问令牌，并将其附加到每个请求头中。

```
node ace make:middleware SetAuthorizationHeader
```

打开`App/Middleware/SetAuthorizationHeader.ts`，添加以下内容:

最后，您需要在全局中间件数组中注册中间件，在`start/kernel.ts`内部。

现在，您可以[将认证中间件](https://docs.adonisjs.com/guides/auth/middleware)添加到路由中，它将检查令牌并在令牌过期时授予访问权限或拒绝访问。瞧，我们完成了！