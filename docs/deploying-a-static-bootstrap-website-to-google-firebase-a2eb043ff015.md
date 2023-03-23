# 将静态引导网站部署到 Google Firebase

> 原文：<https://betterprogramming.pub/deploying-a-static-bootstrap-website-to-google-firebase-a2eb043ff015>

## 快速轻松地构建和部署网站

![](img/3e8f9a6cd02213a457f396e3eb84f880.png)

# 开始工作需要什么

*   一个激活的[谷歌云平台](https://console.cloud.google.com/)账号
*   [谷歌云 SDK](https://cloud.google.com/sdk/install)

如果你以前做过一些 HTML + CSS 网页，或者打算这样做，你只能通过将你的项目文件上传到一个托管源来展示你的作品，在那里你的网站可以通过互联网访问。在过去的几十年里，这是一项痛苦的任务。然而，今天变得容易多了。随着所有云技术越来越受欢迎，服务价格越来越便宜，在一些工具如谷歌 Firebase Hosting 的帮助下，你今天基本上可以免费托管你的网站。

你可以自由选择任何静态网站上传到 Firebase 主机。如果您已经有一个项目准备上传到云，请随意跳过下面的第一步。

# 1.从模板创建引导网站

在本教程中，我们将使用一个引导模板。首先，让我们从您最喜欢的引导模板来源获得一个引导项目。我喜欢用[启动自举](https://startbootstrap.com/templates/)。对于我的项目，为了简单起见，我将使用一个简单的模板。一旦你选择了你的，下载到一个已知的文件夹，然后编辑你的`index.html`来个性化你的页面。最后，在浏览器中打开您的`index.html`页面，查看您的更改。下面是我下载的添加一些改动前后的模板。

![](img/d838f9a7f480b4d5d154221d05a40517.png)

# 2.创建 Firebase 应用程序

导航到 [Firebase 控制台](https://console.firebase.google.com/)并创建一个新项目。下面是代表该过程每一步的一系列截图。您可以自由启用/禁用 Google Analytics，但这不是必须的。

![](img/793492202f9fa36a0808ac9c58998333.png)![](img/29d5c4d73fa3790d6b56e9bd497e1f1e.png)![](img/95ee62b4e484c396500d3869eba73783.png)![](img/9d4301449ce0e9356507375fe56e0dcd.png)![](img/ab44fe8262705865baa245e86397a548.png)![](img/7ecd6ee75f7ea9d93bf32703f2858843.png)![](img/561d590a85ed6db8378d763f09eafac2.png)![](img/43e5e6d3dbd6a1535d8c3303367e12e8.png)![](img/e2d2420b7b9afbfe917852755dac5fbd.png)

# 3.准备项目进行部署

在使用 CLI 部署静态网站之前，我们需要有一个默认的公共目录，其中包含静态文件。因此，在您的项目目录中，将您的所有网站文件移动到一个名为 *public* 的新文件夹中。按照下面的截图寻求帮助。

![](img/bb8c7ea3d1f912456c1015dc39b07931.png)

在部署项目之前，我们需要对项目进行的最后一项更改是加入 Firebase SDK。创建 Firebase 应用程序后，您将看到一个代码片段(见下面的截图)。这是允许你的静态网站与 Firebase 交互的原因。复制代码块并将其添加到您的`index.html`的底部。

![](img/c44c7dcf7b7469cbbc870af08fd63046.png)

# 4.使用 Firebase CLI

如果你没有安装 firebase，使用这个[链接](https://firebase.google.com/docs/cli)或者这个`npm`命令:

```
npm install -g firebase-tools
```

现在，使用终端，确保您在项目的目录中，并按顺序执行这三个命令。按照截图进行详细的步骤。

```
firebase login
```

![](img/f7b17728186b9738d28ad52d57bf5511.png)

```
firebase init
```

![](img/d84e54d031b6bad652e6a2212c707c37.png)![](img/93a0bde39abb9e8b96142f3077d86f91.png)

```
firebase deploy
```

![](img/85eaa1c666c8ed6da34d20e10c8716f1.png)

访问显示在终端底部的托管 URL，您应该会看到您部署的静态网站。这里是我的。你也可以在 [Firebase 控制台](https://console.firebase.google.com/)中找到更多关于你的主机的信息。你成功了！🎉

![](img/1c83b80d5c128001c7a3617cc727af6e.png)

作为可选的清理任务，将`.firebase`*`.firebaserc`*和`firebase.json`添加到您的`.gitignore`中。**

**查看 [Github repo](https://github.com/Marwan01/bootstrap-firebase) 获取本文使用的源代码。**