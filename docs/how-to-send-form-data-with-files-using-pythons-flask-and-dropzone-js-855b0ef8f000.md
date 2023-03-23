# 如何使用 Python 的 Flask 和 Dropzone.js 发送带有文件的表单数据

> 原文：<https://betterprogramming.pub/how-to-send-form-data-with-files-using-pythons-flask-and-dropzone-js-855b0ef8f000>

## 用 web 应用程序传递多个文件

![](img/4bf0afdb1a1fe1e2bd97c3dc7300b506.png)

[斯科特·格雷厄姆](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

[Dropzone.js](https://www.dropzonejs.com/) 是一个非常酷的 JavaScript 库，你可以通过拖拽文件到一个区域来立即上传文件。然后 Dropzone 完成剩下的工作，把你的文件发送到你指定的地方保存。您可以删除多个文件或单个文件。Flask 是一个 WSGI web 应用程序框架，使构建 web 应用程序变得容易。如果你想使用基本的 Dropzone，有一个很棒的教程叫做“[用 Flask](https://blog.miguelgrinberg.com/post/handling-file-uploads-with-flask) 处理文件上传。”

但是，如果您想将 Dropzone 添加到现有表单中，并通过 Flask 后端接收文件和表单数据，该怎么办呢？这是一个我在网上看到被问了不止一次的问题，所以这里有一个简单的方法来使用 Flask 和 Dropzone 发送多个文件和表单数据，只需点击一个“提交”按钮。

# 为什么要一起发送文件和表单数据？

您可能希望同时发送文件和表单数据有几个原因:

*   如果您想要将正在收集的文件发送到 API，您可能需要收集 API 密钥、令牌或类似的东西来完成上传。
*   如果您希望将文件与某个人或有关文件的数据相关联，您可能需要收集表单数据来实现这一目的。
*   如果您希望允许其他人在发送时修改他们的文件信息。例如，您可能想让他们有机会在发送文件时更改文件名。

# 先决条件

你需要准备一些东西来开始:

*   Python 已安装。
*   项目的虚拟环境(可选，但推荐)。
*   烧瓶安装完毕。
*   请求已安装。
*   参见 GitHub 上的项目[。](https://github.com/erikkai/flask_and_dropzone/tree/main)

*注:本项目基于* [*米格尔·格林伯格的烧瓶和 Dropzone 教程*](https://blog.miguelgrinberg.com/post/handling-file-uploads-with-flask) *。*

# HTML 文件里有什么？

为了简单起见，我创建了一个简单的表单。让我们来看看 HTML 的不同部分。

## 表单

我包含了这个片段以供参考。如果您想一起查看所有内容，请从 [GitHub](https://github.com/erikkai/flask_and_dropzone) 中抓取代码示例。您可以在正文标签之间看到，表单正在做一些事情:

1.  `form action=“/”` 当您点击“提交”按钮时，会告诉您想要将信息发送到哪里。您也可以通过 Dropzone 配置包含此信息。我在这里描述它，因为一个容易犯的错误是假设 Dropzone 自动知道将所有东西发送到哪里。如果您指定使用 Dropzone，或者，正如我所选择的，使用表单操作，就会出现这种情况。
2.  `class=“dropzone”`让 Dropzone 知道您想在哪里放置一个区域。
3.  `id=“my-dropzone”` 是您需要引用表单并添加您的配置选项的 Dropzone。
4.  `method=“POST”` 明确表示希望`POST`形成数据和文件。如果您不选择该选项，它会尝试用`GET`发送您的信息，这意味着该文件将被忽略。

表单的其余部分只是设置“提交”按钮和您想要的字段。如果您想知道多部分设置在哪里，您不需要它，因为您正在使用 Dropzone。根据您决定创建的内容，您可能需要添加它。

## 属国

在`head`标签之间，您可以看到您需要的所有不同的脚本和样式表。Dropzone 很灵活，所以如果你想用不同的方式做事，你可以。您也可以将 Dropzone `.js`和`.css`文件放入一个静态文件夹中，以便 Flask 查找。我选择用 cdn 代替它们。

## Dropzone 配置脚本

您可以将 Dropzone 的配置脚本放在几个地方，包括在您引用的单独文件中。我选择使用脚本标签将它添加到 HTML 文件的依赖项下。配置如下所示:

要设置，您有`myDropzone`。这是对您的表单的引用，在该表单中，您将 id 放置为“my-dropzone”。无论您选择什么名字，您都要根据破折号的位置将名字放在 camel 大小写中。所以如果你有“我的-惊人的-dropzone”，它将是`myAmazingDropzone`。对于这些选项，您可以设置:

*   `autoProcessQueue`至`false`。这告诉 Dropzone 不要自动抓取您的文件并将其发送出去。
*   `uploadMultiple`到`true`。如果你想一次拖放一堆文件，你需要将这个设置为`true`。
*   `parallelUploads`到`10`,因为我认为用十个来测试对这个演练来说可能已经足够了。这是同时尝试上传的文件数量。
*   `maxFiles`表示您希望某人一次能放下多少文件。我又放`10`了。

接下来，我们向“提交”按钮添加一个事件监听器。我们可以通过按钮的类型来引用它，所以不需要 ID。然后我们告诉 Dropzone 在点击“提交”按钮时上传所有内容。表格到此为止。接下来我们一起去看看`app.py`吧。

# app.py 文件里有什么？

`app.py`的代码看起来是这样的(完整代码，请访问 [GitHub](https://github.com/erikkai/flask_and_dropzone/blob/main/app.py) 上的示例):

你可以在格林伯格的教程中阅读关于这段代码的详细讨论。我在这里所做的更改是:

*   制作这个关于视频的样本。对于上传扩展，我只允许上传视频。您可以将其更改为文本文件或图像。或者，如果你根本不担心保护你的文件，你可以摆脱上传扩展配置。
*   使得处理表单数据和文件成为可能。需要注意的重要一点是，您的文件和表单位于请求信息的两个不同部分。`request.form`将包含您添加的所有字段，而`request.files`将包含您的文件信息。文件在一个不可变的多字典中。
*   保留`files`对象。您可以看到，我只是更新了项目中的文件名，而不是从`my_files`中的项目中取出文件名。
*   添加一个点，在这里我获取表单数据并将其写入文本文件。你可以做很多其他的事情，比如添加一个数据库或者以其他方式处理这些信息。这只是提供一个简单的例子。顺便说一下，在这个例子中，所有文件都存储在同一个目录中。

如果你正在处理图像并且想要缩略图，你可能想要浏览格林伯格的教程，因为他提供了一种方法。如果您想了解代码片段中的各种安全特性，这也是一个有用的教程。

根据文件的大小，您可能想要更改`MAX_CONTENT_LENGTH`配置以接受更大的文件，而不仅仅是`1024 * 1024`。

您还可以在 [Dropzone.js](https://www.dropzonejs.com/) 网站上找到更多 Dropzone 信息。我希望这有助于您开始使用 Dropzone 处理多个文件和表单数据！