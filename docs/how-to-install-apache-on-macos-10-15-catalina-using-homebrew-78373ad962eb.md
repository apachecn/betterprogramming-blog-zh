# 如何使用自制软件在 macOS 10.15 Catalina 上安装 Apache

> 原文：<https://betterprogramming.pub/how-to-install-apache-on-macos-10-15-catalina-using-homebrew-78373ad962eb>

## 关于如何在 macOS Catalina 上设置 Apache/PHP 开发环境的最新系列文章的第 1 部分

![](img/bf16893c770d43a7b6ddfaeb748ed158.png)

图片来源:凯西·麦克马伦

本文面向那些希望在 Mac 上安装 Apache web 服务器作为开发环境的一部分的 web 开发人员。

这篇文章是系列文章中[下一篇的先决条件，它将引导你使用自制软件和 PECL 安装 PHP。](https://medium.com/p/ad5b6c9ffb17/edit)

这种安装 Apache 的方法会禁用 macOS 预装的版本，允许更灵活的版本控制，并将继续在所有 macOS 版本上工作。

这些说明对当前所有版本的 macOS 都适用。此外，您不必从全新安装操作系统开始使用这里的信息，但我已经看到了一些奇怪的事情发生在升级和全新安装时(例如，在升级后完全丢失了`mod_deflate.so`模块扩展文件)。我强烈推荐全新安装，但是如果你要冒险升级，请准备好寻找一些文件和路径。

让我们开始吧。

# 步伐

## 获取最新的操作系统更新

如果您刚刚安装了全新的操作系统，您可能不需要这样做。如果您正在升级，请进入“系统偏好设置”并选择“软件更新”图标，以确保一切都是最新的。

## 安装 Xcode

如果您对安装整个 Xcode 应用程序不感兴趣，那也没关系。Homebrew install 命令将安装它需要的基本 Xcode 命令行工具。然而，我建议完全安装，尤其是如果您运行的是 Catalina。为此，您可以从终端运行以下命令。

```
xcode-select --install
```

## 安装自制软件

以下命令将安装 Homebrew。

注意:下面的命令是一个单行，由于页面宽度限制，在 Medium 中已经换行。确保复制整行。

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## 创建 Apache 日志文件目录

Apache 预配置了自己的日志目录，但我们将创建自己的`/usr/local/log/`文件夹，以便在需要时可以轻松找到它们。如果您打算继续阅读下一篇文章并安装 PHP，我们也将对 PHP 日志使用相同的日志文件位置，并将所有日志放在一个地方。

按照以下步骤创建适当的目录并设置它们的权限。忽略任何你可能收到的*已经存在的*消息。

```
sudo mkdir /usr/local/logsudo mkdir /usr/local/log/httpdsudo chgrp -R staff /usr/local/log/httpdsudo chmod -R ug+w /usr/local/log/httpd/
```

## 安装和配置 Apache

macOS 10.15 Catalina 预装了 Apache。然而，我们将通过自制软件安装 Apache，然后将其配置为在端口 80 上运行，而不是使用交付的版本。

如果您已经运行了预安装的 Apache，那么需要首先关闭它，并删除任何自动加载脚本。运行以下两个命令没有坏处，即使是在全新安装时。

注意:第二个命令是一个单行，由于页面宽度的限制，它在 Medium 中换行了。确保复制整行。

```
sudo apachectl stopsudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
```

现在安装家酿提供的新版 Apache。

```
brew install httpd
```

完成后，运行以下命令启动 Apache 服务，并确保它在登录时自动重启。

```
brew services start httpd
```

现在，您已经安装了 Homebrew 版本的 Apache，并将其配置为在重启时自动启动。它应该已经在运行了，所以您应该能够将浏览器指向 [http://localhost:8080](http://localhost:8080/) 并看到一个简单的标题，上面写着*它工作了！*

注意，我们当前指向端口 8080。我们将在下面的步骤中改变这种情况。现在我们有了一个可以工作的 web 服务器，我们想做一些配置上的改变，让它更符合我们的喜好——或者更符合我的喜好。

让我们打开 Apache 配置文件:

```
open -e /usr/local/etc/httpd/httpd.conf
```

## 更改监听端口

向下滚动，找到显示 *Listen 8080* 的行，并将其更改为:

```
Listen 80
```

## 启用有用的 Apache 模块

接下来，您可以启用下面两个方便的 Apache 模块。您可以搜索以下两行，并通过删除前导 hashtag #来取消对它们的注释。

提示:deflate 行在`LoadModule`部分的中间，rewrite 行在`LoadModule`部分的最底部。

```
**LoadModule** deflate_module lib/httpd/modules/mod_deflate.so

**LoadModule** rewrite_module lib/httpd/modules/mod_rewrite.so
```

## 更改 Apache 用户和组设置

默认情况下，Apache 作为用户 *_www* 和组**_ www 运行。一会儿，我们将把我们的默认文档目录设置为我们主文件夹中的一个站点目录，当您试图访问您主目录中的文件时，这些用户和组设置将导致权限问题。**

**在您的`httpd.conf`文件中向下滚动大约三分之一，您会发现两行设置了 Apache 将运行的用户和组。更改这些以匹配您的用户帐户。将`your_username`替换为您的 MacBook 用户名，并使用群组名`staff`。**

```
**User** your_username

**Group** staff
```

## **设置 Apache 服务器管理员和服务器名称**

**找到`ServerAdmin`行，通过移除前导标签#来启用它(如果需要)，然后将电子邮件更改为您自己的电子邮件:**

```
**ServerAdmin** yourname@email.com
```

**通过删除开头的 hashtag #并将其更改为`localhost`，在配置中启用 Apache 服务器名称:**

```
**ServerName** localhost
```

## **更改文档根目录、目录和覆盖**

**接下来，我们将更改 Apache 的文档根目录。这是 Apache 用来提供文件的文件夹。默认情况下，文档根被配置为`/usr/local/var/www`。因为我们正在创建一个开发环境，并且希望尽可能避免文件夹权限问题，所以我们将把文档根目录更改为我们主目录中的一个文件夹(我们很快就会创建这个文件夹)。**

**继续向下滚动，找到`DocumentRoot`行。改为:**

```
**DocumentRoot** "/Users/your_username/Sites"
```

**接下来，您还需要更改位于`DocumentRoot`行正下方的标签引用。这也应该更改为指向您的新文档根目录:**

```
<**Directory** "/Users/your_username/Sites">;
```

**在同一块，你会发现一个`AllowOverride`设置。这应该从*无*更改为允许*所有*超控:**

```
**AllowOverride** All
```

## **设置 Apache 错误日志位置**

**最后，我们将 Apache 错误日志的位置设置为我们之前创建的文件夹。向下滚动一点，找到`ErrorLog`的那一行。将其改为如下:**

```
**ErrorLog** "/usr/local/log/httpd/error_log"
```

**保存并退出文本编辑器。**

## **创建站点文件夹**

**使用下面两行创建一个站点文件夹，并添加一个`index.html`文件，让我们开始吧:**

**注意:第二个命令是一个单行，由于页面宽度的限制，它在 Medium 中换行了。确保复制整行。**

```
mkdir ~/Sitesecho "<h1>It Works in my Sites Folder</h1>;" > ~/Sites/index.html
```

## **重启 Apache**

**现在，我们将重新启动以确保我们的配置设置是正确的:**

```
sudo apachectl -k restart
```

**尝试浏览到您的本地主机的新位置: [http://localhost](http://localhost/)**

**如果这有效，并且您看到消息*它在我的站点文件夹*中有效，那么恭喜您！您已经在端口 80 上运行了一个新的 web 服务器实例。**

**如果您收到一个错误，一个可能的原因是在您的`httpd.conf`文件中的目录条目周围使用了双引号。有时文本编辑器会将这些转换成拉丁字符类型的引号。这些引号是可选的，可以删除。尝试删除双引号并重新启动 Apache。**

# **第二部分**

**如果您是一名 PHP 开发人员，并且希望继续您的开发环境设置，那么请阅读本系列的第二部分，[如何使用自制软件和 PECL 在 macOS 10.15 Catalina 上安装 PHP 7.2](https://medium.com/@crmcmullen/how-to-install-a-php-7-2-on-macos-10-15-catalina-using-homebrew-and-pecl-ad5b6c9ffb17)。**