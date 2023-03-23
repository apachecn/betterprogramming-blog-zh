# 在 Rails 7 中设计授权设置

> 原文：<https://betterprogramming.pub/devise-auth-setup-in-rails-7-44240aaed4be>

## 将 Devise Auth 与 Rails 7 集成有一些未记录的变化。在这里了解它们，或者使用附带的模板绕过它们

![](img/47f880c5bc0f1ccea2050ca1a628fbe2.png)

由 [foxfox](https://unsplash.com/photos/f6j5Dol1H_I) 通过 [Unsplash](https://unsplash.com/s/photos/rails) 拍摄

[Rails 7](https://rubyonrails.org/2021/12/15/Rails-7-fulfilling-a-vision) 已经发布，但令我惊讶的是，让它与 device 一起工作有一些曲折，我无法找到解释，除非在几个小时的过程中拼凑出关于[device GitHub](https://github.com/heartcombo/devise)和 StackOverflow 的问题。为了节省你一点时间，下面是怎么做的。

简而言之，我们在本文结尾的目标是让 Rails 7 安装上 Devise 的默认模块和自动生成的视图。本文的[配套代码](https://github.com/ManickYoj/rails7-devise-demo)可以在 GitHub 上完整查看。此外，如果您正在启动一个新的应用程序，您可以使用配置为模板的 [repo](https://github.com/ManickYoj/rails7-devise-template) 作为您自己应用程序的起点，而无需进一步阅读任何内容！

# Rails 7 和 Devise 的一般设置

首先，我假设您已经有了一个 rails 7 应用程序，并且正在使用的任何数据库都已经设置好了。如果没有，只需创建一个 rails 应用程序，如 [Rails 入门指南](https://guides.rubyonrails.org/getting_started.html)的步骤 3.2 所述。假设你已经安装了依赖项(ruby，rails ),你需要做的就是在控制台中运行下面的程序来创建一个名为`app_name`的新应用。

```
rails new app_name
cd app_name
```

要开始我们自己的旅程，首先添加 Devise 作为应用程序的依赖项。您可以手动将它添加到您的 gem 文件中，或者通过在控制台中运行以下命令自动获取最新版本

```
bundle add devise
```

您的 gem 文件现在应该包括如下内容

```
# Gemfile
gem "devise", "~> 4.8"
```

现在通过控制台安装它。

```
bundle install
```

然后，就像在 device 自述文件中一样，我们生成 device 配置。

```
rails g devise:install
```

# 围绕 Rails 7 的 Turbo 工作

现在，我们需要为 Rails 7 做一些特殊的事情。Rails 7 包括 [Turbo](https://github.com/hotwired/turbo-rails) 作为基石组件。Turbo 允许您运行异步页面更新，而无需编写任何 Javascript(这很棒),但它是通过劫持提交表单和跟随链接的正常流程来实现的。Devise(还)没有为此做好准备，默认情况下，它将无法显示它非常依赖的 flash 消息。我们需要修改 Devise 生成的代码来对付 Turbo。

因此，一旦你运行了`rails generate devise:install`,我们需要在除了 Devise README 指导我们做的以外的几个地方修改 Devise initializer 配置，并添加一个控制器作为 device 的父控制器。值得表扬的是:这些变化来自主题为的 [Go Rails 视频，该视频也解释了为什么这些变化是必要的。](https://gorails.com/episodes/devise-hotwire-turbo)

# 回到设计

按照正常的设备设置，我们还需要调整行动邮件配置，以便我们可以在开发中发送密码恢复电子邮件。

```
# config/environments/development.rb, near the other action_mailer config. ~line 40 in an unaltered config file.config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

此外，在正常的 device 设置中，我们需要在布局中包含显示警告和通知 flash 通知的功能，因为 device 广泛使用它们来让我们知道注册、进入和退出尝试的结果。

```
# app/views/layouts/application.html.erb, above the <%= yield %><% **if** notice %>
  **<p** class="alert alert-success"**>**<%= notice %>**</p>**
<% **end** %>
<% **if** alert %>
  **<p** class="alert alert-danger"**>**<%= alert %>**</p>**
<% **end** %>
```

配置完成后，我们就可以生成可认证的模型了。我们将使用标准名称`user`,但是——正如在 devise README 中很好地记录的那样，您可以随意命名它(尽管后面提到的一些方法和文件名将会改变)。在控制台中，运行

```
rails g devise user
```

对生成的`db/migrate/<timestamp>_devise_create_users.rb`文件进行必要的修改，添加您自己的列或支持额外设计模块的列。在这里，我认为没有必要修改。然后，通过控制台运行您的迁移

```
rake db:migrate
```

你现在可以运行你的应用程序，它应该包括像`/users/sign_up`和`/users/sign_in`这样的页面。理论上，device 现在应该可以工作了，但是不幸的是，device 使用的视图链接和重定向在 Rails 7 中不能工作。让我们解决这个问题。

如果您在浏览器中导航到`[/users/edit](http://localhost:3000/users/edit)`(如果包含协议和域，则导航到`[http://localhost:3000/users/edit](http://localhost:3000/users/edit)`)，并尝试使用取消帐户链接，将会取消帐户，但在重定向时会显示错误。错误会说类似`undefined method `users_url’ for … RegistrationsController`的话。它告诉我们，在删除用户后，Devise 试图将我们重定向到尚不存在的`user#index`路线。让我们将该路径路由回登录页面。

```
# config/routes.rbRails.application.routes.draw do
  devise_scope :user do
    # Redirests signing out users back to sign-in
    get "users", to: "devise/sessions#new"
  enddevise_for :users
end
```

> 注意:我试图找出如何改变 Devise 的删除重定向到的 url，但到目前为止，我在网上找到的方法似乎都不适用于 Rails 7。如果你想通了，请在评论里告诉我！

应该可以了！我们的开箱即用设备设置现在可以与 Rails 7 一起工作。同样，如果你想参考这个设置的任何代码，或者使用模板批发一个新的应用程序，GitHub 上有[代码，你也可以使用它作为模板回购来启动你自己的 Rails 7 devise 项目。](https://github.com/ManickYoj/rails7-devise-template)

# 最后说明:快速链接、设计和删除

如果您后来添加了一个带有`method: :delete`的注销链接(或任何其他链接)，该链接将不起作用！Turbo 将再次劫持请求，并将其转换为`GET`请求，从而到达错误的控制器端点。您可以指定链接不应该使用 turbo ( `data: {turbo: false}`)。这是可行的，但是它会重复发送删除请求。我怀疑这与链接不应该改变应用程序状态的规则有关。改变应用程序的状态。我怀疑这就是 Turbo 不支持这种行为的原因。

更容易和更好的替代方法是使用一个按钮来发送删除请求，这样就可以了。

```
<%= button_to(
  "Log Out",
  destroy_user_session_path,
  method: :delete
) %>
```

编码快乐！

PS。你愿意和我一起在网上卖宠物药品吗？[考拉健康](https://www.koala.health/)正在[招聘](https://jobs.lever.co/koalahealth)。这里有一整篇文章是关于你为什么应该和我们一起工作的。

## **资源**

*   [Rails 7 中带有 Devise 的预配置模板，这也是本文的参考代码](https://github.com/ManickYoj/rails7-devise-template)