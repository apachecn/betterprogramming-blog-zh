# 使用 Node.js、Express 和 Multer 将图像和视频流式传输到 Google 云存储

> 原文：<https://betterprogramming.pub/streaming-images-videos-to-google-cloud-storage-with-node-js-express-and-multer-%EF%B8%8F-7455c60f310>

## 从 AWS S3 迁移到 Google 云存储

![](img/bda4ad7eaf7742703c4a637de0b4f350.png)

*注意:为了简单起见，我使用一个 config.js 文件来保存配置值。我强烈推荐使用* [*dotenv*](https://www.npmjs.com/package/dotenv) *而不是这种方法。*

# **问题**😒

我的任务是转换代码，主要是上传高分辨率图像和大型视频文件。它必须高度可靠，更重要的是，不再依赖 AWS S3。我不会详细讨论 S3 与其他存储提供商的对比，但我会说，当我选择使用云存储时，谷歌的基础设施非常令人愉快——漂亮的用户界面、令人惊叹的文件浏览器和几十个选项，与 S3 相比，它们实际上是有意义的(对我来说)。

有一个问题…谷歌几乎没有把不在磁盘上的图片上传到云存储的例子。即使他们有，对我来说也已经过时了。在令人头疼的问题和数小时研究 StackOverflow、查看 SDKs 源代码等之后。现在我可以说我已经完成了任务。我终于可以*使用 Express.js 和 Multer 上传图片了，我很乐意分享让这一切工作的代码。*

# 要求📜

1.  上传任何媒体文件(音频、视频、图像等。)
2.  设置`UUID`为名称
3.  将正确的扩展名添加到`end`
4.  获取`MIME`类型以传递给谷歌云存储
5.  将对象 ACL 设置为`public`，以便我们可以在浏览器中查看

# **解决方案**🚨

[Express](http://expressjs.com/) ， [Multer](https://www.npmjs.com/package/multer) ，Node.js 的 [Google 云存储](https://www.npmjs.com/package/@google-cloud/storage)包，代码示例如下:

## 1.路由文件🚓

## 2.控制器🚂

## 3.配置🛠

# 就是这样！👏

很简单，对吧？如果你对你的实现有任何问题，请在下面的评论中提出，或者在推特上联系我。我总是乐意帮忙！