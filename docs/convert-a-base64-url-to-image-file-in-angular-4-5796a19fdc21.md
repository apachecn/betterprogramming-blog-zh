# 将 Base64 url 转换为 Angular 4+格式的图像文件

> 原文：<https://betterprogramming.pub/convert-a-base64-url-to-image-file-in-angular-4-5796a19fdc21>

![](img/3164afcb0f2b6608d81edf3363774349.png)

嘿大家好！

我想分享我最近在一个项目中遇到的一个问题，在这个项目中，我需要通过相机捕捉来上传一张个人资料照片。我用的是[ngx-网络摄像头](https://basst314.github.io/ngx-webcam/)套装，它允许从网络摄像头/设备摄像头捕捉图像。

我集成了这个包，并且能够捕获图像——它在页面上显示正确。但问题是它给出的数据是 base64 URL，可以通过用*数据:URL 替换 *src* 来显示图像。*

在尝试了超过 10-15 个关于堆栈溢出的链接后，我找到了解决方案，并能够转换图像。起初，我认为它已经工作，但转换后的文件没有显示。于是我又找了一遍，终于得到了代码，加上一些自定义的修改。

# **更新**

以前有类似异步调用序列的问题，所以一些用户得到了 *atob* 函数错误。所以我重新排序了方法，现在一切都正常了。我为方法添加了返回类型，也为变量添加了数据类型。所以你可以更详细地了解在哪里发生了什么。

# 让我们看看代码:

首先从外部 URL 获取图像，并以字符串形式获取图像的 base64Data URL。

1.  getImage
    调用提交图像 URL 的用户。会依次调用其他方法。
2.  调用 getBase64ImageFromUrl
    从提供的 Url 获取图像。该 URL 可以是外部 URL 或 base64 URL。
3.  它将从图像中创建 base64Url。

然后创建本地使用或显示方法的图像

1.  createBlobImageFileAndShow
    它将从 base64 URL 创建图像 blob，这将用于本地处理图像或显示图像。
2.  dataUriToBlob
    获取数据 URI 并返回包装在 Observable 中的 Blob 文件的方法。
3.  generateName
    为图像斑点生成随机名称的辅助方法。

**演示:**我已经添加了更新代码的完整代码片段，您可以在复制之前测试代码。我还更新了所有类型的数据和返回类型。

从 Url 获取图像演示

***注意:*** *如果您在控制台中看到此错误，请确保您的窗口对象 atob 方法被正确调用，并且 base64Trimmed 数据 URL 作为参数被正确发送:*

```
Error: Failed to execute ‘atob’ on ‘Window’: The string to be decoded is not correctly encoded.
```

**更新-** 执行 blob 失败现已修复，不会再来。不过，你可以评论任何问题。

通过使用这些功能，您可以捕捉图像，并将其保存到一个文件夹中，或者轻松上传到在线服务器上。

此外，请访问 Tekraze.com 网站获取更多类似的技术帖子。

谢了。