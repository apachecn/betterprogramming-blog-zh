# 在你的 Spring Boot 项目中上传文件到亚马逊 S3 的两种方法

> 原文：<https://betterprogramming.pub/2-ways-to-upload-files-to-amazon-s3-in-your-spring-boot-project-65620411622d>

## Rest APIs 和使用预签名的 URL

![](img/26682794fa72ce1308a06e03641fd7e1.png)

照片由[JF·马丁](https://unsplash.com/@numericcitizen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果您的项目必须处理文件，那么您将需要存储这些文件的存储空间。我想到的最佳解决方案是 s3。**亚马逊 S3** 是 AWS 提供的一项服务。在存储文件方面，S3 是一个高度可扩展和可靠的解决方案。

在本文中，我将向您展示如何在您的项目中将文件上传到 s3。我假设您的 spring boot 项目是一个微服务，它已经向您的前端公开了 API。

当我们想要上传文件时，有两种方法可以做到:

*   第一种方式是前端通过 REST API 将文件传递给你的服务器，然后你调用 s3 SDK 上传文件并将结果返回给前端。
*   第二种方式是所谓的*预签名 URL。*预签名*URL 是具有*有限权限和到期*的 URL。任何客户端都可以使用该 URL 将文件直接放入 s3，而无需担心 AWS 访问密钥和密码。*

*如果您看到，在第一个选项中，文件将转到您的服务器，然后再次从您的服务器转到 s3。这将增加延迟，因为文件的数据传输会发生两次，因此不建议使用第一个选项。第二个选项是我们上传文件的最佳选择。*

*让我们看看我们能做些什么。*

*   *首先，我们将创建一个 REST API，我们的前端将调用它，我们将创建一个具有有限权限的预签名 URL，并返回到前端。*
*   *前端会直接把文件上传到 s3。*

*让我们将 SDK 依赖添加到我们的`pom.xml`*

*创建一个新的配置类`awsS3Config.java`，我们将在其中为 s3 创建一个 bean。*

*确保您已经将`access-key and secret-key`添加到您的`application.yml`文件中*

*现在让我们创建一个服务，它将为 s3 操作与 AWS 通信。*

*在上面的类中，我们有`generatePreSignedUrl`，它接受下面的参数。*

*`filePath`:这是您想要上传文件的 s3 的对象键。*

*`bucketName`:这是 s3 存储桶的名称。*

*`httpMethod`:这是您的预签名 URL 允许的 HTTP 方法。这里我们想要上传文件，所以我们将在这里传递`HttpMethod.PUT`。*

*现在让我们在控制器中创建一个 REST API 端点。*

*这个 API 将返回一个预先签名的 URL，有效期为 10 分钟，并且只接受`PUT`方法直接从前端上传文件。*

> *跟着我上 [Meta](https://www.instagram.com/manoj_ahi/)*

*干杯！！*