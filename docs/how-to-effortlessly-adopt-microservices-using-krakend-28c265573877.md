# 如何使用 KrakenD 轻松采用微服务

> 原文：<https://betterprogramming.pub/how-to-effortlessly-adopt-microservices-using-krakend-28c265573877>

## KrakenD API 网关简介

![](img/be061c6a5945406a4769de9e046333a2.png)

照片由[法比奥·巴拉西纳](https://unsplash.com/@fabiolog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

[KrakenD](https://www.krakend.io/) 是用 [Golang](https://golang.org/) 编写的现代开源 API 网关。目前，由于其无状态架构，它的性能是市场上最快的。这使得开发人员可以灵活地过滤和操作来自多个服务的响应。此外，它还附带了许多中间件，有助于保护传输、添加身份验证、限制连接等等。

考虑 KrakenD 的这个典型用例:您有一个包含许多服务的复杂系统，UI 消费者想要调用您的后端。他们需要做额外的实现来接收来自服务器的响应。即使他们只对特定的数据感兴趣，他们仍然必须等待整个响应，然后过滤数据。

这就是克拉肯德发挥作用的方式。它在您的系统和客户端之间增加了一层。由于有了这一层，消费者无需改变他们的实现就能收到他们想要的信息。

在本文中，我们将看看使用 KrakenD 的主要好处。

# 将服务响应合并成一个

您可以在将从后端接收的内容返回到客户端之前对其进行操作。让我们看一个简单的例子，它说明了 KrakenD 如何为您提供实现这一目标的灵活性，而无需任何编码。

## 准备后端数据

我将把两个服务的过滤响应合并成一个。我将使用 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) ，而不是自己编写服务。这是一个方便的用于测试和原型制作的伪 API。它有一组预定义的资源，可用于生成虚拟数据。

我选择了`[posts](https://jsonplaceholder.typicode.com/posts)`和`[comments](https://jsonplaceholder.typicode.com/comments)`服务。

在您的浏览器中打开此 URL，查看其中一个`posts`:

```
[https://jsonplaceholder.typicode.com/posts/](https://jsonplaceholder.typicode.com/comments/4)1
```

回应:

现在，让我们来看看其中的一个`comments`:

```
[https://jsonplaceholder.typicode.com/comments/](https://jsonplaceholder.typicode.com/comments/4)1
```

回应:

假设我们想要混合`posts`的`title`和`comments`的`body`。

## 为 KrakenD 准备配置文件

这个配置是在一个单独的配置文件`krakend.json`中完成的。您可以手动或通过方便的 [KrakenDesigner GUI](https://designer.krakend.io/#!/) 创建它。

我的`krakend.json`配置如下所示:

*   `host`是我们获取数据的地方(假 API)。
*   请注意，您可以通过将查询字符串参数插入到花括号中来将它们传递给后端。我的`id`参数在`querystring_params`选项中定义。
*   我使用`url_pattern`为不同的数据定义了两个`backend`服务，即`/posts/{id}`和`/comments{id}`。请注意，您可以配置更多后端。
*   我使用了一个单独的`endpoint`来返回来自后端的响应— `/mixed{id}`。
*   你可以提供一个`blacklist`或`whitelist`你想要返回给客户端的数据，这非常有用。在我们的例子中，我已经将`body`和`title`添加到了`whitelist`中，因为我对其余的响应字段不感兴趣。

## 测试原型

现在我们需要启动 KrakenD 来测试结果。

开始的最快方法是使用 Docker 映像。

在您的终端中运行以下 Docker 命令:

```
docker pull devopsfaith/krakend
```

然后运行这个命令，用我们的定制配置启动 KrakenD:

```
docker run -p 8080:8080 -v "${PWD}:/etc/krakend/" devopsfaith/krakend run -d -c /etc/krakend/krakend.json
```

注意，`krakend.json`文件应该在一个名为`/etc/krakend`的文件夹中。

在浏览器中打开此 URL:

```
[http://localhost:8080/mixed/1](http://localhost:8080/mixed/1)
```

您应该会看到这样的响应:

```
{"**body**":"laudantium enim quasi est quidem magnam voluptate ipsam eos\ntempora quo necessitatibus\ndolor quam autem quasi\nreiciendis et nam sapiente accusantium","**title**":"sunt aut facere repellat provident occaecati excepturi optio reprehenderit"}
```

如您所见，响应已经使用`mixed/{id}`端点进行了适当的合并。你可以比较一下我在文章前面粘贴的来自`posts`的`title`和来自`comments`的`body`。

*提示:如果修改了配置文件，可以通过执行 KrakenD* `*check*` *命令来确保语法正确:*

```
docker run -it -p 8080:8080 -v $PWD:/etc/krakend/ devopsfaith/krakend check --config krakend.json
```

# 其他功能和中间件

## 安全连接

您可以使用 KrakenD 的安全策略来限制对后端的访问。可以在服务(根)级别使用`extra_config`选项启用和配置安全性。

您可以通过主机限制连接，防止点击劫持、MIME 嗅探和跨站点脚本。

KrakenD 支持 HTTP 严格传输安全(HSTS)、HTTP 公钥锁定(HPKP)和 OAuth2。

参见安全[文档](https://www.krakend.io/docs/service-settings/security/)中的示例。

## 克拉肯游乐场

KrakenD 提供了一个现成的[游乐场](https://github.com/devopsfaith/krakend-playground)和一个演示网络应用程序。这是探索特性的快捷方式。你可以四处看看，看看像 KrakenDesigner、 [Jaeger](https://www.jaegertracing.io/) (用于度量和跟踪)、bloomfilter 客户端(用于 JWT 撤销)和 [OAuth2](https://oauth.net/2/) (用于认证)这样的工具。

# 结论

我们已经看到，KrakenD 提供了一种便捷的方法来解决诸如保护后端、使用单个端点公开许多服务等任务。所有这些都可以在一个配置文件中定义。

我希望你能从这篇文章中学到一些新的东西。感谢您的阅读，下次再见！