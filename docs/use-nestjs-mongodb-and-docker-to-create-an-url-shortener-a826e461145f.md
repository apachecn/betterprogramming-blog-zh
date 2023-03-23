# 使用 Nestjs、MongoDB 和 Docker 构建一个简单的 URL Shortener API

> 原文：<https://betterprogramming.pub/use-nestjs-mongodb-and-docker-to-create-an-url-shortener-a826e461145f>

## 使用 NestJS、Docker、MongoDB 实现一个简单的 URL shortener，并使用 Traefik 将其部署到启用 SSL 的生产环境中。还包括 Docker Swarm 设置。

![](img/edba01af5a638a1e2f545cab24dc6ce0.png)

[free pik 上的 Vectorarte](https://www.freepik.com/free-vector/man-woman-with-chains-background_1086772.htm#query=chain&from_query=small%20chain&position=30&from_view=search) 图像

本教程将涵盖用 NestJs 和 MongoDB 构建一个简单的 URL shortener API 的所有工作。此外，我将展示如何使用 Docker 将其部署到(Docker 和 Docker Swarm)生产环境中。

源代码发布在 GitHub 上，可以免费使用:

[](https://github.com/paulknulst/paulsshortener) [## GitHub-paulknulst/paulsshortener

### 一个渐进式 Node.js 框架，用于构建高效且可伸缩的服务器端应用程序。嵌套框架类型脚本…

github.com](https://github.com/paulknulst/paulsshortener) 

# 创建 NestJS 项目

首先，创建一个 NestJS 项目，作为 URL 缩短器的基线。为此，您需要在您的系统上安装 [Nest-CLI](https://github.com/nestjs/nest-cli) ，这可以通过以下方式完成:

```
$ npm install -g [@nestjs/cli](http://twitter.com/nestjs/cli)
```

然后切换到项目文件夹，使用 Nest-CLI 通过执行以下命令创建一个新的 NestJS 项目:

```
$ nest new paulsshortener
```

在这个过程中，您将被询问您想要使用哪个包管理器。将使用本教程`npm`。

现在，您可以在"*观察模式*"中启动 NestJS 项目，通过执行以下命令，立即看到您将在本教程中做出的所有更改:

```
$ npm run start:dev
```

为了测试一切是否正确启动，点击 [http://localhost:3000](http://localhost:3000) ，这将在浏览器中显示“Hello World”。

在项目中打开 AppService ( `/src/app.service.ts`)并将`return 'Hello World!';`更改为`return 'This will be your URL shortener';`。重新加载 http://localhost:3000 后，将会看到更新后的响应。

# 设置 MongoDB 数据库

为了简单起见，本教程将使用 MongoDB。为了避免设置正确版本、替换旧安装等麻烦，您应该使用 Docker 来部署它。将这个最小合成文件保存到您的项目根目录中，并将其命名为`docker-compose.local.yml`:

如果你不熟悉 Docker Compose 并且不想用它来建立一个 MongoDB，你可以看看官方的 MongoDB 文档[来学习如何在你的机器上安装它。请记住，在本教程的后面部分，Docker 还用于部署这个 URL shortener！](https://www.mongodb.com/docs/manual/installation/)

现在，要部署 MongoDB，在终端中切换到您的项目根目录并执行:

```
$ docker-compose -f docker-compose.local.yml up -d
```

之后，您已经成功地设置了 MongoDB 和您的机器，并可以开始使用它了。

# 将您的 NestJS 项目连接到 MongoDB

要将项目连接到 MongoDB，您应该使用 Mongoose，它是最流行的 MongoDB 对象建模工具。首先将所需的依赖项安装到项目中:

```
$ npm i @nestjs/mongoose mongoose
```

一旦安装了依赖项，就可以通过编辑如下所示的`AppModule` ( `/src/app.module.ts`)将 Mongoose 模块导入到项目中:

现在将自动建立连接，您可以创建将用于处理数据库的模式。

现在使用 Nest-CLI 在您的 NestJS 项目中创建一个新模块，它将处理与 URL 相关的所有事情:

```
$ nest g res url
```

这个例程会问两个你应该回答的问题。

*   你使用什么传输层？ **- > REST API**
*   您想生成 CRUD 入口点吗？ **- >否**

切换到新生成的`url`文件夹，创建新的文件夹模式，并添加文件`url.schema.ts`。然后创建一个类(`Url`)向文件中添加两个属性(`url`，`shortenedUrl`)。另外，添加文档和模式的导出。您的文件应该如下所示:

打开`url.module.ts`，添加两个新的导入，并修改`@Module`注释:

# 实现 URL 链接缩短功能

要缩小 URL，您将使用 CRC32 哈希算法，该算法必须添加到项目中才能使用:

```
$ npm install crc-32 --save
```

打开`UrlService` ( `/src/url/url.service.ts`)，用以下代码片段替换文件内容:

这个代码片段包含三个重要的函数:

*   `shrink`:使用 CRC32 算法将给定的 URL 转换成 8 个字符的字符串。然后只返回 8 个字符的字符串。
*   `create`:使用 shrink 函数创建一个 8 字符的字符串，并将一个新的`UrlSchema`文档保存到 MongoDB 中
*   `find`:从 MongoDB 中检索保存的 URL。

# 向 NestJS API 添加路线

要在客户机中使用 URL shortener，我们需要创建三个 REST 端点/函数。

*   `GET /shrink`:使用 Http Get 方法创建一个新的简短 URL。path 参数包含未缩写的 URL。这可以在任何浏览器中完成。
*   `POST /shrink` **:** 使用 Http Post 方法创建一个新的简短 URL。正文包含未缩写的 URL。你需要一个 API 或者邮差来使用这个。
*   `GET /s` **:** 以 8 个字符的字符串作为路径参数，返回不缩写的 URL。最后会自动转发到不短的网址。

当您使用 NestJS 时，您可以通过向 URL 模块中的`UrlController` ( `url.controller.ts`)添加三个新函数并使用`@Get`和`@Post`对其进行注释来轻松实现这些端点。打开`UrlController`，将内容替换为:

在这个代码片段中，创建了两个新的 GET 资源(`/shrink/`和`/s/`)和一个从`UrlService`调用先前创建的函数的 POST 资源。此外，类上方的注释从`@Controller('url')`更改为`@Controller('')`，以进一步缩小结果 URL。

# 测试 URL 缩写程序

要测试功能，您可以简单地使用您的浏览器，因为我们已经将这两个资源实现为 GET 调用。

请记住，当您使用 GET 提供 URL 作为路径参数时，您必须对 URL 进行编码！这意味着像这样的 URL:

```
https://www.paulsblog.dev/manage-time-more-efficiently-with-the-pomodoro-technique/
```

会变成这样:

```
https%3A%2F%2Fwww.paulsblog.dev%2Fmanage-time-more-efficiently-with-the-pomodoro-technique%2F
```

有了这些信息，您可以通过在我们的浏览器中打开以下 URL 来创建一个缩短的 URL:

```
[http://localhost:3000/shrink/https%3A%2F%2Fwww.paulsblog.dev%2Fmanage-time-more-efficiently-with-the-pomodoro-technique%2F](http://localhost:3000/shrink/https%3A%2F%2Fwww.knulst.de%2Fmanage-time-more-efficiently-with-the-pomodoro-technique%2F)
```

通过这个 GET 调用，您的 API 将返回 8 个字符的字符串: **-5f1a8349** (在您的项目中应该是相同的)

将这个字符串附加到 GET /s/ call 中，通过打开:[http://localhost:3000/s/-5f1a 8349](http://localhost:3000/s/-5f1a8349)来接收 URL 的非缩写版本

**浏览器中的结果将是之前提供的 URL 的简化版本。**

# 实现转发到未缩短的 URL

现在，您已经开发了一个可以在 CRC-32 算法的帮助下缩短 URL 的 API，您应该启用该功能来转发请求并自动打开它在数据库中找到的 URL。

使用 NestJS，这真的很容易，因为您只需要在 UrlController ( `src/url/url.controller.ts`)中调整 unshrink 函数。将先前创建的函数更改为以下实现:

# 用 Docker 部署 URL Shortener

让我们假设您想要在 Docker 环境中部署 URL shortener，并且必须开发一个可以用来完成这项工作的合成文件。

第一步是创建一个新的 Compose 文件(`docker-compose.prod.yml`)，并将之前创建的 MongoDB 文件中的内容复制到其中。然后添加一个名为 backend 的新服务，它将用于在 Docker 环境中安装、编译和运行 NestJS 项目。

由于您开发了一个定制软件，DockerHub 上不会有合适的映像，您必须从头创建一个。因为该项目基于在 NodeJS 环境中工作的 NestJS，所以您可以创建一个新的 Dockerfile 并使用最新版本的 node 作为基本映像。然后简单地复制源代码，安装，构建和运行项目。以下 Dockerfile 文件就足够了，应该在项目根目录下创建:

这个 docker 文件现在可以用作合成文件中后端服务的映像。

不幸的是，运行这个合成文件将不起作用，因为我们项目中链接的 URL 是硬编码的，不会自动调整。此外，AppModule ( `app.module.ts`)中的 DB 主机名和端口是静态的。

要解决这些问题，您必须在 AppModule ( `app.module.ts`)和 UrlService ( `url.service.ts`)中添加和更改一些东西。具体来说，添加三个新变量来保存数据库端口、数据库 URL 和结果服务的基本 URL。

切换到 AppModule ( `app.module.ts`)并将这两个变量添加到类定义之上:

此外，更改模块导入中的 Mongoose 部分，以正确使用这些变量:

然后打开 UrlService ( `url.service.ts`)并在类定义上方添加`baseurl`变量:

最后，更改 create 函数，使用新引入的基本变量返回完整的缩短 URL。

现在，您已经应用了这些更改，您应该通过添加可用的环境变量并根据您的需要调整它们来调整您的合成文件:

最后，在您的本地主机上部署它:

```
$ docker-compose up -d --build
```

您的 URL 缩写程序现已正确安装，可以在您的本地环境中使用。

# 使用 Traefik 部署到生产环境

现在您已经有了一个可以部署到任何地方的 Docker 服务，您可以使用它在使用 Traefik 的生产环境中部署它。为此，您将编辑合成文件，添加所有与 Traefik 相关的关键字(标签、网络)，并根据需要进行调整。

如果您不熟悉使用 Traefik 部署 Docker 服务，我可以推荐以下教程，其中涵盖了在单台服务器上的基本安装和使用 Docker Swarm 的服务器集群安装:

*   [**如何用自动加密证书解析器设置 Traefik v2**](https://levelup.gitconnected.com/how-to-setup-traefik-v2-with-automatic-lets-encrypt-certificate-resolver-83de0ed0f542)
*   [**使用 Docker 和 Traefik**](/deploy-any-ssl-secured-website-with-docker-and-traefik-27fbeb1343d3) 部署任何 SSL 安全网站
*   [**设置 Docker Swarm(用于 Traefik)**](https://levelup.gitconnected.com/docker-swarm-in-a-nutshell-ed2a9c42cd7c)
*   [**在 Docker 群上安装 Traefik**](https://levelup.gitconnected.com/the-most-important-services-everyone-should-deploy-in-a-docker-swarm-8e120b5a66#529f)

要使用 Traefik 部署您的 URL shortener，请调整标签部分。以下部分将展示并解释如何在单个服务器设置中实现这一点，此外还将提供 Docker Swarm 配置供下载:

在使用 Compose 成功部署这个服务之前，您必须调整主机:使用您自己的`BASE_URL`并更新`traefik`配置(对我来说，它是 [at0m.de](https://at0m.de) )。更改这两个值后，使用以下命令部署它:

```
$ docker-compose -f docker-compose.prod.yml up -d
```

使用 Docker Swarm 你可以使用[这个组合文件](https://raw.githubusercontent.com/paulknulst/paulsshortener/master/docker-compose.prod-swarm.yml)。但是，您必须为 MongoDB 服务调整`BASE_URL`和放置约束。然后构建，将映像推送到我们的注册表，并将其部署到您的 Docker Swarm 上:

```
$ docker-compose -f docker-compose.prod-swarm.yml build
$ docker-compose -f docker-compose.prod-swarm.yml push
$ docker stack deploy -c docker-compose.prod-swarm.yml paulsshortener
```

# 实时版本的附加调整

因为你在你的服务器上公开部署了它，你应该按照下面的方法添加速率限制:[https://docs.nestjs.com/security/rate-limiting](https://docs.nestjs.com/security/rate-limiting)。

# TL；博士:

在项目中安装所需的包:

```
$ npm i --save @nestjs/throttler
```

在 AppModule ( `app.module.ts`)中扩展 imports-array:

然后，将`ThrottlerGuard`添加到 providers 数组中:

将`SkipThrottle`注释添加到`UrlController` ( `url.controller.ts`)内的 Get /s/ endpoint，以忽略对该特定调用的速率限制:

# 结束语

我希望你喜欢阅读我的教程，并且现在能够在 Docker 容器中创建、构建和部署你的 URL shortener 网站。

请记住，这是一个非常基本的例子，没有任何错误处理，也没有 URL 检查。但是，本教程应该是开发您的版本的起点。

如果你喜欢读这篇文章，考虑在评论区发表你的宝贵意见。我很想听到你对这个网址缩写的反馈。此外，如果您对实现自己的版本有任何疑问，请在下面记下。如果可能的话，我试着回答他们。此外，与您的朋友和同事分享这篇文章，向他们展示如何使用 NestJs、MongoDB 和 Docker 来创建自己的 URL 缩短器。

```
Feel free to connect with me on [my personal Blog](https://www.knulst.de), [LinkedIn](https://www.linkedin.com/in/paulknulst/), and [Twitter](https://twitter.com/paulknulst).
```

*本文最初发表于我的博客*[*https://www . paulsblog . dev/use-nestjs-MongoDB-and-docker-to-create-an-URL-short ener/*](https://www.paulsblog.dev/use-nestjs-mongodb-and-docker-to-create-an-url-shortener/)