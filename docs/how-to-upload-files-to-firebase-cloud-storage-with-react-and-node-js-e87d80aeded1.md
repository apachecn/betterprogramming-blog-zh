# 如何用 React 和 Node.js 上传文件到 Firebase 云存储

> 原文：<https://betterprogramming.pub/how-to-upload-files-to-firebase-cloud-storage-with-react-and-node-js-e87d80aeded1>

## 为您的应用程序构建文件上传程序的分步指南

![](img/67362306eb22c84ccebea6a03a637df7.png)

背景照片由 [Artem Sapegin](https://unsplash.com/@sapegin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/react?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我曾经参与过几个需要实现文件上传程序的项目，但总是发现这并不容易。

提交文件格式会引出不同的问题:“文件到底是什么？我们通过电线发送的是什么？我们如何处理这样一种格式来存储它并提供给用户？”

我最近使用 Firebase 云存储作为文件存储空间构建了一个，我想我会喜欢一篇带我了解它如何工作以及如何正确设置它的帖子。

所以，在这里，我希望它能回答你的问题，如果你需要下一个项目的样板文件，我会提供整个项目的回购。

先决条件:这篇文章假设你已经熟悉用 Express 构建 Node.js API 和用 React 构建前端。这里我们只关注文件上传部分。

# 设置 Firebase 云存储

首先，您需要一个 Firebase 帐户，其中有一个活动项目和一个打开的存储桶:

1.  连接或创建一个 [Firebase](https://firebase.google.com) 账户。
2.  创建新项目。
3.  为您的项目命名。
4.  激活或不激活谷歌分析，由你决定。
5.  在左侧菜单中，点击“存储”，然后点击“开始”。
6.  Rules:打开一个模式，显示您的 bucket 的默认读写访问规则。我们希望保持对写访问的身份验证。关于读取权限，这取决于您的需求。在这里，我们将更新规则，使我们的图像从他们的公共网址公开可读。

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read;
      allow write: if request.auth != null;
    }
  }
}
```

文档中关于存储安全规则[的更多信息。](https://firebase.google.com/docs/storage/security/start?authuser=0#sample-rules)

7.地点:明智地选择，你不能改变这一点。我住在巴黎，所以我选择了位于德国法兰克福的`europe-west3`。您可以在 [this Firebase 页面](https://firebase.google.com/docs/projects/locations?authuser=0)上找到完整的位置列表以及关于为您的项目选择数据位置的更多信息。

现在您已经有了可以使用的默认存储桶。在 [Firebase 的免费层](https://firebase.google.com/pricing)中，你可以拥有一个，最多存储 5 GB。如果你需要多个桶和更多的空间，你需要相应地升级你的计划。

现在我们有了存储桶，我们需要生成一个私钥，我们的 API 将使用它安全地连接到我们的桶。

1.  在左侧菜单中，单击顶部的设置轮。
2.  选择“服务帐户”选项卡。
3.  在页面底部，单击“生成新的私钥”按钮。这将生成一个 JSON 文件，其中包含您的 Firebase 帐户凭证。

你会得到一个警告，说这个私钥应该保密并放在一个安全的地方，所以要确保无论你把这个文件放在哪里，你都要*而不是*把它提交给你的远程 repo。

正如您将在下一步看到的，我们将为我们的服务器创建一个`api/`文件夹。我将把这个文件存储在这个文件夹中的一个`services/`文件夹中。然后我会将这个文件夹中的所有 JSON 文件添加到我的`.gitignore`中:

```
/api/services/*.json
```

现在我们已经准备好下一步，构建上传 API。

出于安全原因，我们希望从我们的服务器处理经过身份验证的调用，而不是从浏览器处理，因为在浏览器中，包含敏感凭据的环境变量更容易被访问。

# API: Node.js + Express.js + Multer

为了给你提供一个全功能的 repo，我将 API 包含在同一个项目文件夹中。

然而，这并不理想，因为把所有的鸡蛋放在同一个篮子里从来都不是。因此，请记住，在现实生活中，您要么使用无服务器功能，要么让上传 API 与后端的其余部分一起运行。

## 设置 Express 服务器

1.我们安装必要的依赖项:

```
$ npm i express body-parser cors dotenv
```

2.在项目的根目录下，我们创建了一个`api/`文件夹，并在其中创建了一个`index.js`文件，我们在其中设置了 Express 服务器:

现在，我们希望我们的前端和 API 同时运行，同时仍然有一个命令要运行。为此，在我们的`package.json`文件中，我们这样更新我们的`start`脚本:

```
"scripts": {     
    "start": "(cd api && node index.js) & react-scripts start",     
    ... 
}
```

顺便说一下，如果你想了解更多关于如何同时运行多个命令的信息，我发现这篇文章很有用。

## 添加上传端点

我们创建一个能够接收 POST 请求的`/api/upload`端点。我们将使用 [Multer](https://www.npmjs.com/package/multer) ，这是一个 Node.js 中间件，允许我们处理`multipart/form-data`:

```
$ npm i multer
```

Multer 向包含上传文件的`request`对象添加一个`file`(如果发送多个文件，则为`files`)。它还向`request`添加了一个`body`对象，该对象包含您想要添加到请求中的任何其他文本字段。

因此，感谢 Multer，我们将能够通过`req.file`访问我们上传的文件，并从`req.body`访问我们剩余的数据。

在`index.js`中，我们创建了一个 Multer 实例:

```
const multer = require('multer');
```

要将我们的文件发送到 Firebase，我们需要用它创建一个`Buffer`对象。我们将使用 Multer 提供的内存存储引擎:

当使用内存存储时，我们的`req.file`将包含一个名为`buffer`的字段，该字段包含整个文件。

以下是该端点的起始代码:

我们已经成功了一半。现在剩下的就是连接到 Firebase 云存储并处理我们的文件，因为服务需要正确地上传它。

## 连接到 Firebase 云存储

1.  首先，我们需要添加`@google-cloud/storage`依赖项来连接到 Firebase 云存储:

```
$ npm i @google-cloud/storage
```

2.接下来，我们需要三个环境变量。这就是我们一开始安装的`[dotenv](https://www.npmjs.com/package/dotenv)`模块发挥作用的地方。它将环境变量从`.env`文件加载到`process.env`。

在项目的根目录下创建一个`.env`文件，并在其中添加两个环境变量:

*   这是您的 Firebase 项目 ID。你可以在你的 Firebase 账号>设置>通用设置里找到。
*   `GCLOUD_APPLICATION_CREDENTIALS`:还记得当初你存放在某处的私钥吗？这是我们需要它的地方。您需要在这里指明您的私钥 JSON 文件的路径。因此，它可能类似于`/api/services/myprivatekey.json`。
*   `GCLOUD_STORAGE_BUCKET_URL`:最后，您需要您的存储桶的 URL，即`[YOUR_GCLOUD_PROJECT_ID].appspot.com`。如果你不确定，你可以在你的 Firebase 账户>存储>文件标签(这是默认的)>中看到，就在左侧存储文件列表的上方，你有一个类似`gs://you-project-id.appspot.com`的网址。这就是了。

最后，确保您将新的`.env`文件添加到您的`.gitignore`中。

3.现在我们可以用我们的 Firebase 凭证启动一个`Storage`实例:

```
const { Storage } = require('@google-cloud/storage');const storage = new Storage({
    projectId: process.env.GCLOUD_PROJECT_ID,
    keyFilename: process.env.GCLOUD_APPLICATION_CREDENTIALS,
});
```

4.并创建一个`bucket`，它是对象(文件)的容器，我们将它与我们的 Firebase 存储桶相关联。我们将在下面的端点中使用它来处理我们的文件。

```
const bucket =
    storage.bucket(process.env.GCLOUD_STORAGE_BUCKET_URL);
```

5.接下来，我们继续进入我们的`app.post('api/upload')`端点。检查后，我们确实有一个现有的`req.file`。如果您记录它，您会看到它包含以下内容:

```
{
  fieldname: 'image',
  originalname: 'Medium Post Cover 3.png',
  encoding: '7bit',
  mimetype: 'image/png',
  buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 0b 22 00 00 07 6c 08 06 00 00 00 e9 93 d9 14 00 00 00 04 67 41 4d 41 00 00 b1 8f 0b fc 61 05 00 ... 1834232 more bytes>,
  size: 1834282
}
```

Firebase 使用的是`Blobs`(二进制大对象)，这是一种可以在数据库中存储二进制数据的数据类型。

因此，我们用`file()`方法在我们的桶中创建新的 blob，将我们的文件名作为引用传递给它:

```
const blob = bucket.file(req.file.originalname);
```

然后我们启动一个*可写流*。FreeCodeCamp 写了一个关于 Node.js streams 的很棒的指南，其中他们回答了你可能会有的关于他们的所有问题(我有很多)。

*流*是一组数据(比如数组或字符串)的集合，这些数据可能不会一下子全部可用。它们允许我们处理大量的数据(如图像或视频)，我们需要一次处理一大块。

一个*可写的流*是一种抽象的流，数据可以被写到*目的地*，所以基本上，当我们想要写数据时，我们使用一个可写的流，这就是我们在这里想要的。

```
const blobStream = blob.createWriteStream({
    metadata: {
        contentType: req.file.mimetype,
    },
});
```

**重要提示**:您需要将文件 mimetype 作为元数据传递给`createWriteStream()`，否则您的文件将不会以正确的格式存储，并且不可读。

这将返回一个`WriteStream`对象，我们可以在其上检查事件。在`finish`事件中，我们组装新存储文件的公共 URL，并在响应中发送给用户，让用户要么在前面显示文件，要么在数据库中存储位置字符串。

注意，我们在`blob.name`上使用了`encodeURI`来涵盖文件名包含空格或其他需要编码的字符的情况。

```
**// If there's an error**
blobStream.on('error', (err) => next(err));**// If all is good and done** blobStream.on('finish', () => { **// Assemble the file public URL**const publicUrl =
`https://firebasestorage.googleapis.com/v0/b/${bucket.name}/o/${encodeURI(blob.name)}?alt=media`;**// Return the file name and its public URL
    // for you to store in your own database** res.status(200).send({ 
        fileName: req.file.originalname,
        fileLocation: publicUrl
    });
});**// When there is no more data to be consumed from the stream the end event gets emitted**
blobStream.end(req.file.buffer);
```

我们做到了！我们的 API 已经可以使用了。以下是完整的`index.js`文件的要点:

index.js |文件上传 API 端点处理上传到 Firebase 云存储

剩下的只是一个简单的前端，带有一个处理文件输入的表单。为此，我们将使用 React。

# 前端的上传处理器

因为我们在这里假设您知道如何设置 React 应用程序和使用钩子，所以我将只关注调用我们的上传端点的表单提交处理程序。但我在这篇文章的结尾提供了完整的项目回购。

我们需要一个`FormData`对象:

```
let fileData = new FormData();
```

我们在我们的`FormData`对象上设置了`image`字段，并添加了选中的文件及其名称。这里有两点需要注意:

首先，字段名称必须与我们在 API 中期望的名称相同:`app.post('/api/upload', uploader.single(<FIELD_NAME>), …)`。

其次，请注意，我们使用`Date.now()`将当前时间戳添加到文件名中。这可以确保文件在存储中具有唯一的名称，从而防止以后被覆盖。

一方面，这允许用户多次上传同一个文件，这可能是你想要的，也可能是你不想要的。另一方面，如果您需要设置 auth 来读取图像，它可以防止潜在的问题。

事实上，Firebase 会为每个文件生成一个访问令牌，如果用户上传相同的图片，它会替换之前的图片并更新访问令牌，这将导致使用之前令牌的图像在某个地方损坏。

```
fileData.set('image', selectedFile, `${Date.now()}-${selectedFile.name}`
);
```

最后，我使用`axios`进行 API 调用:

我们将所有这些都放在一个尝试/捕捉中:

这就是它的全部！

# 结论

您可以在完整的项目报告中通过加载和错误处理来检查完整的 React 组件(我保持它的简单，只有一个主要的`App`组件):

[](https://github.com/clairechabas/file-uploader-firebase-storage-react-node) [## claire chabas/文件-上传程序-firebase-存储-反应-节点

### 要使用这个文件上传器，你需要:设置一个 Firebase 帐户>激活云存储>生成一个…

github.com](https://github.com/clairechabas/file-uploader-firebase-storage-react-node) 

我希望这篇文章是有帮助的。如果你有任何问题或改进建议，我很乐意在评论中看到。

快乐编码。