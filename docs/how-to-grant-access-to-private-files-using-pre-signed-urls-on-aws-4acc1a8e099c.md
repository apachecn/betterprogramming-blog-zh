# 如何在 AWS 上使用预签名的 URL 授予对私有文件的访问权限

> 原文：<https://betterprogramming.pub/how-to-grant-access-to-private-files-using-pre-signed-urls-on-aws-4acc1a8e099c>

## 通过 S3 和 CloudFront 交付私人内容

![](img/082720771ea70759676afdaced2a59a7.png)

照片由[戴恩·托普金](https://unsplash.com/@dtopkin1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

当尝试设计一个解决方案架构，其中应用程序可以在像 S3 这样的存储选项上生成和存储私有文件，并允许特定用户在一定时间内访问这些文件，同时仍然保持私有时，我们可以根据实际用例场景遵循两种不同的方法。

让我们写下一些使用案例，看看我们如何才能找到最佳解决方案:

1.  将音频文件从一种格式转换为另一种格式的应用程序，允许用户通过五分钟后过期的链接下载转换后的文件。
2.  提供优质内容(研究见解、文档、视频等)的应用程序。)仅限登录用户，时间有限。
3.  向属于指定 IP 地址范围的特定用户提供对私有内容的访问的应用程序。

对于所有这些不同的场景，我们可以选择通过像 S3 这样的存储选项来存储优质内容，并直接或从 HTTP 服务器进行交付。

但是要限制对内容的访问，我们可以使用两种不同的方法。

# S3 预签名网址

假设我们的应用程序将为特定用户提供一个私有文件，存储在 S3 的私有存储桶中。这可以通过为用户提供一个预签名的 URL 来实现，该 URL 可以由有权访问该私有存储桶的 IAM 用户生成。

