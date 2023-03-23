# 通过集成 MinIO 和 NestJS 上传和检索图像

> 原文：<https://betterprogramming.pub/upload-and-retrieve-images-by-integrating-minio-with-nestjs-419e4e629b5d>

## 基于网络的应用程序的最小设置

![](img/4220cd419b4e4c25032549e172ce7c10.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Tamanna Rumee](https://unsplash.com/@tamanna_rumee?utm_source=medium&utm_medium=referral) 拍摄的照片。

*注意:如果您打算在本地安装 MinIO，本文假设您已经在本地机器上安装了 Docker。如果您在这里只是学习如何将 MinIO 与 NestJS 集成或者连接到外部 MinIO 服务器，那么您不需要 Docker。*

在我们开始讨论如何将 MinIO 与 NestJS 集成之前，让我们先来回答一下 MinIO 和 NestJS 是什么以及为什么。

对于那些不熟悉 MinIO 和 NestJS 的人来说，下面是对这两个工具是什么以及为什么使用它们的快速总结。尽管立即跳到实践环节可能很有诱惑力，但我建议你先了解你在做什么——尤其是如果你是新手的话。知道如何使用这些工具是一回事，但当你知道何时以及如何在正确的情况下应用它们时，它会更有价值。

但是，如果您已经熟悉这些工具，请随意跳过。

# MinIO 是什么？

MinIO 是一个对象存储服务器，实现了和亚马逊 S3 相同的公共 API。它也可以被视为一个开源的、高性能的云替代方案。它的最大单个文件上传大小为 5TB，与其他云服务提供的大小大致相同，并且也是为企业准备的。

# 为什么要用 MinIO？

既然我们已经有了云(例如 AWS、GCP、Azure)，为什么我们还要托管自己的云呢？乍一看，这似乎有些多余，但是原因很简单。请记住，MinIO 是作为企业解决方案构建的。一些公司已经拥有他们花费了数十万美元的现有基础设施，迁移到云并不是一个明智的想法。他们可以不使用第三方云服务，而是使用 MinIO，这实质上允许他们拥有一个自托管的云平台。这意味着他们可以重用现有的基础架构，并为其提供类似云的功能。

一些公司也有政府合同，这意味着他们不能简单地使用第三方云服务，因为他们需要满足特定的要求。这就是 MinIO 发挥作用的地方。

# 什么是 NestJS？

在其[官方主页](https://nestjs.com/)上，该团队将 NestJS 描述为一个开源、可扩展、通用和进步的 Node.js 框架。

他们的[官方文档](https://docs.nestjs.com/)实际上很好地解释了什么是 NestJS，但长话短说，它提供了一种灵活的模块化方法来创建你的后端 API——更不用说它是用 TypeScript 构建的并且完全支持 TypeScript。

# 为什么要用 NestJS？

因为 NestJS 是用 TypeScript 构建的，所以它越来越受到许多公司的青睐——特别是如果他们正在构建大规模的应用程序。NestJS 还提供了很棒的文档，这使得它非常容易学习。NestJS 提供的另一个有用的工具是 NestCLI，这是一个命令行界面工具，允许您快速直观地搭建和维护您的嵌套应用程序。它还支持大量的数据库和模块，如 TypeORM、GraphQL 和 OpenAPI。

如果您需要一个可靠的 Node.js 框架来支持一系列特性，那么 NestJS 正是您所需要的。

# 实践课程

现在，您已经理解了这两种工具的用途和原因，很明显它们是一个非常好的组合。两者都是企业就绪的解决方案，所以如果你是一家计划自己托管一些服务的公司，试试看。

# 初始设置

在我们开始之前，确保已经配置了 NestJS 和 MinIO。
我推荐用 Docker 设置 MinIO。但是，如果您正在尝试与一个已经存在的 MinIO 实例(可能由您正在使用的客户端处理)集成，下面有一节专门针对这一点！

# 设置 NestJS

按照[文档](https://docs.nestjs.com/first-steps#setup)中提供的步骤运行您的 Nest 应用程序。安装后，使用以下命令启动应用程序:

```
npm run start:dev 
```

导航到 [http://localhost:3000/](http://localhost:3000/) ，您应该会看到`Hello World!`消息。如果这有效，NestJS 就成功地安装在您的系统上了！

# 设置 MinIO

使用 Docker 是让 MinIO 快速启动并运行的最简单的方法。[文档](https://docs.min.io/)指导我们完成一个非常简单的设置过程。在 NestJS 创建的项目目录中，运行下面的命令:

```
docker run -p 9000:9000 minio/minio server /data
```

这将使用`minioadmin`作为访问和密钥来初始化 MinIO。如果您想将默认密钥更改为您自己的密钥，您可以这样做:

```
docker run -p 9000:9000 \
  -e "MINIO_ROOT_USER=AKIAIOSFODNN7EXAMPLE" \
  -e "MINIO_ROOT_PASSWORD=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" \
  minio/minio server /data
```

`MINIO_ROOT_USER`是访问密钥，而`MINIO_ROOT_PASSWORD`是秘密密钥。

一旦运行，导航到 [http://127.0.0.1:9000/](http://127.0.0.1:9000/) 应该会向您呈现 MinIO 的基于 web 的对象浏览器。这基本上是一个查看、下载或删除文件的 GUI。

# 将 NestJS 与 MinIO 集成

完成初始设置后，我们将安装`nestjs-minio-client`，这是一个用于 NestJS 的 [MinIO 客户端，它将使连接 MinIO 的方式更加容易。安装 npm 或 yarn，如下所示:](https://github.com/djedlajn/nestjs-minio-client)

```
npm install nestjs-minio-client --save yarn add nestjs-minio-client
```

完成后，我们需要为 NestJS 创建模块:

之后，我们可以从修改我们的`minio-client.module.ts`文件开始:

如果你注意到，我们正在使用看起来像`configService.get('ENV_VARIABLE_NAME’)`的环境变量。由于一些原因，这可能看起来与你传统的`process.env.ENV_VARIABLE`不同。首先，NestJS 推荐使用 ConfigService 模块，它允许我们在不同的`.env`文件之间轻松切换。它还允许我们将环境变量配置为全局变量，这样其他模块也可以很容易地使用它们。

我们采用这种方法的另一个原因是，模块实际上是在加载环境变量之前注册的。这可能会导致您遇到如下所示的错误:

```
Invalid endPoint: undefined 
```

这就是为什么我们选择调用`MinioModule.registerAsync()`而不是`MinioModule.register()`。`registerAsync()`函数允许首先加载环境变量，然后 ConfigService 可以获取它们。

既然我们了解了使用 ConfigService 的原因，我们首先需要使用以下命令安装所需的依赖项:

```
npm i --save @nestjs/config
```

然后，我们可以将这个模块导入到`app.module.ts`中。传入`isGlobal: true`属性允许它在 AppModule 导入的其他模块中使用:

对于额外的上下文，在一个适当的项目场景中，我们通常会有一个`.env`文件，我们在其中保存密钥和秘密。这是因为在应用程序中直接硬编码你的密钥/密码从来都不是一个好主意。通过检查应用程序的代码，可以很容易地抓取或访问它。只要记住*而不是* 提交并推送到 GitHub 或任何其他公共网站就行了！它们也可以被注入到 Docker 容器中，这在部署过程中非常方便。

现在让我们创建我们的`.env`文件。请记住，环境变量名需要与我们在嵌套应用程序中编写的相匹配:

设置好之后，我们可以在`minio-client`文件夹下创建一个`file.model.ts`，导出我们可能使用的所有必要接口:

继续我们的`minio-client.service.ts`文件，这是执行图像保存和删除的逻辑:

如果你仔细观察，我们正在散列日期字符串，并在将它保存到桶中时使用它作为文件名。这样做的原因是我们希望避免重复的文件名。这样做还可以避免文件名中的空白字符问题，空白字符会在文件检索过程中引起问题。

设置好服务文件后，现在是时候创建我们的模块了，该模块将实际接收和处理 API 调用。通常建议将代码分割成模块，这是 NestJS 的要点。因此，我们可以创建一个名为`image-upload`的新模块来处理图像上传:

我们现在可以修改我们的`image-upload.service.ts`文件了。这里，我们将使用我们在`minio-client.service.ts`中创建的上传功能。如果您还记得，它会返回一个包含上传图像的完整路径的`url`属性。可以返回给控制器，然后在我们进行 API 调用时作为响应返回:

一旦处理好，我们就可以修改`image-upload.controller.ts`。我们可以使用`FileInterceptor`拦截器来处理以`multipart/form-data`格式发布的数据:

最后，我们需要将`MinioClientModule`导入到`image-upload.module.ts`中，因为我们正在使用`MinioClientService`:

# 斗牛中的最后一剑

唷，那是一大堆的设置。在我们开始发布数据之前，让我们先重启 NestJS，因为我们添加了新的环境变量。我们还将通过 MinIO 浏览器创建我们的 bucket。确保桶名与您在`.env`文件中输入的相匹配。重启 NestJS 时，确保终端中没有记录错误。

我们可以使用 Postman 来发送我们的请求，因为我们在本教程中没有设置前端应用程序。您的 POST 请求应该发送到[http://localhost:3000/image-upload](http://localhost:3000/image-upload)，因为这是我们在`image-upload.controller.ts`下设置的端点。在“Body”选项卡下，确保选择`form-data`并将键设置为`image`并将类型设置为`File`。可以参考下面的截图:

![](img/7e66277b7a1ecc5ba30838dadeee2367.png)

如果您的请求通过了，而 NestJS 没有报告任何错误，这意味着它成功了！如果您刷新 MinIO 浏览器，您现在应该可以在那里看到您的文件。

但是，如果您试图导航到通过 POST 响应返回的 URL，您将会意识到您被要求登录。你可能希望它只显示图片，如果你使用 AWS 或 GCP 等第三方云服务，这是可以预料的行为。那么，这是怎么回事？你不会想给别人你的访问权和密钥，对不对？

# 收尾

为了确保我们可以通过 URL 直接检索图像，就像我们通常对 AWS 或 GCP 所做的那样，我们需要修改 bucket 的策略。我们可以使用`setBucketPolicy`函数来修改 bucket 的策略，并使其可公开访问。由于我们只需要调用`setBucketPolicy`一次，我们可以临时修改`minio-client.service.ts`中的代码，并用它来调用构造函数中的那个函数:

一旦文件被保存，NestJS 将自动重启，并进行新的修改(假设您使用了`npm run start:dev`)。如果没有，就停止并再次启动 NestJS。您应该在输出中看到这一点:

![](img/e4165d86e5cbe2ab3c66198fffe26f0d.png)

NestJS 应注销“存储桶策略集”

如果您看到这个输出，您现在可以删除我们所做的更改，因为我们只需要它运行一次，而不是每次应用程序启动时。如果您现在导航到该 URL，您应该会看到我们的图像立即加载，而不会提示您输入登录凭据！

![](img/4706e395f5b9f5989c943d8db2eade63.png)

一旦设置了新策略，您应该会立即看到该图像。

这就对了。我们成功地设置并集成了 MinIO 和 NestJS，并让它们上传、存储和检索图像！给你们自己一点鼓励！

# 结论

事实证明，将 MinIO 和 NestJS 结合使用比想象的要简单。老实说，设置非常简单，我们不需要配置任何实例，也不需要经历这个过程中的那么多步骤。

当然，这是在本地运行，而不是在世界任何地方公开。但是，如果您已经有了现有的基础架构，或者您的客户想知道他们是否应该迁移到云，那么您现在知道了一个新的替代方案！

我想感谢你们所有人花时间通读并阅读这篇文章。我希望它能帮助你学习新的东西，或者在你尝试类似的事情时作为一个指南。