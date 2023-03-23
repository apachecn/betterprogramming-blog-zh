# 使用 Python 的 AWS S3 桶教程:上传、删除和管理桶中的文件

> 原文：<https://betterprogramming.pub/aws-s3-buckets-with-python-tutorial-uploading-deleting-and-managing-the-files-in-buckets-dd90d4fd45fe>

## 初学者清晰的概念性指南

![](img/476cdd8bf44d60dea3353fe2c9a69ded.png)

埃米尔·佩龙在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

亚马逊上的 S3 存储桶是你可以存储文本文件、音频文件、视频文件、图像和任何其他你喜欢的材料的地方。

S3 有 API，可以调用这些 API 来完成所有可以在数据库上完成的操作。

# 1.创建与 S3 联系的资源

有两种方法可以连接到 S3。

1.  客户端:与资源相比，旧版本的编码更详细。
2.  Resource:客户端的新版本，提供了更多处理操作的方法，减少了冗长的编码实践。

我们坚持使用资源来连接这里。我正在为每个任务开发函数，所以最后，我们将在一个类中收集所有的函数。这样，我们可以增加它的可重用性。

一个类和一个构造函数

您需要拥有代码中提到的三个凭证。如果你想创建一个 S3 账户，请按照这个[教程](https://rockstarhelpdesk.com/knowledge-base/how-to-create-your-amazon-s3-account-and-bucket/)操作。

我将在这里使用一个已经在 S3 创建的桶。我的桶名存储在我从 S3 账户得到的`bucket_name` 中。有一些资源方法可以从 python 创建 bucket，但是为了简单起见，我将使用一个现有的 bucket。

# 2.使用自动多部分将数据上传到存储桶

如果文件很大，例如 1GB，S3 存储桶允许并行线程同时上传文件块，以减少上传时间。如果数据小于 1GB，单个线程将完成上传。

多线程和单线程之间的切换是自动的。数据阈值需要如上设置，例如我使用 1GB。

阈值将由您设置，因此您将根据任务调整它。

上传数据的功能

强制参数:

1.  `bucket_name (String)`:S3 桶名
2.  `data (byte string)`:字节字符串对象**。**

注意上传方法的名称，它的 upload_fileobj()。此方法用于上载二进制类型的对象。这意味着如果文件驻留在您的本地系统中，它将不会是二进制形式。你必须使用:

```
with open('filename', 'rb') as data:         
     s3.upload_fileobj(data, 'mybucket', 'mykey')
```

`filename`是文件在系统上的路径。`rb`意为读写二进制。

如果数据是从外部 URL 检索的，那么它已经是二进制形式，因此该方法无需任何额外的更改就可以工作。

如果您想了解更多关于数据如何在网络上传输以及字节字符串和简单字符串之间的区别，请阅读我在参考资料一节中的文章。

3.`file_name (String)`:你喜欢的任何文件名。这将是 S3 桶中的文件名，并且将是文件的标识。因此写一个有意义的名字。

次要参数:

4.`file_size (String/int)`:我们要上传的文件的大小。在`ProgressPercentage(file_name,file_size)`函数中，类型变成了浮点型，因此字符串和整数都有效。

5.`content_type (String)` : 数据的类型；。pdf、mp4 等。

`TransferConfig`是配置文件，其中:

1.  `multipart_threshold`:多线程激活的上限。
2.  `max_concurrency`:螺纹数量。默认值为 10。
3.  `multipart_chunksize`:每个线程中数据的大小。
4.  `use_threads`:启用线程。

# 3.查看存储桶内容

bucket 中的每个对象都有我们可以使用的属性。`obj.key`显示了我们上传的数据的`file_name`。其他有用的属性可以在 S3 的官方文档中找到，链接在本教程的底部。

# 4.最后一节课有更多有用的方法

进度百分比是由 boto3 S3 提供的内部类。这在上传大文件时非常有用，因为它显示了加载进度。因此，我们可以看到一个带有数字的进度条，而不是一个空白屏幕。

我已经修改了这个类，使它将`file_size`作为输入，而不是从操作系统库本身进行计算。因为我使用的是外部 URL，所以我必须自己给出文件大小。

你可以使用这个类，随心所欲地制作对象，玩水桶。

通过使用这个类，您还可以用一个连接创建多个存储桶。此外，您不需要在上传/下载后关闭连接，因为连接是 Http 1.1，它会在一段时间后由 boto3 自动关闭。

# **资源**

[](https://www.kite.com/python/docs/boto3.s3.inject.upload_fileobj) [## 通过代码行完成、无云处理来加快编码速度

### Kite 是为 Python 开发者提供的免费自动完成工具。使用 Kite 插件为您的代码编辑器更快地编码，具有…

www.kite.com](https://www.kite.com/python/docs/boto3.s3.inject.upload_fileobj)  [## 类别:Aws::S3::对象摘要

### 从亚马逊 S3 检索对象。要使用 GET，您必须拥有对象的读取权限。如果您授予对…的读取权限

docs.aws.amazon.com](https://docs.aws.amazon.com/sdk-for-ruby/v2/api/Aws/S3/ObjectSummary.html) [](https://medium.com/@hatim.zahid/serialization-and-deserialization-how-data-travels-in-a-computer-network-13f61dc225c4) [## 序列化和反序列化:数据如何在计算机网络中传输？

### 为什么选择 JSON？什么是 TCP？什么是字节串和字节流？

medium.com](https://medium.com/@hatim.zahid/serialization-and-deserialization-how-data-travels-in-a-computer-network-13f61dc225c4)