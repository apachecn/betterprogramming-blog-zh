# 如何用 Ruby on Rails 快速认证用户

> 原文：<https://betterprogramming.pub/how-to-quickly-authenticate-users-with-ruby-on-rails-c55471308641>

## 使用基本身份验证

![](img/2ccaf7ca0b5810cc41edff1d5e95656f.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在某些情况下，您可能需要对某些页面进行授权，以便未经授权的客户端无法访问它们。在 Ruby on Rails 应用程序中可以使用许多授权方法。

在本文中，我将提到一个非常简单(但不太著名)的方法，它已经实现，并且可以在每次创建新的 Rails 应用程序时使用。

`ActionController::HttpAuthentication::Basic`有一个叫做`http_basic_authenticate_with`的方法，你通常需要在你的控制器的顶部调用它。

[Rails](https://github.com/rails/rails) 是一个开源框架，所以我们很庆幸能够看到[的实现](http://api.rubyonrails.org/classes/ActionController/HttpAuthentication/Basic/ControllerMethods/ClassMethods.html)以及通过这种方法完成的工作:

让我们举一个`DocumentsController`的例子。我有两个呈现视图的方法。我想向任何人显示文档列表，不管他们是否已经通过验证。

我想将修改文档的权限仅限于经过授权的用户。也就是说，他们知道我一开始设置的可以使用的名字和密码。

我当然应该使用在配置文件中设置的参数化值，但是为了这个例子的目的，我在调用这个方法的时候使用实际值。

是的，我知道这并不太难找到，但它只用于这篇文章，也许还有一些祖母。

这一行代码调用了保护您的文档不被您不想要的人修改的方法。这真的很棒，而且省时，所以你不必自己写所有的东西。

如果您需要对许多控制器进行这种类型的授权，那么您可以简单地在`ApplicationController`声明它:

这就是为您的应用程序使用这种非常易于使用的授权方式所需要做的全部工作。

[在这里，您可以查看这个 Rails 模块中包含的其他方法](http://api.rubyonrails.org/classes/ActionController/HttpAuthentication/Basic.html)。

编码快乐！