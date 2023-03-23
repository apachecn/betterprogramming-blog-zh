# 如何在 Node.js 中处理 Docker 秘密

> 原文：<https://betterprogramming.pub/how-to-handle-docker-secrets-in-node-js-3aa04d5bf46e>

## 不要在生产环境中暴露敏感信息

![](img/54006b7bb5af5cdead321d38e4f00048.png)

[Floh Maier](https://unsplash.com/@flohmaier?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

在我的[上一篇文章](https://medium.com/better-programming/how-to-add-authentication-to-your-fastify-rest-api-using-auth0-cddc7eacc90)中，我告诉你*而不是*将敏感信息放入源代码控制中。我展示了如何使用一个`.env`文件来存储这些信息并将其从源代码控制中排除。

我还展示了如何使用`[dotenv](https://www.npmjs.com/package/dotenv)`在环境中加载这些设置，以便 Node.js 应用程序可以读取它们。

虽然使用环境变量是本地开发的正确方法，但不建议在生产环境中使用。

命令行中的一个简单的`printenv`会列出所有的环境变量。在生产中管理您的敏感设置的一个更安全的方法是使用 [Docker secrets](https://docs.docker.com/engine/swarm/secrets/) 。

# 码头工人的秘密

Docker 秘密有助于管理容器在运行时需要的敏感数据。例如，用户名、密码和证书。单个秘密的最大大小是 500KB。

当你的容器在集群中运行时，比如 Docker Swarm，你可以使用 Docker secrets。从 Docker 版本 17.06 开始，Docker 支持所有类型的容器上的 Docker 秘密。

## 创建 Docker 机密

我更喜欢使用命令行创建 Docker 秘密。

有两个选择:使用`echo`或使用包含您的秘密的文件。下面的命令创建了一个名为`DB_PASSWORD`的 Docker secret，它使用`echo`保存字符串`“secretpassword”`。

```
echo "secretpassword" | docker secret create DB_PASSWORD -
```

另一种方法是使用包含秘密值的文本文件。

```
docker secret create DB_PASSWORD db_password.txt
```

这两个命令具有相同的结果。创建一个 Docker 秘密`DB_PASSWORD`,其中包含秘密和密码。与任何 Docker 资源一样，您可以使用`inspect`命令来获取秘密的细节。

```
Docker secret inspect [secretid or name]
```

如果您成功地创建了这个秘密，它将返回一个 JSON 对象及其详细信息。细节不包括价值。

执行 docker secret inspect 时 Docker Secret 的详细信息

## 在 Node.js 中读取和使用 Docker 机密

Docker 使用内存文件系统来存储机密。Docker 秘密看起来像你的容器中的常规文件。

Docker 将每个秘密作为一个文件存储在`/run/secrets/`中。文件名是机密的名称。当 Node.js 应用程序在容器中运行时，它可以像读取常规文件一样读取秘密。

我开发了[一个 Node.js 模块](https://github.com/PatrickKalkman/MiniVideoEncoder)，它从`/run/secrets`读取一个文件并返回文件的内容。

secrets.js，一个读取 Docker 秘密的 Node.js 模块

对于本地开发，我想使用前面描述的`.env`文件。

但一旦应用程序在生产中运行，它应该从 Docker secrets 中读取设置。通过将`secrets.js`与我的标准配置对象相结合，我获得了两方面的优势。

组合了 Docker 秘密和环境变量的配置对象

例如，在第 19 行，我把阅读秘密和环境设定`secrets.read(‘STORAGE_HOST’) || process.env.STORAGE_HOST`结合在一起。秘密比环境更重要。

代替开发你自己的，有现存的 npm 库帮助阅读 Docker 秘密。

比如 [docker-swarm-secrets](https://www.npmjs.com/package/docker-swarm-secrets) 、 [docker-secret](https://www.npmjs.com/package/docker-secret) 、[@ cloud reach/docker-secret](https://www.npmjs.com/package/@cloudreach/docker-secrets)。这些模块读取所有 Docker 秘密，并通过一个 JavaScript 对象公开它们。

## 将 Docker 机密分配给服务

在容器可以访问秘密之前，我们必须给予容器明确的许可。有两个选项可以赋予权限，在创建服务时添加或者添加到您的`docker-compose.yml`文件中。

```
docker service create --name myservice --secret STORAGE_HOST myimage 
```

服务`myservice`可以使用命令行访问主机上定义的秘密`STORAGE_HOST`。

另一种不同的方式也是我喜欢的方式是使用合成文件，如下所示。

合成文件在单独块中命名每个秘密，参见第 13 行。这些秘密让`external: true`定义这些秘密已经存在，并且是使用命令行在外部创建的。

定义和使用 docker 秘密的 docker-compose.yml

# 官方码头工人形象中的码头工人秘密

如果你想知道官方 Docker 图片是如何处理 Docker 秘密的，似乎有一个典型的模式。大多数使用环境设置的官方图像也包含带有`_FILE`后缀的相同环境设置。

例如， [MongoDB](https://www.mongodb.com/) 图像使用了`MONGO_INITDB_ROOT_USERNAME`和`MONGO_INITDB_ROOT_PASSWORD`环境变量。

它还接受`MONGO_INITDB_ROOT_USERNAME_FILE`和`MONGO_INITDB_ROOT_PASSWORD_FILE`环境变量。如果您将后者设置为`/run/secrets/[secret name]`，图像将读取并使用该秘密。

如果我们看看官方 [Postgres](https://hub.docker.com/_/postgres) 图片的[文档](https://hub.docker.com/_/postgres)，它提到了以下内容:

> 作为通过环境变量传递敏感信息的替代方案，`_FILE`可被附加到一些先前列出的环境变量，使得初始化脚本从容器中存在的文件加载那些变量的值。
> 
> 特别是，这可以用来从存储在`/run/secrets/<secret_name>`文件中的 Docker 秘密中加载密码。"

所以，官方的 Postgres 图像也使用了同样的`_FILE`图案。

# 在 Node.js 中使用 _FILE 模式

虽然早期的读取秘密的模块工作良好，但最好使用与官方图像相同的模式。我们只需要对 secrets 模块和 config 对象做一点小小的修改。

通过指定完整路径读取 Docker 机密

我们接收完整的路径，而不是接受秘密的名称和预先考虑秘密的路径。`Config`对象首先读取`_FILE`设置的值，如果不可用，则使用环境设置。

配置对象，它使用与官方 Docker 图像相同的模式

例如，第 27 行上的`domain: secrets.read(‘AUTH_DOMAIN_FILE’) || process.env.AUTH_DOMAIN`首先尝试读取秘密文件，如果不成功，则使用`AUTH_DOMAIN`环境设置。

# 开始使用 Docker 机密

我希望我已经说服您开始使用 Docker 秘密来存储生产中的敏感信息。感谢您的阅读，如果您有任何问题或意见，请随时回复。