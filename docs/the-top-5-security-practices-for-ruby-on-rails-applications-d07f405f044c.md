# Ruby on Rails 应用程序的 5 大安全实践

> 原文：<https://betterprogramming.pub/the-top-5-security-practices-for-ruby-on-rails-applications-d07f405f044c>

## 您的 Ruby on Rails 安全备忘单

![](img/e7d5193078062c7d2e3d2b28a8808559.png)

照片由[迈克·科诺诺夫](https://unsplash.com/@mikofilm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

老实说:当我第一次开始开发 [RoR](https://rubyonrails.org/) 应用程序的时候，我从来没有把安全性放在心上。Rails 是一种快速构建 MVP 原型的方法，具有所有的快捷方式和约定——如此之多，以至于我从来没有考虑过一旦应用程序实际上线后我需要采取的措施。虽然 Rails 不像 WordPress 那样漏洞百出，WordPress 因容易被黑客攻击而臭名昭著，但因为它经常被新手开发者使用，它可能会在应用程序中留下很多安全漏洞。

我想用这篇文章来解决我在整个开发生涯中看到的前五个问题，但这绝对不是一个完整的列表。我没有考虑常见的攻击，如 XSS 脚本和伪造的 SQL 注入。有整篇文章都是关于编写良好的代码来防止 SQL/js 注入的。我也不解决服务器安全问题。无论您使用的是 AWS、Digital Ocean、Linux 还是 Heroku，都必须遵循基本步骤——使用防火墙、备份数据库以及强制使用私有/公共密钥对进行登录。

今天，我将重点介绍应用程序级的步骤，您可以采取这些步骤来使应用程序为生产做好准备，为最终用户创建一个安全的环境，防止暴力攻击，并保证 API 密钥的安全。

让我们开始吧！

# 1.强制使用强密码

在 2020 年，开发团队不会从头开始编写 Rails 认证，这几乎是必然的。像`Devise`或`OmniAuth`这样的宝石是为网络创建安全用户的现代标准。然而，默认情况下，`Devise`用户可以用任意六个字符的密码创建一个账户。像“123456”或“password”这样的密码将通过`Devise`验证。很明显，这是不好的。

要使用户帐户更加安全，请在创建帐户时强制创建强密码。是的，这对用户来说可能更烦人，但是安全的 web 密码已经成为标准，所以它应该不会显著影响用户激活。使用[装置](https://github.com/heartcombo/devise/wiki/How-To:-Set-up-simple-password-complexity-requirements)可以直接实施密码长度和复杂性。

或者，这些宝石可以增强用户安全性:

*   【https://github.com/bdmac/strong_password 
*   [https://github.com/ValiMail/devise-secure_password](https://github.com/ValiMail/devise-secure_password)

# 2.保护用户数据

新的 Rails 开发人员养成了使用 ID 显示数据的习惯。当年轻的 RoR 开发人员第一次学习使用 CRUD 动作生成 RESTful 控制器时，这是他们学到的`ActiveRecord`方法的一部分。

例如，您可能有一个在应用程序中显示餐馆的 URL，类似于[http://localhost:3000/restaurants/1](http://localhost:3000/restaurants/1)2，以及类似于以下的控制器代码:

默认情况下，这段代码不执行任何授权检查。应用程序中的每个用户都可以通过更改 URL ID 来查看每条记录。为了恰当地管理角色和权限，Rails 拥有授权宝石，如[权威人士](https://github.com/varvet/pundit)或[坎坎坎](https://github.com/CanCanCommunity/cancancan)。然而，确保用户不能访问其他人的数据的最简单的方法是只对属于他们的对象执行查询(这也减少了查询的负载以加速应用程序)。

如果餐馆不属于用户，第二个查询将返回`nil`而不是返回记录。

![](img/eaed4f28edce8cda4d4ee7031d3f4639.png)

[Moja Msanii](https://unsplash.com/@mojamsanii?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

# 3.强制 SSL

对于存储用户数据或金融交易的应用程序来说，这是理所当然的。所有请求都应该通过 HTTPS 而不是 HTTP 安全地发送。

像 Heroku 这样的托管服务提供 SSL 证书作为域设置的一部分。其他服务，如自动气象站、数字海洋等。，[让我们加密](https://letsencrypt.org/)是设置免费 SSL 的绝佳选择。最后，确保在生产环境中强制执行 SSL 连接，并将任何不安全的请求重定向到 HTTPS。

将这一行添加到生产配置中，以强制在生产环境中通过 SSL 对应用程序进行所有访问:

```
#config/environments/production.rb config.force_ssl = true
```

# 4.用环境变量保护 API 键

加密凭证最初是在 Rails 5.1 中引入的。在此之前，可以选择使用像 Figaro 或 dotenv-rails 这样的宝石。Rails 6 通过引入特定于环境的凭证文件使这变得更加简单。

什么样的数据应该加密？连接到 Stripe、AWS 或 Google 等第三方服务的任何 API 密钥。经验法则是 git 不应该跟踪这些文件——即使是私有存储库——以避免意外共享。

要编辑凭证，请在终端中运行`credentials`命令。指定环境(开发、测试或生产):

```
rails credentials:edit --environment development
```

这将在`config/credentials/development.key`中用新的加密密钥创建一个`config/credentials/development.yml.enc`文件。

将密钥添加到 YAML 文件中:

```
stripe:
  publishable_key: XXXXXXXXX123
```

要访问应用程序中的值，请使用`Rails.application.credentials`:

```
Rails.application.credentials.stripe[:publishable_key]
=> XXXXXXXXX123
```

# 5.防止暴力攻击

对于大多数应用程序来说，注册垃圾邮件是一个严重的问题，当一个新推出的应用程序在一夜之间获得 100 个用户，但其中 98 个都是机器人时，会感到非常沮丧。`[rack-attack](https://github.com/kickstarter/rack-attack)` gem 是用于阻止和抑制滥用请求的机架中间件。它有助于减少堵塞数据库和限制应用程序带宽的垃圾邮件帐户的数量。

gem 提供了将 IP 地址列入白名单或黑名单、添加限速标题以及防止来自同一电子邮件地址的注册垃圾邮件的功能:

关于这一点，防止注册垃圾邮件的另一种方法是为`Devise`启用注册确认，它会发送一封电子邮件来确认用于注册帐户的电子邮件地址是有效的。

# 更多资源

关于这个主题的更多信息，请查看创建安全应用程序的 ThoughtBot 指南以及由 Bruno Facca 创建的令人难以置信的全面安全清单。

[](https://thoughtbot.com/blog/building_secure_web_applications_with_ruby_on_rails) [## 用 Ruby on Rails 构建安全的 web 应用程序

### 使用像 Ruby on Rails 这样的成熟框架来构建 web 应用程序的优势在于，它相当…

thoughtbot.com](https://thoughtbot.com/blog/building_secure_web_applications_with_ruby_on_rails) [](https://github.com/brunofacca/zen-rails-security-checklist) [## 布鲁诺法卡/Zen-rails-安全-清单

### 本文档提供了在开发应用程序时要实施的安全措施的不一定全面的列表

github.com](https://github.com/brunofacca/zen-rails-security-checklist) 

编码快乐！请告诉我接下来应该讨论哪个 Ruby on Rails 主题！