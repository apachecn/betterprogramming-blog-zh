# Docker 提示:使用 Docker 配置

> 原文：<https://betterprogramming.pub/about-using-docker-config-e967d4a74b83>

![](img/3a74e02d95981e6482495b2f685c8840.png)

Alejandro Escamilla 在 [Unsplash](https://unsplash.com/search/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# TL；速度三角形定位法(dead reckoning)

使用一个 [Docker](http://docker.com) 配置而不是创建一个具有嵌入式配置的映像怎么样？

# 在图像中嵌入配置？

我们经常看到如下所示的 docker 文件，其中创建一个新映像只是为了向基本映像添加一个配置。

```
$ cat Dockerfile
FROM nginx:1.13.6
COPY nginx.conf /etc/nginx/nginx.conf
```

在这个例子中，本地`nginx.conf`配置文件被复制到 [NGINX](https://www.nginx.com/) 映像的文件系统中，以覆盖默认配置文件，即`/etc/nginx/nginx.conf`中提供的文件。

这种方法的一个主要缺点是，如果配置发生变化，就需要重新构建映像。

# Docker 配置进入画面

`Configs`从 17.06 号码头开始提供服务。在`secrets`存储敏感信息的地方，`config`允许在服务映像之外存储非敏感信息，比如配置文件。

至于其他 Docker 原语(容器、图像、卷)，`config`在 CLI 中有自己的命令集。

```
$ docker config --helpUsage: docker config COMMANDManage Docker configsOptions:Commands:
 create Create a configuration file from a file or STDIN as content
 inspect Display detailed information on one or more config files
 ls List configs
 rm Remove one or more configuration filesRun ‘docker config COMMAND — help’ for more information on a command.
```

# **创建配置**

回到前面的例子，我们将创建一个 Docker 配置文件，而不是在映像中复制 NGINX 配置文件。

在本例中，`nginx.conf`文件包含以下内容。

它基本上定义了一个 web 服务器，该服务器监听端口 8000，并将到达`/api`端点的所有 HTTP 请求转发给上游 API 服务器。

使用 Docker CLI，我们可以从这个配置文件中创建一个`config`，我们将这个配置命名为`proxy`。

```
$ docker config create proxy nginx.conf
mdcfnxud53ve6jgcgjkhflg0s
```

然后，我们可以像检查任何其他 Docker 原语一样检查配置:

```
$ docker config inspect proxy
[
  {
    "ID": "x06uaozphg9kbnf8g4az4mucn",
    "Version": {
      "Index": 2723
    },
    "CreatedAt": "2017–11–21T07:49:09.553666064Z",
    "UpdatedAt": "2017–11–21T07:49:09.553666064Z",
    "Spec": {
      "Name": "proxy,
      "Labels": {},
      "Data": "dXNlciB3d3ctZGF0YTsKd29y...ogIgICAgIH0KICAgIH0KfQo="
    }
  }
]
```

这些数据仅采用 Base64 编码，很容易解码。

根据定义，配置中的数据不是机密的，因此不会加密。

# 使用配置

既然我们已经创建了`proxy`配置，我们将看看如何在服务中使用它。为此，我们将考虑两种方法——使用命令行和使用 Docker 堆栈。

在这两种情况下，我们定义了两种服务:

*   **API** :一个简单的 HTTP 服务器监听端口 80，并在每次收到 Get 请求时发回一个城市访问建议。
*   一个**代理**使用上面创建的配置，将所有以`/api`端点为目标的流量发送到上游 API 服务。

我们将通过在代理的端口 8000 上的`/api` 端点上发送一个 HTTP Get 请求来测试这个设置，并确保我们得到一个来自 API 的响应。

## **使用命令行**

我们首先创建一个覆盖网络。我们将使用它，这样 API 和代理这两个服务就可以一起通信。

```
$ docker network create --driver overlay front
```

然后我们创建了`api`服务。

```
$ docker service create --name api --network front lucj/api
```

最后一步是创建`proxy`服务。

```
$ docker service create --name proxy \
  --name proxy \
  --network front \
  --config src=proxy,target=/etc/nginx/nginx.conf \
  --port 8000:8000 \
  nginx:1.13.6
```

一切就绪后，让我们在本地主机的端口 8000(代理服务在主机上发布的端口)上发送一个 HTTP 请求

```
$ curl localhost:8000/api
{“msg”:”c249837f1f58 suggests to visit Emosiba”}
```

请求到达代理服务，代理服务将请求转发给 API。换句话说，作为 Docker 配置提供的 NGINX 配置文件被正确地考虑在内。

## **使用 Docker 编写文件**

当然，使用 Compose 文件来定义应用程序并将其部署为 Docker 堆栈更加方便。然后我们用以下内容创建一个`stack.yml`文件。

注意:因为配置是在运行应用程序之前创建的，所以在这个文件中它被定义为`external`。

然后可以使用以下命令运行该应用程序:

```
$ docker stack deploy -c stack.yml test
Creating service test_proxy
Creating service test_api
```

让我们向代理服务的`/api`端点发送一个 HTTP Get 请求。

```
$ curl localhost:8000/api
{“msg”:”f462d568c0b0 suggests to visit Onitufdu”}
```

和以前一样，请求被转发给 API 服务。

# 服务更新

当需要修改配置的内容时，常见的模式是创建一个新的配置(使用`docker config create`)，然后更新服务订单以删除对以前配置的访问，并添加对新配置的访问。服务命令是`--config-rm`和`-- config-add.`

让我们从`nginx-v2.conf`文件创建一个新的配置。

```
$ docker config create proxy-v2 nginx-v2.conf
xtd1s1g6b5zukjhvup5vi4jzd
```

然后，我们可以用下面的命令更新服务。这样，我们删除名为`proxy`的配置，并添加名为`proxy-v2` **的配置。**

```
$ docker service update --config-rm proxy --config-add src=proxy-v2,target=/etc/nginx/nginx.conf proxy
```

注意:默认情况下，当配置附加到服务时，它在/config_name 文件中可用。然后我们需要使用`target`选项明确定义位置。

# 摘要

Config 是一个非常简洁的东西，它有助于将应用程序从配置中分离出来。你在应用程序中使用配置吗？