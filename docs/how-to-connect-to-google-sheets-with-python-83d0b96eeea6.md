# 如何用 Python 连接到 Google Sheets

> 原文：<https://betterprogramming.pub/how-to-connect-to-google-sheets-with-python-83d0b96eeea6>

## 利用 Google Sheets 作为应用程序数据存储的一部分

![](img/6aeb71a319464624529aa5765409c951.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

最近，我与一位客户合作，帮助解析数百个 PDF 文件，为它们分配关键字，以便它们以后可以被搜索到。

挑战的一部分是知道从哪里下载哪些文件。客户端将每个 PDF 文件存储在 AWS 上，它们的特定 URL 存储在 Google Sheets 的单元格中。

现在，我喜欢 Python 编程语言。它很容易阅读，并且有大量的库，允许你用它做任何事情。它是你多功能腰带中最完美、最通用的工具。简而言之，Python 让你成为蝙蝠侠。

接下来是一个教程，教你如何使用 Python 来连接自己选择的 Google 电子表格，以便从所述表格中检索信息。

# **设置**

所以为了这个，我用了 Python 3.6.3。你可以使用任何你喜欢的版本。据我所知，Python 2.7 也支持连接 Google Sheets 的相关库( [gspread](http://gspread.readthedocs.io) )。

对于设置，您可以首先进入您的终端(假设您在 OSX)并键入以下内容:

```
pip install gspread
```

这将下载连接到 Google sheets 所需的库。

然后，您需要从 [Google 的 API 服务获取您的凭证。](https://console.developers.google.com/apis/library)在这里，您应该搜索“Google Drive”并执行以下操作:

1.  点击结果。
2.  接受服务条款。
3.  创建一个项目。
4.  创建项目后，单击 Enable。

完成后，请遵循以下步骤:

1.  单击左侧导航栏上的“创建凭据”按钮。
2.  单击新建创建凭据按钮。
3.  选择服务帐户密钥选项。
4.  在服务帐户下，选择新服务帐户。
5.  给它起个名字。
6.  选择项目>角色中的所有者。
7.  点击 JSON 并下载文件。

注意:该文件包含您访问该项目的特定凭证，并且是唯一的副本。请妥善保管此文件。对于这个项目来说，最简单的做法是将这个 JSON 文件存储在与您的代码相同的目录中。

打开 JSON 文件(可以用自己喜欢的文本编辑器)，复制信息后跟`client_email:`。

这一点很重要:使用 Python 代码访问您希望访问的电子表格，并将其与您从`client_email:`复制的电子邮件共享。这很重要，因为您需要让您的 API 凭证访问您的电子表格。

使用以下命令启动您最喜欢的代码编辑器:

现在，您可以访问您的电子表格，并且可以使用 gspread 库中的函数来操纵进出 Python 的信息。从 [GitHub 库](https://github.com/burnash/gspread)中获取特定的命令来使用这个库。

如果您有任何问题、评论、错误或批评，请告诉我！

玩得开心成为蝙蝠侠与蟒蛇。