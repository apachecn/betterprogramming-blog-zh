# 用 Python 写一个脚本将文件备份到 Google Drive

> 原文：<https://betterprogramming.pub/uploading-files-to-google-drive-with-python-d08ae8fcbc49>

## 在事情发生之前，备份它！

![](img/d259f461c79b0cd385ed778eab921d56.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

有一天，我的一位同事问我是否为重要文件和文件夹设置了定期备份。虽然我以前用过 Dropbox 和 Google Drive，但我真的希望能够更精细地控制我想要备份的内容。此外，我对使用 Python 备份重要文件很感兴趣。

# PyDrive 简介

`PyDrive`是[Google-API-python-client](https://github.com/google/google-api-python-client)的包装库，简化了许多常见的 Google Drive API 任务。虽然 [Google Drive API](https://developers.google.com/drive/) 为您提供了连接到 Drive API 端点并进行必要调用的必要功能，但我发现`PyDrive`让生活变得更加简单，尤其是对于喜欢编写较少样板代码的 Python 开发人员来说，尤其是因为:

*   将`OAuth2.0`简化为几行灵活设置的代码。
*   将驱动 API 包装到每个资源的类中，使您的程序更加面向对象。
*   它有助于除 API 调用之外的常见操作，如内容获取和分页控制。

说够了，我们开始吧！

# 证明

在你做任何事情之前，去你的[谷歌开发者控制台](https://console.developers.google.com/apis/credentials)，你应该会看到一个标题为`OAuth 2.0 client IDs`的部分。点击列表中的一个条目，你会看到许多字段，包括`Client secret`。

如果您尚未创建凭证，请单击`Create credentials`按钮并按照说明创建新凭证，然后按照上述步骤找到`Client secret`。下载文件——它应该以`client_secrets.json`的名称存储在本地。请不要提交此文件！

既然您已经在磁盘上获得了客户端机密文件，那么对驱动器 API 进行身份验证就很容易了——您将知道为什么:

```
g_login = GoogleAuth()
g_login.LocalWebserverAuth()
self.drive = GoogleDrive(g_login)
```

这将在您的 web 浏览器中自动打开一个窗口，要求您使用创建凭据时使用的帐户登录。单击正确的帐户将完成授权流程，现在您的代码就可以开始使用 Google Drive APIs 了。

接下来，我们将看看如何创建文件和文件夹。

# 创建文件夹和文件

文件夹是只包含元数据的文件，可以用来组织 Google Drive 中的文件。具体来说:

*   文件夹是一个 MIME 类型的文件，没有扩展名。
*   您可以使用别名`root`来引用提供了文件 ID 的根文件夹

```
drive_folder = self.drive.CreateFile({
    'title': dir_name,
    "mimeType": "application/vnd.google-apps.folder"
})drive_folder.Upload()
```

文件的创建方式有点类似:

```
f = self.drive.CreateFile({
    'title': "{}".format(filename),
    "parents": [{"id": gdrive_folder_id}]
})
f.SetContentFile(filename.open())
f.Upload()
```

记住——在调用 file 对象上的`Upload()`方法之前，不会在 Google drive 上创建文件夹或文件。

# MIME 类型

一种**媒体类型**(也称为**多用途互联网邮件扩展或 MIME 类型**)是一种标准，它指示文档、文件或各种字节的性质和格式。它在 IETF 的 [RFC 6838](https://tools.ietf.org/html/rfc6838) 中被定义和标准化。

当您在 Google Drive 上创建文件时，您需要提供文件的正确 mime 类型——否则，内容将无法正确上传。

有一个很棒的 Python 库叫做 mime-types，可以让你猜测文件的 mime 类型。

```
import mimetypes
mime_type = mimetypes.guess_type(filename)
```

将所有这些放在一起:

完整的代码可以在这里找到:
[https://github.com/sauravbiswasiupr/backup-wizard](https://github.com/sauravbiswasiupr/backup-wizard)