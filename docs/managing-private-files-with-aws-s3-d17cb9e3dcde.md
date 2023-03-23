# 使用 AWS S3 管理私有文件

> 原文：<https://betterprogramming.pub/managing-private-files-with-aws-s3-d17cb9e3dcde>

## 了解使用 AWS S3 在应用程序中处理私有文件的两种方法

![](img/2e9fe7277809e270b9f1d20b0dc2013f.png)

作者图片

# 概观

回到 2020 年，在我工作的公司，有人告诉我，我们正在将我们的图像从 [Cloudinary](https://cloudinary.com/) 迁移到 [AWS S3](https://aws.amazon.com/es/s3/) ，我将负责执行迁移。长话短说，我完成了迁移，但在此过程中，我遇到了一个问题—我们的映像必须是私有的，只能由管理员查看。两年后，我面临着一个类似的问题，但是使用了不同的用例。

在本文中，我将向您展示使用 AWS S3 管理私有文件的两种方法:一种是我在 2020 年做的，另一种是我现在计划做的。

# 方法 1:通过后端端点获取文件

这个解决方案是我在迁移图像时使用的。它主要包括创建一个后端端点，该端点将验证请求图像的用户是否有查看文件的必要权限。

![](img/ee1d689d6c10c7bfc8f963cb24800796.png)

## **我的**u**sè案**

授予管理员访问权限，这样他们就可以看到很少被请求的私有图像(三倍)。

这种方法的一个缺点是，私有文件首先从 S3 传到我们的后端，再从那里传到客户端，这增加了传输成本。但是这对于我们的用例来说是可以的，因为这些图像仅仅被请求一两次，然后就忘记了。毕竟，除非我们需要验证历史数据，否则不再需要它们。

# 解决方案 2:预先设计 URL

这个解决方案在于使用所谓的预设计 URL。这些 URL 背后的想法是在一段时间内提供对私有文件的访问，我们通过在后端使用私有凭证对我们的 S3 的对象密钥进行签名来实现，然后设置一个过期时间，在该时间内，预签名 URL 将对请求有效。

![](img/d9beaed2f36afbd7b99ea010c50be0d6.png)

如您所见，对于这个方法，我们还使用一个后端端点来验证用户是否有权查看私有文件，因此我们可以返回签名的 URL。

## **我的** u **se 案例**

呈现一个带有姓名和图片的雇员列表(为了保持示例简单),这些雇员将在应用程序中经常被访问。

这样，我们解决了第一种方法的传输成本问题，而不是将文件从 S3 传输到我们的后端，然后再传输到客户端。我们只需向客户端发送一个签名的 URL，该 URL 可用于直接从 S3 获取文件。

这里我们将面临的一个问题是，每次我们对一个 URL 签名时，都会生成一个新的签名。所以浏览器不能缓存图像，因为 URL 总是会改变，所以我们每次都会有这样的东西:

![](img/dec6f5a394a2a6d1d3ac6bb16e00457f.png)

在每个请求中，用户将下载文件，而不是从缓存中获取。

![](img/2edd9046fbfa1b37360f9f7289588b12.png)

这个问题在这篇[文章](https://advancedweb.hu/cacheable-s3-signed-urls/)中有详细解释，但基本上，我们需要一种方法来为预先设计的 URL 获取相同的签名，这样浏览器就可以缓存文件。

另一件事是，我需要在前端呈现一个员工列表，其中每个人都有一个私人图像，但拥有一个每次只签署一个 URL 的端点将是我们服务器的一个 [N+1 查询问题](https://hernanreyes.dev/how-the-n1-query-problem-can-burn-your-database)。因此，当我们返回员工列表时，我们还必须返回预先签名的 URL 以避免这个问题。

在我调查这个的时候，我看到[idea . so](https://www.notion.so/)和 [raindrop.io](https://raindrop.io/) 使用这些预先设计好的 URL 来处理你账户里面的私人文件。

# 固定 S3 铲斗

我们迟早要做的事情是配置我们的 S3，以便在使用 AWS SDK 时访问我们的后端，这样我们就可以从我们的 S3 桶上传或获取文件。

AWS 为我们提供了不同的方法来访问我们的 S3 bucket，但是因为我们在同一个 AWS 帐户中工作，所以我们将使用 IAM 策略来提供对我们的 bucket 的访问。

## IAM 策略

这些策略控制对 AWS 上资源的访问(在我们的例子中是 S3 ),并附加到 AWS 帐户中的用户、角色或组等实体，以定义这些实体在 AWS 提供的不同服务中可以做什么。

现在，我们将继续进行配置:

这里我假设您已经有一个 AWS 帐户

## **创建存储我们的对象的桶**

![](img/19f9702b5079bd92fb7c16263e3eb3b9.png)

如果您也有公共文件，您必须像我在这里做的那样禁用阻止公共访问选项。

## **创建一个策略来定义我们用户的权限**

![](img/e07a0c0eef751c9ef14155770dbad9a2.png)

对于我的例子，我只需要三个权限:

1.  `PutObject`上传文件
2.  `PutObjectAcl`定义文件是公开的还是私有的
3.  `GetObject`获取文件

此外，我指定这个策略只对我们刚刚创建的带有字段的 bucket 有效:`"Resource": "arn:aws:s3:::hrmtest/*"`

## **创建一个用户来获取我们后端所需的凭证**

![](img/aed566152593aa8a2cffef7cef6b0d91.png)

这里，我们使用上一步中刚刚创建的策略创建用户，这样我们就可以获得在应用程序中使用它们的凭证。

之后，我们就可以在应用程序中使用我们的凭证了。

# 代码时间！

我正在准备一个小项目，我们将能够从 AWS S3 上传和获取文件。我们将使用从 AWS 下载的凭证，并将它们放在启动服务时将被读取的`.env`文件中，这样我们就可以向 AWS 发出请求。代码如下:

```
# AWS S3
S3_ACCESS_KEY_ID=AKIAZCF3JGYORXJMZPMY
S3_SECRET_ACCESS_KEY=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
S3_BUCKET_NAME=hrm-article-demo
S3_BUCKET_REGION=us-east-1
```

到本文发表时，凭证将不再存在，所以不要费心尝试用它们做些什么。

在我们读取凭证之后，我们将使用它们来获得到 AWS S3 的会话。代码如下:

## **上传方式**

有了这个，我们就可以上传公共或私人文件。

## **GetFile 方法**

为了获得私有文件，我们将使用这个方法。对于公共文件，AWS 给我们一个如下格式的 URL:`https://{bucket-name}.s3.amazonaws.com/{object-key.png}`→[https://HRM-article-demo . S3 . amazonaws . com/employees/1399 d998-b505-4ce 2-b49b-BD 8073 de 9 b 9 e . jpg](https://hrm-article-demo.s3.amazonaws.com/employees/1399d998-b505-4ce2-b49b-bd8073de9b9e.jpg)。

## **预设计方法**

有了这个，我们将能够签署我们的私人文件的关键，所以我们可以看到该文件一段时间。

另一种不需要每次都获得相同签名的方法如下所示:

如果你不介意每次都得到相同的签名，这是一个更简洁的解决方案。

这里，我只展示了本文感兴趣的代码。如果你想看到项目的所有代码，只需进入:[https://github . com/hernanhrm/blog-examples/tree/main/private-files](https://github.com/hernanhrm/blog-examples/tree/main/private-files)。

此外，我提到在后端，我们需要验证用户是否有所需的权限来查看所请求的文件，但我没有在本文中包括该代码，因为我将撰写一篇关于如何在应用程序中处理身份验证和授权的文章。

# 游戏时间！

现在我们有了代码，我们可以玩它了。

## **上传图像**

我们将上传一个私人图像，所以看到它的唯一方式是通过我们的端点获取文件或签署 URL。

![](img/2927486fec52cfc28687fe0169388e40.png)

一旦我们发送请求，我们将得到如下响应:

![](img/e00d29ac245a1bc8c0dfeaa482093417.png)

正如我刚才告诉你的，查看图像的唯一方式是通过我们的端点来获取文件或签署 URL，因此如果我们尝试使用 AWS 给我们的 URL，它将向我们显示如下内容:

![](img/27ea5b907614da92c45c92eebe730f2f.png)

但是，如果我们使用我们创建的端点从 AWS 获取文件，我们将获得文件:

![](img/79df03807451018287d6ecce5eb4e54b.png)

对，就是我。现在让我们试着签名 URL:

![](img/ba3df47aa4e12f3dde7f0db5a4f6dfc3.png)

现在，如果我们使用预先指定的 URL，我们也将在文件仍然有效时获取它。看起来是这样的:

![](img/5d4eb078cb731515848758d6f338fd3a.png)

## `**GetEmployees**`法**法**

对于我的用例，我告诉过你我需要为员工的照片生成一个签名的 URL，但是调用一个端点来逐个签名一个 URL 将是一个 N+1 查询问题。因此，我将使用一种方法在请求雇员时获取辞职的 URL。代码如下:

因此，如果我们为我们的员工上传一些图像，当我们请求 GET `api/v1/employees`时，我们将在当天每次执行请求时获得一个带有相同签名的已签名 URL，正如我们可以看到的，浏览器缓存了图像。

![](img/c9cb9e875a8561160912e515d6ccc7de.png)

# 结论

好了，这比我预期的要长，但是我用我在工作中遇到的用例向您展示了如何配置您的 S3 和从 AWS S3 获取私有文件的两种方法。所以我希望你能根据你的情况决定用什么方法。

# 参考

1.  为了便于说明， [Excalidraw](/excalidraw.com)
2.  [深入了解预签名网址的工作原理](https://advancedweb.hu/tags.html#S3%20signed%20URLs-ref)
3.  如果你会说西班牙语，这里有一个关于 https://ed.team/cursos/s3 S3 的很好的课程
4.  [使用预先设计的 URL 保护 AWS S3 上传的安全](https://medium.com/@aidan.hallett/securing-aws-s3-uploads-using-presigned-urls-aa821c13ae8d)
5.  [项目资源库](https://github.com/hernanhrm/blog-examples/tree/main/private-files)