我将展示一个用 Python 编写的快速演示，使用 [Boto 3](https://boto3.readthedocs.io/) 库与 AWS 服务进行交互。最初，您需要创建一个 IAM 用户，该用户至少拥有存储这些私有文件的私有存储桶的`GetObject`和`ListBucket`权限。

然后，确保为该用户生成访问密钥，并用这些凭证配置本地 CLI，以便能够通过 Boto 3 库与服务进行交互。

转到预签名 URL 的实际生成，下面的代码演示了预签名 URL 的生成，用于在私有桶中下载私有文件(名为`s3.png`)。

它还显示了用于将文件上传到私有 bucket 的预签名 URL 的生成，但是我们将在一分钟后讨论这个问题。

现在，在生成预签名的 URL 之后，具有该 URL 的用户可以在有限的时间范围内(例如创建后五分钟内)。

让我们更深入地了解一下背景中发生了什么。生成的 URL 如下所示:

```
[https://**BUCKET_NAME**.s3.amazonaws.com/s3.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=**XXXXXXXXXXXX**%2F20200305%2Feu-central-1%2Fs3%2Faws4_request&X-Amz-Date=20200305T220655Z&X-Amz-Expires=3600&X-Amz-SignedHeaders=host&X-Amz-Signature=53deb5df885a8fc00e02cc88076206f50b093ae5a305cbbeab5620e3a776641e](https://codingimpossible-presigned.s3.amazonaws.com/s3.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIA2TJR6SRO6MQPM6TN%2F20200305%2Feu-central-1%2Fs3%2Faws4_request&X-Amz-Date=20200305T220655Z&X-Amz-Expires=3600&X-Amz-SignedHeaders=host&X-Amz-Signature=53deb5df885a8fc00e02cc88076206f50b093ae5a305cbbeab5620e3a776641e)
```

从高层次的角度来看，这个 URL 允许将要使用它的用户代表生成该 URL 的实际用户请求私有文件。(这就是为什么创建预签名 URL 的用户必须有权访问 bucket 和其中的文件)。

如果我们看一下 URL 的查询参数，关键参数是`X-Amz-Credential`，它实际上包括所有者的访问密钥、生成日期(`20200305`)、桶的区域(`eu-central-1`)和 AWS 服务(S3)。

除此之外，我们可以看到另一个重要的参数，`X-Amz-Expires`，它给出了 URL 有效性的总秒数。

其他参数与签名算法和加密相关，但由于这篇文章并不打算深入探讨安全部分，您可以在亚马逊 AWS 文档中阅读更多内容。

通常，由于应用程序能够让用户在一定时间内下载私有文件，因此在一些用例中，它应该能够让用户直接上传文件到私有存储桶，而不需要任何中介。

这也可以通过生成一个预签名的 URL 来完成，并启用 POST。

上面的代码示例展示了如何为文件关键字`s3-post.png`生成一个预先签名的 POST URL，并展示了文件上传过程。

这里有几点需要注意:

*   生成的预签名后的 URL 应该是这样的:[https://BUCKET _ name . S3 . amazonaws . com/](https://codingimpossible-presigned.s3.amazonaws.com/)
*   除了 POST URL，生成方法还返回几个参数(就像 GET 示例的查询参数一样)，这些参数允许具有 URL 和参数的用户代表生成 URL 的用户执行 POST 请求(即上传文件)。
*   备注:确保分配 IAM 用户`putObject`权限。

在上面的代码示例中，文件上传过程是通过提供参数和文件作为 POST 请求主体自动完成的。

如果您要手动执行 POST 请求，例如。使用 [Postman](https://www.postman.com/) ，确保将 URL 参数以如下图所示的有序方式放置在请求体中。

![](img/67280c237eb19eab8c8ec9efa4701a14.png)

到目前为止，我们找到了一种方法来创建一个应用程序，通过生成预先签名的 URL，允许用户在一定时间内访问存储在 S3 上的私人文件。

注意，预签名 URL 的生成是 S3 的一个特性，但是如果我们想提供对路径或 IP 地址而不是文件的有限访问，会发生什么呢？CloudFront 签署了拯救 URL。

# CloudFront 签名的 URL

关于 [CloudFront](https://aws.amazon.com/cloudfront/) 的一个简短说明会将其描述为一个内容交付网络，通过将内容缓存到世界各地的边缘位置来提高访问性能。

为了加快应用程序的内容交付，我们通常会使用 CloudFront。你可以在 AWS 文档中了解更多信息。

现在，让我们看看它如何帮助我们解决上述交付问题。

CloudFront 提供签名的 URL，以便[提供私有内容](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-task-list.html)，这些内容可以存储在 S3 桶中、EC2 上(通过其 IP)、ELB 上，甚至是你自己的 HTTP 服务器上。

为了演示这一点，我将在 CloudFront 上设置一个快速演示发行版，它将通过首先使用一个[源访问标识](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)限制对 S3 的访问，来提供来自我的私有 bucket 的文件。

创建发行版时设置这一点非常简单，只需检查`Restrict Bucket Access`和`Create a new Access Identity`。这意味着用户只能使用 CloudFront URLs 而不是 S3 URL 来访问 S3 文件。

接下来，为了创建一个签名的 URL，我们首先需要一个 CloudFront 密钥对。

在账户菜单中转到`My Security Credentials`，选择`CloudFront key pairs`。创建一个新的对(或者通过首先手动创建来上传您自己的对，例如，通过使用 [OpenSSL](https://www.openssl.org/) )并下载将用于签署 URL 的私钥。

对于已签名的 URL，除了其他事情之外，我们可以指定一个可以访问该 URL 的时间间隔。在 CloudFront 上发出请求后，URL 规范与最初创建的策略相匹配，可以是一个[固定的](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-creating-signed-url-canned-policy.html)或一个[定制的](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-creating-signed-url-custom-policy.html)策略。

为了简单起见，下面的代码通过创建一个自定义方法来演示签名 URL 的生成，该方法加载私钥并使用该密钥对资源 URL 进行签名，从而以这种方式生成一个签名 URL。

产生的 URL 如下所示，我们可以将签名记为查询参数，当请求资源时，将根据访问策略对其进行检查:

```
[http://**XXXXXXXXXX**.cloudfront.net/s3.png?Expires=1583492400&Signature=P7hD6lrJ3Gqh2UgyqvYMiCALbv91WN7mvlDNBMRzOXJOiHwGe0Yh4HuOwvUDGstGx~c64nGNpU1n1TbUloc6WLUfkYtxEBOUQSaMGb4BM~Dd9p4i1pRPp7gCz3c8cHcnuRGTEdpJzDMN835y8Op6~V-FWvjJHCkcPNsIll-sv9oZ2oRJLSoqVbTh-1sXaJ4LAq11MCFf8zGaBvj65P5Wc4SYv5Vg63~CXc67xAQuwt7CClgyaIby6ooehKGddokL9m0XwRFIMr6SCx1HxQcA4jYEdgixnyYd6X2gc1WnEuYdv-Fxna5n3TBocoNbPAlcX8KV5j~HB1eFRAf2I3lQiQ__&Key-Pair-Id=**X**](http://d2gezez09130k9.cloudfront.net/s3.png?Expires=1583492400&Signature=P7hD6lrJ3Gqh2UgyqvYMiCALbv91WN7mvlDNBMRzOXJOiHwGe0Yh4HuOwvUDGstGx~c64nGNpU1n1TbUloc6WLUfkYtxEBOUQSaMGb4BM~Dd9p4i1pRPp7gCz3c8cHcnuRGTEdpJzDMN835y8Op6~V-FWvjJHCkcPNsIll-sv9oZ2oRJLSoqVbTh-1sXaJ4LAq11MCFf8zGaBvj65P5Wc4SYv5Vg63~CXc67xAQuwt7CClgyaIby6ooehKGddokL9m0XwRFIMr6SCx1HxQcA4jYEdgixnyYd6X2gc1WnEuYdv-Fxna5n3TBocoNbPAlcX8KV5j~HB1eFRAf2I3lQiQ__&Key-Pair-Id=APKAJLBB56IPDZ52MMOQ)**XXXXXXXXXXXXX**
```

# 摘要

总而言之，通过使用签名的 URL，可以通过 CloudFront 发行版在有限的时间内授权访问私有内容。可以保护和提供的内容可以是路径或 IP 地址。

最后，如果我们想通过 CloudFront 提供多个私有文件，而不是一个，正确的解决方案是使用签名 cookies，这是未来帖子的热门话题。

感谢你的时间，我希望你觉得这是一个愉快的阅读。

保重。

# 资源

*   [CloudFront 私有内容](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/PrivateContent.html) — AWS
*   [私人内容任务](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-task-list.html) — AWS
*   [已签名的网址](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-urls.html) — AWS
*   [CloudFront 示例](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/cloudfront.html#examples) — Boto 3