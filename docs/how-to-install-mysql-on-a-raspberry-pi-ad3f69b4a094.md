# 如何在树莓 Pi 上安装 MariaDB

> 原文：<https://betterprogramming.pub/how-to-install-mysql-on-a-raspberry-pi-ad3f69b4a094>

## 树莓派的 MySQL 替代品

![](img/fbb66ec6ecb96ea89d044c4b95558797.png)

照片由 [Kolar.io](https://unsplash.com/@jankolar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/database-administrators?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这是[在 Raspberry Pi 集群上开发和部署 Kubernetes 应用程序](https://medium.com/better-programming/develop-and-deploy-kubernetes-applications-on-a-raspberry-pi-cluster-fbd4d97a904c)系列的第六篇也是最后一篇文章。它标志着从纯粹的 Raspberry Pi 焦点到更广泛的软件开发的转变。随着数据库的安装，我们将拥有一个功能齐全的平台，满足我们对应用程序开发和部署的要求。

本文介绍了如何在 Raspberry Pi 上安装和配置功能相当于 MySQL 的替代品 [MariaDB](https://mariadb.org/) 。安装相对简单。配置数据库稍微复杂一些，但仍然很容易管理。

# 概观

大多数重要的应用程序都依赖于某种数据库。截至 2019 年，[关系数据库约占应用程序数据库平台的 60%](http://highscalability.com/blog/2019/3/6/2019-database-trends-sql-vs-nosql-top-databases-single-vs-mu.html) 。其中，MySQL 是最受欢迎的。MariaDB 也是相当受欢迎的。MariaDB 和 MySQL 做了几乎所有其他关系数据库做的事情。和 MySQL 一样，MariaDB 相对来说比较好用，而且免费。

为什么不直接装 MySQL？事实证明，没有一个 MySQL 包可以使用`apt-get`安装在 Raspberry Pi 上，这是在 Debian 和其他几个 Linux 发行版上安装和管理软件的首选方式。至少我找不到。

MariaDB 是由 MySQL 的最初开发者从 MySQL 分支创建的。[关于 MySQL 和 MariaDB 的利弊讨论，请参见本参考资料](https://hackr.io/blog/mariadb-vs-mysql)。MariaDB 的开发者说:“MariaDB 版本是 MySQL 版本的‘替代物’。”

MariaDB 网站上有[兼容性指南。](https://mariadb.com/kb/en/mariadb-vs-mysql-compatibility/)

## 短暂的回归…

本系列的第二篇文章[如何建立一个 Raspberry Pi 集群](https://medium.com/better-programming/how-to-set-up-a-raspberry-pi-cluster-ff484a1c6be9)，描述了我打算建立的集群拓扑。

![](img/ac18549dc8be607041ebb1ada41b4384.png)

我的集群拓扑——图片由 HiClipart、Comcast、BlackBox 和 Cleanpng 提供

如果您一直关注本系列，那么您可能已经按照类似的思路建立了自己的 Raspberry Pi 集群。在本文的其余部分，我将引用这个图表。

# 安装 MariaDB

安装 MariaDB 时需要做一个决定:是要安装服务器还是客户端？最有可能的情况是，您希望在您的集群上同时拥有这两种功能。为了灵活性和故障排除支持，我选择在我的集群中的所有主机上以及外部网络中的 MacBook Pro 上安装客户端。至于服务器，我选择把它放在`kubemaster`主机上(也就是 Pi 路由器)。我有几个原因:

*   剩下的就是树莓派 4 b+ vs 3B+了。我推断功能更强大的机器是安装它的最佳位置。
*   我用位于我的 MacBook 上的 MySQL(是的，MySQL)数据库进行了实验，看看这是否会产生性能差异。它没有。
*   也许我在`kubemaster`上安装 MariaDB 的主要原因是它可以从集群和外部网络访问。

安装很简单。MariaDB 安装附带了客户端和服务器组件。要安装:

```
sudo apt-get install mariadb-server -y
```

`-y`指示`apt-get`对所有提示自动回答是。如果您想对安装进行更多的控制，请关闭它。

如上所述，该命令还将安装 MariaDB 客户端。第一次登录时，你必须使用`root`用户。通过`root`用户的访问受到保护，因此您必须使用`sudo`:

```
sudo mysql -uroot
```

不得不使用`sudo`并不是最佳选择。我们将在下一节看到如何解决这个问题。

有一个选项可以提高上面执行的基本安装的安全性。这是使用`mysql_secure_installation`工具完成的。您可以使用它来设置适当的 root 密码，也可以选择配置一些其他设置，使安装更加安全。您可以按如下方式运行它:

```
sudo mysql_secure_installation
```

根据您的要求，按照提示进行操作。

在网络中的其他主机上安装客户端软件非常简单。在苹果电脑上，你可以使用[自制软件](https://brew.sh/):

```
brew install mysql-client
```

在 Raspberry Pi 主机上，运行:

```
sudo apt-get install mysql-client
```

即使你遵循了上面的所有步骤，你能做的仍然很有限，包括不使用`sudo`作为 root 登录。接下来我们将讨论如何解决这个问题。

# 配置 MariaDB

此时，您已经有了一个正在运行的 MariaDB 服务器和管理它并与之交互所需的客户机软件。最初，只配置了 root 用户，您必须使用`sudo`登录。此外，远程访问未启用，必须进行配置。

## 完成根用户的设置

这一步将使得不必使用`sudo`登录 MariaDB 成为可能。像我们在上一节中必须做的那样登录到 MariaDB:

```
sudo mysql -uroot
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 67
Server version: 10.3.22-MariaDB-0+deb10u1 Raspbian 10...
```

接下来，我们需要修改`root`的权限:

```
MariaDB [(none)]> GRANT ALL PRIVILEGES on *.* 
  TO 'root'@'%' 
  IDENTIFIED BY '<yourpasswordhere>';
Query OK, 0 rows affected (0.001 sec)MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.001 sec)
```

第一个命令配置网络中任何机器的 root 访问权限(这是用`'root'@'%'`做的)。`IDENTIFIED BY '<yourpasswordhere>'`用于设置`root`的密码(如果你还需要的话)。`FLUSH PRIVILEGES`使更改立即生效。

为确保这一切正常，请退出会话，然后不使用`sudo`再次登录:

```
mysql -uroot -hlocalhost -p
```

应该会提示您输入密码。输入`root`的密码。如果您已经登录，请继续并注销:

```
quit;
```

如果你有困难，重新访问上述步骤，并确保你做了一切。

## 配置远程访问

如果您计划从任何远程主机甚至本地主机的 IP 地址访问服务器，您需要配置 MariaDB 以允许这样做。为此，您需要编辑`/etc/mysql/mariadb.conf.d/50-server.cnf`(实际文件名可能不同，但位置应该相同)。这是 MariaDB 的配置文件。文件的前几行应该是这样的:

注意上面的最后一行，指定`bind-address`的那一行。如果这个没有被注释掉，也不会被注释掉，那么您将只能从`localhost`或`127.0.0.1`连接到 MariaDB。要允许来自网络中其他地方的连接，就像集群中的其他主机一样，您需要注释掉如上所示的行，并重新启动服务器:

```
sudo systemctl restart mysql
```

需要重新启动才能重新加载修改后的配置。

您可以通过使用主机的非本地 IP 地址重新登录 MariaDB 来验证此步骤是否成功。您可以通过从不同的主机登录来完成同样的事情。我的主机地址是`10.0.0.100`:

```
mysql -uroot -h10.0.0.100 -p<yourpasswordhere>
```

如果这不起作用，返回并重新检查上面的步骤，包括重新启动 MariaDB。

## 添加另一个用户并配置远程访问

所以现在您应该能够从本地机器以及其他主机以`root`的身份登录。接下来，您需要为您的应用程序添加一个用户，并设置所需的访问权限。在这种情况下，我们将添加密码为`app1`的用户`app1`(当然，您永远不会指定一个与真实世界中的用户 ID 相匹配的密码，因为这是非常不安全的)。

作为`root`，登录服务器(抱歉，你必须使用`sudo`):

```
sudo mysql -h10.0.0.100 -uroot -p<yourpasswordhere>     

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 180084
Server version: 5.5.5-10.3.17-MariaDB-0+deb10u1 Raspbian 10... 
```

键入以下命令:

```
mysql> use mysql;mysql> CREATE USER 'app1'@'%'
  IDENTIFIED BY ‘app1’;mysql> GRANT ALL
  ON *.*
  TO 'app1'@'%'
  WITH GRANT OPTION;mysql> FLUSH PRIVILEGES;
```

`use mysql;`表示我们想要从`mysql`数据库中执行以下命令。这是主系统数据库，其中存储了用户信息等内容。`CREATE USER 'app1'@'%'`显然创造了用户。与上面的`root`一样，`@`表示允许用户从哪个主机登录。指定通配符`'%'`表示用户`app1`将被允许从任何主机登录。如上，`IDENTIFIED BY`指定了密码。

`GRANT ALL`指定该用户将能够执行几乎任何操作，包括创建数据库和表，以及授予其他人执行相同操作的权限。这也是一个安全问题，不是你在实践中想要做的事情。

`FLUSH PRIVILEGES`重新加载权限配置。

与`root`一样，您现在应该能够从集群中的任何地方以`app`的身份登录到服务器。

就这样，你们都完成了！MariaDB 已经完全配置好，可以使用了。

# 摘要

在本文中，我们已经安装并配置了 MariaDB。下一步是使用集群作为应用程序部署平台。

这标志着在 Raspberry Pi 集群[系列上开发和部署 Kubernetes 应用的 Raspberry Pi 焦点的结束。后续文章将更侧重于应用程序。该应用程序的目标是:](https://medium.com/better-programming/develop-and-deploy-kubernetes-applications-on-a-raspberry-pi-cluster-fbd4d97a904c)

1.  相对简单，易于理解，并具有生产质量。它应该比简单的应用程序模板多一点，比全功能的应用程序少一点。这可能是一个艰难的平衡动作。
2.  展示应用程序的非功能特性的最佳实践，所谓的[*——能力*](https://towardsdatascience.com/architecting-for-the-ilities-6fae9d00bf6b) ，或者应用程序的[质量属性](https://en.wikipedia.org/wiki/List_of_system_quality_attributes)。其中一些由平台直接支持，即 Kubernetes 和 Helm。其他的必须设计并实现到应用程序中，例如，度量和日志记录。
3.  ReSTful 原则的有效使用
4.  自动化测试和持续集成
5.  通过 Helm 部署到 Kubernetes
6.  用 Go 写的

我已经在部分解决这些目标的应用程序上取得了良好的进展。这仍然是一项正在进行的工作，但这是一个良好的开端。如果你想先睹为快，目前在 GitHub 上有[可用。如果您雄心勃勃，并且已经设置了集群，自述文件可以帮助您完成其他先决条件和部署步骤。如果你更有野心，阅读代码，制造问题，提交 PRs，等等。](https://github.com/youngkin/mockvideo/tree/master)

感谢您的关注！请继续关注讨论应用程序开发和部署最佳实践的下一系列文章。