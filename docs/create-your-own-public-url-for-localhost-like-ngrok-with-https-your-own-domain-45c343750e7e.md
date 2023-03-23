# 为您的本地主机创建您自己的公共 URL(类似于 ngrok)

> 原文：<https://betterprogramming.pub/create-your-own-public-url-for-localhost-like-ngrok-with-https-your-own-domain-45c343750e7e>

## 与 HTTPS 和你自己的领域

![](img/a891a1d77f9ca3c9ff1c96297a5f4d04.png)

图像由[描述器](http://descrier.co.uk)描述

有时候，在开发过程中，将您的本地应用程序暴露在互联网上是非常必要的。ngrok 是一个很好的解决方案，但是它有一些限制。每次断开连接并重新连接时，URL 都会改变。像 HTTPS 这样的服务也是有偿的。

您可以使用 SSH 创建一个隧道，这一点您可能已经很熟悉了。

假设您在本地机器的端口`3000`上运行了一个节点应用程序，并且您希望互联网上的任何人都能够访问它。

这可以分两步完成:

1.  在服务器上，创建一个类似于`forward.mydomain.com` 的虚拟主机，用一个反向代理连接到一些未使用的端口(比如说`5000`)。
2.  现在创建一个隧道，这样无论什么从服务器上的端口`5000`进入，都会被隧道传送到你本地机器的端口`3000` (PC/笔记本电脑)。

**注意:**确保`forward.mydomain.com`的DNS 指向你的服务器。如果没有，用`name:forward`和`content : ip address of your server`创建一个`A Record`。

# 在您的服务器上创建虚拟主机

我用的是 Apache，但你也可以用 nginx 或其他网络服务器软件。

首先，您需要为 Apache 启用`proxy`和`proxy_http` mods。您可以通过运行以下命令来实现:

`sudo a2enmod proxy`

`sudo a2enmod proxy_http`

这里有一个 Apache 的示例虚拟主机文件，它为任何带有主机名`forward.mydomain.com`的本地端口`5000`创建了一个代理。

如果你想支持 HTTPS，你可以使用 Certbot。简单地按照他们的指示去做。HTTPS 也已启用。

我们已经为我们的需求设置了服务器。现在我们需要创建一个隧道，这样服务器上端口`5000`上的任何东西都可以到达我们本地机器的端口`3000`。

# SSH 端口转发

这是比较简单的部分。SSH 已经支持端口转发。你所要做的只是用这个命令`ssh`:

`ssh -R 5000:localhost:3000 ubuntu@forward.mydomain.com`

**注:** `-R`为远程端口转发

我们已经将服务器的端口`5000`映射到了`localhost:3000`。

万岁！当你打开`[https://forward.mydomain.com](https://forward.mydomain.com)`(如果启用了 HTTPS——否则使用 HTTP ),它会打开你本地机器上运行的任何东西，互联网上的任何人都可以访问它。要停止，只需退出。

最好的事情是你可以隧道到任何你想要的港口。服务器上不需要做任何更改。只需在本地更改该命令的端口。尽情享受吧！