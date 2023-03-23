# 免费无限电子邮件注册-无服务器邮件列表

> 原文：<https://betterprogramming.pub/unlimited-email-signups-for-free-for-indie-hackers-403c20261880>

## 创建一个简单的无服务器功能，无需外部工具即可免费收集用户的电子邮件

![](img/42a5030f2feb5acebb36275f50f9178f.png)

这里免费快速阅读，无介质账号:[https://riccardogiorato . com/garden/2019/unlimited-newsletter/](https://riccardogiorato.com/garden/2019/unlimited-newsletter/)

# **我们要建造什么？**

我们将构建一个简单的 HTML 表单，具有无服务器功能，可以保存用户的所有电子邮件，没有任何硬性限制。

为您提供保存电子邮件可能性的网络服务通常仅限于 1，000 或 2，000 封免费电子邮件，我们将轻松解决这个问题。

![](img/c29498d3cfc767a8baeaab139b2e62db.png)

通过我们的结合，我们将每月免费获得多达 125，000 封新邮件，使用 [Netlify functions](http://netlify.com/products/functions/) 或类似的无服务器托管服务提供商。

保存用户的一封简单邮件不需要你购买任何昂贵的外部工具。

Netlify 也提供表格提交，但每个月只有 100 个基本提交。

# 技术堆栈

![](img/c59ac0f0145925a30f58aab03539b92e.png)

*   免费的 PostgreSQL 数据库服务，如来自 [ElephantSQL](https://www.elephantsql.com/) 或来自 [Heroku](https://elements.heroku.com/addons/heroku-postgresql) 的服务
*   表单的静态 HTML 文件托管: [Netlify](http://netlify.com/) 或任何其他类似 GitHub 页面的服务
*   无服务器功能托管: [Netlify](http://netlify.com/) 或 [Zeit](https://zeit.co/) 为我们简单的 NodeJS 功能保存所有电子邮件在数据库中

# 后端代码

我们将把电子邮件保存在数据库中——如果你需要一个简单的 PostgreSQL 数据库，你可以使用 [ElephantSQL](https://www.elephantsql.com/) 免费层。

您可以使用以下 SQL 代码构建一个简单的`email_signup`表:

您需要在某个地方托管这个节点简单的无服务器功能。我建议你使用[网络功能](http://netlify.com/products/functions/)。

对于代码，我们将只使用几个基本模块，使您能够在没有任何外部 API 的情况下检查电子邮件的有效性:

*   `disposable-email-domains`:这是一个公共模块，给你一个完整的临时邮件列表，防止用户使用一次性邮件
*   `pg`:向 PostgreSQL 数据库发送数据作为查询的官方模块
*   `http`和`url` : 节点模块，用于处理来自请求的 URL，并构建一个简单的 HTTP 服务器来接收带有用户电子邮件的 HTTP 请求

记住用数据库中的连接 URL 替换`connectionString`，在我们的例子中是来自 ElephantSQL。

我们还通过在`saveEmailDB`函数中使用带有绑定参数的预准备语句来防止 SQL 注入:

*   `const insertQuery = “INSERT INTO email_signup(email) VALUES($1)”;`

对于这个查询，由于由`VALUES`属性设置的界限，您的用户将不能在这个 SQL 命令中提交任何其他值。

我们还处理各种响应，例如，如果用户发送临时电子邮件或在@后面没有域名的电子邮件，就会出现 403 状态代码和错误消息。

# 前端代码

前端代码只是一个简单的 HTML 表单，但它会将电子邮件发送到我们的无服务器函数，并将从服务器发送的消息显示在结果 div 中。

好的一面是服务器处理一切，你不需要更新前端代码，只需要用不同的消息更新服务器。