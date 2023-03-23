# 想从你的 Rails 应用程序进行 API 调用吗？试试这三个 HTTP 客户端

> 原文：<https://betterprogramming.pub/making-api-calls-in-your-rails-app-try-these-http-clients-47b90ebca70>

## 法拉第、Excon 和 Rest 客户

![](img/aa3503adcf4deca1727a6e517e0e95ce.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果您正在开发一个利用 API 的应用程序，那么您将不得不选择一个 HTTP 客户端来发出请求。Ruby 有自己的名为`Net::HTTP`的内置 HTTP 客户端，但是使用起来很痛苦。

或者，有一些库可以使 API 请求变得更加容易。这些库的数量之多会让找到合适的库变得非常困难，所以这里有一些小技巧来帮助你找到适合你项目的库。

# 法拉第

虽然 Faraday 本身并不是一个真正的 HTTP 客户端，但它充当了其他客户端的包装器，比如 Ruby 的内置`Net::HTTP`。它作为一个抽象层来定制和简化请求。这种多功能性是法拉第成为最受欢迎的选择的原因。不想使用`Net::HTTP`作为 HTTP 客户端？没问题，只是从许多其他支持的客户端中选择一个。只要确保你为它安装了相应的宝石。

这个 gem 的其他特性包括一个连接对象，它可以用来创建默认的请求头/参数和通用的 URL 基路径。这个系统被称为`Faraday::Connection`，是进行 API 调用的推荐方式。Faraday 还允许使用中间件。它包括它所描述的“机架启发的中间件堆栈”,但也支持大量的外部 gem 来进一步定制请求。这些都是在`Faraday::Connection`内部实现的，以设定一些选项。然后可以轻松地提出请求。

法拉第的多功能性和大量的选项使其成为受欢迎的选择。支持的 HTTP 客户端和中间件列表可以在[这里](https://github.com/lostisland/awesome-faraday/)找到。

# Rest 客户端

这个 HTTP 客户端在 [RubyGems](https://rubygems.org/) 上有超过 2 亿次下载。虽然它没有法拉第那么多的功能或定制选项，但它的简单和直接的方法使它成为一个受欢迎的选择。如果您不想选择适配器或者需要所有复杂的中间件和额外的特性，而只想进行简单的 HTTP 请求，那么 Rest Client 是一个明智的选择。

它又快又简单，而且很有效。Rest 客户端需要注意的一点是，如果头没有作为参数在`RestClient.get(url, headers = {})`中提供，默认情况下请求体将作为字符串返回。Rest Client 确实有一些可以实现的更高级的选项，但总的来说，它是一个没有装饰，没有大惊小怪的宝石，这正是人们喜欢它的地方。点击查看文档[。](https://www.rubydoc.info/gems/rest-client/2.1.0)

# 执委会

Excon 介于 Faraday 和 Rest 客户端之间，由于其较浅的学习曲线，同时也提供一些高级选项和中间件支持，因此也很受欢迎。

它不像 Rest 客户端那样简单，也不提供 Faraday 的大量功能和定制。基本要求轻而易举。

Excon 的另一个很好的特性是持久连接的能力。当需要频繁调用 API 时，可以打开与服务器的连接以提高性能。

注意，当`:persistent => true`用作参数时，连接将保持打开，并在每个请求中重用，直到有一个请求使用`:persistent => false`作为参数。该连接将用于该请求，并在请求完成后关闭。

Excon 既简单又快速，同时也比 Rest 客户端先进一些。如果您需要频繁进行 API 调用，但不需要 Faraday 的所有额外功能，这是一个很好的选择。查看[文档](https://github.com/excon/excon/blob/master/README.md)了解更多信息。

# 结论

在选择用于 Rails 应用程序的 HTTP 客户端时，有很多选项可供选择。这里介绍的三种方法很受欢迎，因为它们都根据您的需求提供了独特的优势。尝试每一种方法，并利用这些 API 来提升您的 Rails 应用程序。

*   [法拉第](https://lostisland.github.io/faraday/)
*   [Rest 客户端](https://github.com/rest-client/rest-client)
*   [执委会](https://github.com/excon/excon)