# 在 macOS Catalina 上安装 MongoDB

> 原文：<https://betterprogramming.pub/installing-mongodb-on-macos-catalina-aab1cbe0c836>

## 一个棘手的问题，由于卡特琳娜的只读根文件夹

![](img/6928f25312b9e52f63e9a987695ff564.png)

由[paweczerwiński](https://unsplash.com/@pawel_czerwinski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/leaf?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

最近，我开始了一份新的开发工作，从 Windows 转到了 Mac。切换非常顺利，只有一个主要问题:设置 MongoDB。这个问题与最新的主要 macOS 版本 Catalina 有关，在本文中，我将分享我的解决方案。

设置 MongoDB 在 Catalina 上运行比它应该的更费时间，但是希望这篇文章将把您从我经历的一些头痛中解救出来！

# 问题是

默认情况下，MongoDB 将数据库信息存储在根文件夹中的`data/db`中。但是 Catalina 更新提供了对根目录的只读访问。基于多姆伯克的这个建议，我的解决方案是把 T1 搬到 T2。

理论上，你可以把`data/db`放在任何你喜欢的地方。但是`/System/Volumes/Data/`似乎比`Documents`更安全。

# 1.禁用 SIP

然而，要访问`/System/`，需要禁用 macOS 的 SIP(系统完整性保护),这可以防止对该目录进行修改。要禁用 SIP，您需要:

*   在启动过程中，通过重启并按住`CMD + R`进入恢复模式。
*   打开终端(可以在顶部菜单的“实用程序”下找到)。
*   在终端内部，运行`csrutil disable`。您现在可以在正常模式下重新启动。

# 2.将/data/db 文件夹移出根目录

如果您在根目录的`/data/db`中有数据，您可以通过提供对根目录的临时写访问来移动它。

一旦 SIP 被禁用，打开终端并键入`sudo mount -uw /`。

现在您可以将您的文件夹从根目录移到`/System/Volumes/Data/`中。这种读写能力只在当前会话中有效。

# 3.(重新)安装 MongoDB

接下来，我想全新安装 MongoDB。我尝试了几种不同的选择，但 Brew 是最方便的。对于那些试图跟随旧教程的人来说，`mongodb`已经从`homebrew-core`中移除。

现在，您需要使用`mongo-community`，可以通过以下方式访问:

```
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community 
```

# 4.从终端运行 MongoDB

如果一切正常，你现在应该能够运行`mongod`和`mongo`——并看到除了`command not found`错误之外的其他错误！

如果我们只是运行`mongod`，我们将得到错误，告诉我们数据库目录是只读的。这是因为，默认情况下，该命令假设我们的数据库文件夹在根目录中。

所以，在运行`mongod`时，我们需要具体的`--dbpath /System/Volumes/Data/data/db`。因为我们正在使用`/System/`，所以我们也需要使用`sudo`。完整的命令是:

```
sudo mongod --dbpath /System/Volumes/Data/data/db
```

# 5.创建终端别名

每次打字都有点累。因此，如果您主要使用相同的数据库路径，我建议设置一个别名。

如果您使用 Zsh，您可以将以下内容添加到`~/.zshrc`:

```
alias mongod="sudo mongod --dbpath /System/Volumes/Data/data/db"
```

这意味着`mongod`会像你预期的那样工作。请注意，如果您需要更改`--dbpath`，您需要禁用这个别名。

我希望这为你节省了一些时间和挫折！这要归功于 Dom Berk，[我在本文中构建了他的解决方案。](https://superuser.com/questions/1458974/macos-switched-root-mongodb-data-folder-to-system-ownership-and-it-cannot-be-cha)