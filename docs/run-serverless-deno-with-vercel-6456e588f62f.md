# 用 Vercel 运行无服务器 Deno

> 原文：<https://betterprogramming.pub/run-serverless-deno-with-vercel-6456e588f62f>

## 通过自动 SSL 轻松部署

![](img/b7129d356135adb24d52da1ca762ecad.png)

照片由[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[Deno](https://deno.land) 是一个简单、现代、安全的 JavaScript 和类型脚本运行时，它使用 [V8](https://v8.dev/) 并内置于 [Rust](https://www.rust-lang.org/) 中。1.0 版本是[最近推出的](https://deno.land/v1)。

Vercel 是一家无服务器功能和静态网站的托管提供商。它支持带有自动 SSL 和 DNS 管理的零配置部署。它很容易使用，并且有一个慷慨的免费层。

Vercel 新的`[vercel-deno](https://github.com/TooTallNate/vercel-deno)`运行时使得部署使用 Deno 实现的无服务器功能变得非常容易。让我告诉你怎么做。

*   [安装 Deno](https://deno.land/#installation) 。
*   [安装 Vercel](https://vercel.com/download) 和[报名](https://vercel.com/signup)。
*   运行`vercel login`并遵循屏幕上的指示。
*   为您的项目创建一个新目录，并使其成为您终端的当前目录。
*   创造`api/hello.ts`:

该无服务器功能可从部署的`/api/hello`路线访问。该函数用包含友好消息和 Deno 版本号的明文正文来回复所有请求。

*   创造`public/index.html`:

该页面可从部署的`/`路线访问。该页面包含一个简单的脚本，它在页面加载时获取`/api/hello`路由，然后将`<span>`元素的内容设置为响应。

**注意**:此文件用于演示目的。虽然您不会在自己的项目中使用这个特殊的`index.html`文件，但是在部署到 Vercel 之前，您确实需要在`public`目录中包含一些内容，否则将会提供项目的根目录。

*   创造`vercel.json`:

这个文件配置 Vercel 使用`vercel-deno`运行时来构建它在`api`目录中找到的所有 JavaScript 和 TypeScript 文件。

**注**:在发布时`vercel-deno`的版本为 0.3.0。您可能想要查看运行时的[最新发布版本](https://github.com/TooTallNate/vercel-deno/releases)是什么，并使用它来代替。

*   创建`.vercelignore`:

这个文件将 Vercel 配置为只部署`api`和`public`目录以及`vercel.json`文件的内容。其他文件不会上传到 Vercel。

*   通过运行`vercel dev`在本地测试项目。

访问屏幕上打印的 URL 以验证一切正常。无服务器函数将在第一次访问其端点时按需编译。键入“Control-C”退出。

*   通过运行`vercel deploy`并接受所有缺省值来部署项目。
*   访问屏幕上显示的检查 URL，查看部署进度。
*   访问屏幕上打印的生产 URL 以查看部署的站点。

就是这样！您的无服务器 Deno 功能已部署到生产中。

您可以在这里看到这个项目的[示例。这里可以看到](https://volatile-rain.vercel.app/)[源代码](https://vercel.com/bacongravy/volatile-rain/14zigqw2a/source)。(请注意，我有意公开了这个部署；请放心，默认情况下，Vercel 不会公开您项目的日志和源代码。)