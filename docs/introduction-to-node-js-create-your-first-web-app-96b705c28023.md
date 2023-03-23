# Node.js 简介—创建您的第一个 Web 应用程序

> 原文：<https://betterprogramming.pub/introduction-to-node-js-create-your-first-web-app-96b705c28023>

## 你好世界

![](img/799bd9626fe83aff55b1f9e3f3c03ca7.png)

尼古拉斯·皮卡德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[Node.js](https://nodejs.org/) 是一个用 JavaScript 运行服务器端应用的运行时平台。它采用 V8 浏览器引擎来解释服务器上的 JavaScript 代码。它在标准库中自带一组内置模块，用于运行服务器端应用程序，并允许直接与硬件交互。

它可以让我们在电脑上操作文件和做许多事情。它不同于浏览器中的 JavaScript，因为它做完全不同的事情。Node.js 可以做的事情之一是运行 web 服务器来提供静态和动态内容。

在本文中，我们将通过安装 Node.js 运行时平台来熟悉 Node.js，并使用它构建简单的 web 应用程序。我们从下载 Node.js 运行时开始。

要在 Windows 上做到这一点，我们转到[https://nodejs.org/en/](https://nodejs.org/en/)并单击 LTS 链接下载 Node.js 运行时，然后双击下载的可执行文件并按照说明进行操作。在 Linux 中，我们可以使用包管理器来下载 Node.js。

每个发行版都有不同的方式来下载 Node.js 运行时。例如，在 Ubuntu 中，我们运行以下命令来下载 Node.js:

```
$ sudo apt-get install curl
$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
$ sudo apt-get install nodejs
```

当我们运行上面的命令时，我们下载 Node.js LTS 版本并安装它。然后，我们可以运行:

```
$ node -v
```

检查安装的 Node.js 版本。

安装 Node.js 运行时后，我们必须学习如何使用终端。

在 Windows 上，我们将使用 Node.js 命令提示符，它是在按照上面的说明安装 Node.js 时安装的。特殊的命令提示符已经为我们设置了 Node.js 环境，而普通的命令提示符则没有。

因此，我们需要 Node.js 命令提示符来运行 Node.js 应用程序。Windows 10 也有针对 Linux 的 Windows 子系统，它为我们提供了不同风格的 Linux 作为命令提示程序，让我们在 Windows 中完成许多可以在 Linux 上完成的事情。

要安装它，我们必须进入开始菜单并搜索*打开和关闭 Windows 功能*，然后选择*Linux 的 Windows 子系统*并单击*确定*。之后，重启电脑。

然后，重新启动后，去 Windows 应用商店搜索你想要的 Linux 版本。最常见的是 Ubuntu，所以我们可以搜索并点击*安装*。

之后，打开已经安装好的 Ubuntu 命令提示符程序，让它运行，然后它会提示你通过设置登录命令提示符的用户名和密码来创建一个帐户。

有了用于 Linux 的 Windows 子系统，磁盘存储与 Windows 共享，因此我们不必担心移动文件。

但是，Linux 的 Windows 子系统版本 1 的磁盘操作很慢。版本 2 更快，将随着 Windows 10 的最新更新而推出。

对于 macOS 和 Linux 用户，他们有终端程序，让我们在命令提示符下运行命令。运行 Node.js 应用程序不需要特殊的命令提示符程序。它们都可以运行 POSIX 兼容的程序。

对于所有用户来说，另一种方法是使用虚拟机在您选择的操作系统上运行 Node.js。

一个流行的选择是使用 [VirtualBox](https://www.virtualbox.org/) ，它支持许多常见的操作系统，如 Windows 和许多版本的 Linux。它是免费的，并且得到了一个大社区的支持。

在 VirtualBox 虚拟机上安装新的操作系统很容易，并且有现成的虚拟机可供下载。比如 https://www.osboxes.org/[有很多为 VirtualBox 做的 Linux 虚拟机。](https://www.osboxes.org/)

虚拟机使用自己的存储并与主机共享内存，因此虚拟机将占用您在设置虚拟机时留出的大量内存。

如果你不喜欢虚拟机与你自己的键盘交互的方式，你可以通过使用 PuTTY 这样的终端程序来连接它，从而改善体验。

但是，在这样做之前，您必须启用您的虚拟机。例如，要启用从 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 到 VirtualBox 虚拟机的登录，我们必须执行以下步骤:

*   运行`sudo apt install ssh`来安装 SSH 程序。
*   关闭虚拟机。
*   在 VirtualBox 中，进入*设置*菜单，*网络*部分。在*适配器 1* 上选择*主机专用适配器*，然后点击*确定*。
*   启动 VirtualBox 虚拟机，登录并运行`ifconfig`来获取虚拟机的 IP 地址。
*   然后，您可以使用它登录到您选择的终端程序，比如 PuTTY。

在你的虚拟机或者电脑上设置好 Node.js 之后，我们就可以开始编写 Node.js 程序了。首先，我们必须使用文本编辑器来编写程序，因为我们需要一个只保存纯文本的程序，这就是代码。

比如在 Windows 中， [Visual Studio 代码](https://code.visualstudio.com/)是编写 JavaScript 代码的绝佳选择。它有代码和模块的自动补全功能，您可以尝试键入代码来引用它们。

此外，它速度快，重量轻。它只需要 250MB 的磁盘空间，需要 1GB 的内存和 1.6 GHz 的处理器。

这些需求与其他功能丰富的文本编辑器相当。它还具有缩放设置，分割单个文件的视图，识别不同类型的文件，以便它们可以被正确地高亮显示以便于阅读，并且它有许多插件来扩展其功能。

它还与计算机操作系统的命令提示符或外壳相集成，因此您可以直接在其中运行命令。

JavaScript 支持是内置的，所以我们可以自动为 JavaScript 代码突出显示语法、自动完成和捕捉语法错误。这对于提高用 JavaScript 编程的生产率非常有帮助，因为它有助于更快地读写代码。它也是用 JavaScript 构建的。

为了制作有用的程序，我们必须使用其他库来帮助我们实现这一点，因为我们不能自己编写所有的东西。Node.js 有一个标准库，可以让我们做很多事情，比如运行 web 服务器和操作存储在计算机上的文件。

要做标准库中没有的事情，我们可以安装 Node.js 包，并在您的应用程序代码中导入或要求它们。库存储在包存储库中，可以由包管理器下载。

对于 Node.js 生态系统，最流行的包存储库是节点包管理器或 [npm](https://www.npmjs.com/) 。Npm 是 Node.js 包的存储库，我们可以用`npm`程序下载它们。

npm 程序有许多命令可以让我们轻松下载 Node.js 包。您安装的软件包的详细信息存储在您所在的项目文件夹的`package.json`中。

`package.json`也可以在`package.json`的`scripts`部分存储脚本命令。

此外，为了防止包版本的改变和导致问题，创建了一个`package-lock.json`，指示当你安装一个包时安装的版本。如果您使用版本控制，您将签入这两个文件，以便在您再次设置应用程序时安装正确的包。

Npm 有许多命令来处理节点包。最常见的如下。

## npm 安装

`npm install`，简称`npm i`，用于安装包。

如果您没有在`install`后面加上一个包名，如果依赖项还没有安装或者安装的版本已经过期，它会下载`package.json`中列出的所有包。

如果你在`npm install`后面放一个包名，只要找到这个包，它就会用这个名字安装这个包。在安装过程中，`npm`会运行`npm run link`和`npm run build`来编译软件包。

运行`npm install -g packageName`可以全局安装包。

## `npm prune`

`npm prune`移除未使用的包装。`npm run --production`用于从`devDependencies`部分删除包。`--dry-run`选项用于在实际运行前检查哪些包将被删除。

`--json`选项可用于在 JSON 中显示结果。启用包锁定后，修剪是自动进行的。

## `npm run`

`npm run-script`让您运行自己编写的自定义脚本。`npm run`是该命令的别名。

## `npm start`

`npm start`通过运行您定义的命令启动软件包。

## `npm update`

`npm update`更新项目中的包。`npm update packageName`只更新名为`packageName`的包。它会将最新版本记录到`package-lock.json`。

要安装 npm 或将 npm 升级到最新版本，请运行`npm install -g npm`。

# 创建 Web 服务器

现在我们已经拥有了编写基本 web 服务器所需的一切。它所做的只是通过监听给定的端口来服务于“Hello World”响应。我们可以通过使用给定的端口访问本地主机来查看消息。

web 服务器所做的是，当给定您放入浏览器或 HTTP 客户端的 URL 时，它获取您发出的请求，包括 URL、标题、cookies 和请求正文，然后服务器将根据接受的请求做一些事情。

web 服务器将获得请求数据，如 URL、请求方法(get、POST、PATCH、PUT、DELETE)、头、cookies 和请求体，并根据请求中的数据做出响应。

例如，如果我们转到 http://localhost:9999/helloworld，那么监听端口 9999 的 web 服务器将获得请求 URL，然后如果 URL 是`helloworld`，在本例中就是这样，它将返回纯文本响应“Hello world”。

响应可以有自己的头，如文件类型和正文数据。它可以是不同的格式，如纯文本或 JSON，这是一种格式类似于 JavaScript 文字的纯文本。

此外，响应将有一个响应代码。响应代码的范围从 100 到 500。常见的包括 200 表示一般的成功响应，201 表示创建的新数据，204 表示没有内容的响应。

300 系列响应用于重定向响应。301 代表永久移动。302 意味着找到了资源。400 系列错误是针对客户端的错误。

例如，400 是向服务器提交错误数据时的一般错误请求响应。401 表示当用户无法使用正确的凭据登录时的未授权响应。

通常，当试图访问不允许用户访问的资源时，会返回 403。404 是资源未找到响应。

405 表示使用未经授权的 HTTP 方法发出请求。422 是另一个因发送错误数据而返回的响应，不能被服务器处理。

500 系列错误是服务器端错误。500 表示一般服务器错误。502 表示坏网关，503 表示服务不可用，504 表示网关超时。

现在我们可以用 Node.js 编写我们的 web 服务器了。

我们将用两种方法来做这件事。我们将使用 Node.js 内置的`http`模块和非常流行的 [Express](https://expressjs.com/) web 框架来构建我们的 web 服务器应用程序。

用`http`模块构建的看起来如下:

```
const http = require("http");
http
  .createServer((request, response) => {
    response.writeHead(200, { "Content-Type": "text/plain" });
    response.write("Hello, World!");
    response.end();
  })
  .listen(8888);
console.log("Server listening on port 8888");
```

在上面的代码中，我们首先用`const http = require(“http”);`行导入`http`模块。然后，我们使用`createServer`函数创建 HTTP 服务器。

然后，我们传入一个回调函数，它带有一个`request`和`response`参数。如果请求被服务器接受，这对于处理数据是很方便的。对于这个简单的“Hello World”示例，我们只返回响应。

我们使用在`response`对象中可用的`writeHead`函数设置头，并设置响应代码(对于成功的响应为 200)和`Content-Type`响应头，响应头被设置为`text/plain`，因为我们想要返回纯文本。

然后，我们运行带有`“Hello, World!”`字符串的`response.write`函数，在您选择的 HTTP 客户端的浏览器中显示“Hello world”。调用`response.end()`会将响应返回给 HTTP 客户端的浏览器。

我们可以通过将上面的代码保存在你选择的目录下的一个名为`app.js`的文件中，然后在进入你保存文件的目录后运行`node app.js`来运行这个应用程序。之后，我们应该看到:

![](img/93455aba17a8996ef2d753804e36de9d.png)

上面的代码适用于非常简单的应用程序，如 Hello World 应用程序。然而，当一个应用程序有更多的功能时，使用`http`模块编写它们将是一件痛苦的事情。

我们必须检查侦听器中的请求和响应，然后我们检查我们希望用户访问的 URL，然后根据传入`request`对象的内容操纵响应并返回它。

这将使创建复杂的应用程序变得困难，因为在这些应用程序中，你需要用请求中不同的标题和正文来检查大量的 URL。

因此，我们可以使用一个框架来简化这一点。Express 框架非常简单易用，让我们可以简化 Hello World 应用程序。

要使用 Express 构建我们的应用程序，我们可以创建一个文件夹，然后进入该文件夹并运行`npm init -y`。

这将创建一个空的`package.json`文件，将该文件夹指定为 Node.js 项目文件夹。运行`npm i express`安装快速框架。在同一个文件夹中创建`app.js`并添加:

```
const express = require("express");
const app = express();
const port = 9999;app.get("/", (req, res) => {
  res.send("Hello, World!");
});app.listen(port, function() {
  console.log(`Server listening on port ${port}`);
});
```

我们可以通过在您创建的文件夹中运行`node app.js`并转到 [http://localhost:9999，](http://localhost:9999,)来运行它，在那里您会看到与上面相同的内容。

它只在你去 http://localhost:9999 的时候显示“Hello World”。像 http://localhost:9999/abc 这样的其他 URL 不起作用，因为我们只指定了根 URL(用`“/”`表示)将返回“Hello World！”

这是我们在使用`http`模块的 web 服务器中所没有的。

Node.js 是一个运行时平台，有一个很好的生态系统。它采用 V8 浏览器引擎来解释服务器上的 JavaScript 代码。

它在标准库中自带一组内置模块，用于运行服务器端应用程序，并允许直接与硬件交互。

它让我们在电脑上操作文件和做许多事情。它不同于浏览器中的 JavaScript，因为它做完全不同的事情。

创建一个简单的 web 服务器可以简单地通过内置于 Node.js 标准库中的`http`模块来完成，或者我们可以使用 Express 框架来获得更高级的功能。