# My Node.js，MongoDB Atlas REST API 环境设置

> 原文：<https://betterprogramming.pub/my-node-js-mongodb-atlas-rest-api-environment-setup-71923cfbc98c>

## *确保您拥有成功开发节点 API 所需工具的初级读本*

![](img/126b1432b04bb7b9dcef35da01f2633f.png)

由[蒂姆·莫斯霍尔德](https://unsplash.com/@timmossholder?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/learn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

目的:设置好本地开发环境后启动一个简单的 Node.js 应用，通过 [Postman](https://www.getpostman.com/) 测试一个 API，建立到 [MongoDB Atlas](https://docs.atlas.mongodb.com/api/) 的数据库连接。

这些是您将在本教程中安装的工具:

*   Visual Studio 代码(VS 代码):这当然不是创建 Node.js 应用程序的要求，但它是我推荐的文本编辑器。我相信它使软件开发变得更加愉快和高效。
*   Git Bash : Git 是一个非常流行的管理源代码的工具，尤其是当你在一个大型开发团队中工作的时候。VS 代码允许您集成 Git Bash 终端，这使得将您的代码推送到 Git 存储库非常方便。此外，它允许您在模拟 Bash 环境时使用大多数标准的 UNIX 命令。
*   node . js:JavaScript 运行时环境。
*   npm:节点包管理器。这与 Node 一起使用，以便我们可以轻松地共享打包的代码模块。
*   Postman:一个 API 客户端工具，我们将用它来测试 REST 服务。
*   MongoDB Atlas account:一个基于云的数据库，由给你带来 MongoDB 的同一批人创建。

# 下载 VS 代码

从[https://code.visualstudio.com/](https://code.visualstudio.com/)下载稳定版本。

这是一个非常简单的下载。微软在简化这个应用程序的安装过程方面做得很好。

# 在 VSCode 中使用 Git Bash

为你的特定操作系统从 https://git-scm.com/downloads 下载 Git Bash。

当你点击 Git 安装程序时，我建议使用所有默认的安装设置，除非你真的知道你在做什么。

我们现在将 Git Bash 作为集成终端添加到 VS 代码中。

*   在 VS 代码中打开一个新的终端(Ctrl + Shift +`)或者终端→新终端。
*   打开命令选项板(Ctrl + Shift + P)或视图→命令选项板。
*   键入“终端:选择默认外壳”。
*   您应该会看到下面的选项:

![](img/c259030f8d5b9ebca9f9727434c6cab7.png)

*   选择 Git Bash。
*   在终端窗口中选择+按钮。

![](img/7175f28d372d830828f320823bcfaaa1.png)

您应该会看到类似这样的内容。

检查点 *:* 输入下面的命令，确保已经正确安装了 Git。

```
git --version
```

![](img/c51c736c6c50b87e02f2009e9a90b6d8.png)

根据您安装的版本，这应该会出现。

# 安装 Node.js

点击此链接下载:[https://nodejs.org/en/](https://nodejs.org/en/)。

我选择了“推荐给大多数用户”选项，然后使用 Node.js 设置中的所有默认设置。

检查点:安装完成后，在命令行中输入:

```
node -v && npm -v
```

它应该是这样的(你的版本可能比我的更新):

![](img/970bb3663b07587c11a489da0f88fb0d.png)

如果您已经成功安装了 Node 和 npm，请跳到下一节。

如果它没有出现在你的 Git Bash 中，不要烦恼！重新启动计算机。如果这不起作用，请在 Powershell[或 Windows 命令行中键入相同的命令，您应该会看到它。](https://docs.microsoft.com/en-us/powershell/scripting/overview)

为了使用 Git Bash，您需要将它添加到您的路径中。该路径告诉您的操作系统在哪里寻找可执行文件，以响应来自用户的命令。

下面是在 Windows 上如何做到这一点:

*   打开“查看高级系统设置”。
*   单击“环境变量”。
*   在系统变量下，选择路径，然后选择编辑按钮。
*   加上这个`C:\Program Files\nodejs\`。
*   如果上面的文件路径不正确，您可以通过在 Windows 命令行中键入以下命令来轻松找到它。

```
where node
```

*   在检索到节点目录的正确路径并将其添加到 path 系统变量后，选择“确定”。
*   重新启动计算机。

检查点:现在，尝试在 Git Bash 中键入以下命令:

```
node -v && npm -v
```

![](img/f194823f81be0cb08e6ead1d7bc65737.png)

# Node.js Hello World

在计算机上为 Hello World 应用程序创建一个目录。

![](img/f89fa4d94179bc4e30d90a898bf34e47.png)

然后键入以下命令来初始化您的节点项目:

```
npm init
```

继续按回车键回答所有这些问题:

![](img/fe60ee099d6e75082a8892758280e8ad.png)

快速创建新文件:

```
touch app.js
```

将这段代码添加到新创建的`app.js`文件中:

我们在这里做的是指定主机名和端口，然后创建我们自己的 HTTP 服务器。然后，我们发送一个请求，如果成功，我们将看到“Hello World”打印在屏幕上。

关卡:导航到`localhost:3000`，在那里你会看到:

![](img/3efc045fd39bb0f30ead16e1436d583c.png)

# 安装邮递员

为了测试我们的 API，我们需要某种类型的客户端。我们可以每次都通过命令行发送一个`curl`命令，但是这变得很乏味。有一个 GUI(图形用户界面)来简化这个过程是很好的。

但是，如果您想尝试一下，请在命令行中键入以下内容。这假设您仍然有上一步中运行的节点服务器。

```
curl localhost:3000
```

你应该看看这个:

![](img/707e6530c2dc56590ce3942793636f14.png)

但是随着我们项目的增长，这些`curl`命令只会变得越来越复杂。所以，我们用邮递员。

导航到[https://www.getpostman.com/downloads/](https://www.getpostman.com/downloads/)并为您的操作系统选择下载。

你不需要一个帐户就可以开始。只需在“Launchpad”旁边创建一个新标签即可。

输入`localhost:3000`，点击“发送”。

检查点:你的邮递员接口应该是这样的:

![](img/12915d41901746411fa5e2e00b813ce3.png)

# MongoDB 地图集

本部分不需要下载，只需要创建一个帐户。不要担心，我们将保持该帐户的免费等级。

点击[https://www.mongodb.com/](https://www.mongodb.com/download-center)的“免费试用”按钮，并输入一些账户详情。那么我建议使用“启动集群”来保持免费。

然后你将到达这一页。根据您的位置，选择您的位置。

![](img/e175e2aa1d07af511038d2e8314aeab7.png)

确保在创建该群集时，底部显示“FREE”。现在不需要为此付费。然后，给你的新集群几分钟时间来创建。

现在，我们将把我们的 IP 地址列入白名单。这是为了让我们可以从任何地方连接，并成功访问我们的数据。

![](img/c9572c34bf2ea45cbaa35cccde8ab141.png)

点击集群中的“连接”按钮。

![](img/e3f215db3ac0dc46cd9ccdc0311bd9f4.png)

然后选择“添加您当前的 IP 地址”。

![](img/40f99d43d178ef120b57dca5c6621ce1.png)

添加你的 IP 地址，也可以添加 0.0.0.0 从任何地方连接(我不推荐)。

在这里，我们可以创建我们的 MongoDB 用户。这将用于连接 URI，所以我不建议把一个非常安全的密码。

好吧！现在让我们连接到我们的集群。点击“连接您的应用程序”。

![](img/c7e1968396a41b08671a92ba6aa39897.png)

复制这个，因为我们以后会用到它。我们也将用我们刚刚创建的密码替换`<password>`。

回到我们的 Hello World 应用程序，我们将转到命令行，在那里我们将安装 MongoDB 驱动程序。

```
npm i --save mongodb
```

现在，请将下面的代码添加到`app.js`中，这样我们就可以连接到我们的数据库。我把新增的内容用粗体显示。

```
const http = require("http");
const hostname = "127.0.0.1";
const port = 3000;
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader("Content-Type", "text/plain");
  res.end("Hello World\n");
});**const MongoClient = require("mongodb").MongoClient;****const uri = "mongodb+srv://ryan:ryan1234@cluster0-rzvvd.mongodb.net/test?retryWrites=true&w=majority";****MongoClient.connect(uri, {useUnifiedTopology: true } (err, client) => {
  if (err) console.log("Error occurred connecting to MongoDB...");
  console.log("Connected to MongoDB!");
});**server.listen(port, hostname, () => {
  console.log(`Server running at [http://${hostname}:${port}/`);](http://${hostname}:${port}/`);)
});
```

检查点:在重新启动您的节点服务器(`node app.js`)时，您应该会看到下面的控制台输出。

![](img/d92178be13323adfe071886fc2525d66.png)

我们已经成功地建立了与数据库的连接。我们现在准备使用 Node.js 和 MongoDB 构建一个 REST API！