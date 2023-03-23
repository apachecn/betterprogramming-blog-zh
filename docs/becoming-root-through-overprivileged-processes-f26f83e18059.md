# 通过过度特权化的过程成为根

> 原文：<https://betterprogramming.pub/becoming-root-through-overprivileged-processes-f26f83e18059>

## 利用过度特权进程提升 Linux 特权

![](img/f10ad77ea761ddcc0cabd7297d779b0a.png)

亚历克斯·拉德里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

欢迎回到 Linux 安全系列！在本系列中，我们将讨论影响 Linux 系统的安全问题以及导致这些问题的常见错误配置。我们开始吧！

权限提升是攻击者提升系统权限的一种方式。例如，假设一个攻击者已经获得了对您的 web 服务器的访问权，但只是作为一个低特权用户。他们不能读写敏感文件、执行脚本或更改系统配置。他们如何危害您的服务器并保持对服务器的访问？

如果他们可以找到一种方法来欺骗系统，使其认为自己是根用户，攻击者就可以进行更强大的攻击，如读写敏感文件和在系统中插入永久性后门。这就是特权升级的由来。

今天，我们来讨论一下攻击者如何利用特权过高的进程来提升他们的特权。

# 什么是超特权进程？

特权过高的进程是指运行时使用的权限比它需要的多。这是一个安全风险，因为如果攻击者劫持了以高特权运行的应用程序，攻击者就可以获得它在系统上的权限。今天，让我们看看攻击者在遇到以 root 用户身份运行的特权过高的进程时可以做些什么。

假设一个 web 应用程序遭受了典型的命令注入攻击。该应用程序允许用户通过 GET 请求参数提交文件名来读取文件:

```
[https://example.com/read?filename=abc.txt](https://example.com/read?filename=abc.txt)
```

该应用程序的 PHP 源代码如下所示。应用程序从用户处检索 URL 参数，然后将其直接连接到一个系统命令中:

```
<?php $file=$_GET[‘filename’]; system(“echo $file”);?>
```

该应用程序缺乏对“系统”调用的任何输入验证，使得攻击者能够通过命令注入执行任意系统命令。例如，攻击者可以通过将系统命令注入 filename 参数来执行系统命令:

```
[https://example.com/read?filename=abc.txt;ls](https://example.com/read?filename=abc.txt;ls)
```

这将导致应用程序执行该命令，该命令将打印`abc.txt`的内容，然后列出当前目录的内容:

```
echo abc.txt;ls
```

但是，如果 web 应用程序拥有 root 权限，该怎么办呢？那么攻击者可以做得更糟，因为注入的命令也将在 root 权限下运行。例如，攻击者可以使用命令注入，通过编辑`/etc/passwd`文件将自己添加为根用户:

```
[https://example.com/read?filename=abc.txt;](https://example.com/read?filename=abc.txt;ls)echo+“vickie::0:0:System Administrator:/root/root:/bin/bash”>> /etc/passwd
```

该命令向`/etc/passwd`文件添加一个新的根用户:

```
echo “vickie::0:0:System Administrator:/root/root:/bin/bash” >> /etc/passwd
```

因为`0`是 root 用户的 UID，所以添加一个 UID 为`0`的用户将赋予该用户 root 权限。这个用户将拥有用户名`vickie`和一个空密码。该命令对于普通用户来说通常是不可能的，因为只有特权用户才能修改`/etc/password`文件。但是，由于 web 应用程序以根用户身份运行，因此命令会成功，攻击者会获得对系统的根用户访问权限。

当攻击者可以执行任意代码时，特权过高的进程不仅仅是一种危险。假设在 web 应用程序中的这个端点上也存在路径遍历攻击:

```
[https://example.com/read?filename=abc.txt](https://example.com/read?filename=abc.txt)
```

攻击者可以通过使用文件名参数中的序列`../`来逃离当前目录，从而读取当前目录之外的文件:

```
[https://example.com/read?filename=../../../../etc/shadow](https://example.com/read?filename=../../../../etc/shadow)
```

`/etc/shadow`文件是 Linux 系统中的一个文件，包含系统用户的散列密码。只有特权用户才能读取。如果 web 应用程序有权查看`/etc/shadow`文件，攻击者就可以利用路径遍历漏洞读取该文件。然后，攻击者可以破解他们在该文件中找到的密码，以访问系统上特权用户的帐户。

# 注意安全！

我在本文中讨论的攻击都是由应用程序以过多的特权运行引起的。为了防止这些问题，您应该实现最小特权的原则。

这一原则意味着应用程序和进程只应被授予完成其任务所需的特权。例如，当应用程序只需要文件的读权限时，就不应该授予它任何写或执行权限。您应该经常检查面向用户的应用程序(如 web 服务器和文件服务器)是否以 root 用户身份运行。你永远不应该使用“以根用户身份运行”作为权限问题的默认解决方案。相反，您可以准确地授予应用程序所需的权限。这将降低攻击期间整个系统受损的风险。

感谢阅读！下一次，我们将深入探讨攻击者可以用来危害您的系统的更多特权提升技术。