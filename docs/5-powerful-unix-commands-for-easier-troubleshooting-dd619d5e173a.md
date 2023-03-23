# 5 个强大的 Unix 命令，便于故障排除

> 原文：<https://betterprogramming.pub/5-powerful-unix-commands-for-easier-troubleshooting-dd619d5e173a>

## 一组经过验证的 Unix 命令，帮助您识别应用程序问题

![](img/2a5511bc9e37a7122b762bf9fb1f6414.png)

照片由 [Aryan Dhiman](https://unsplash.com/@mylifeasaryan_?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

识别应用程序问题可能非常耗时。这就是为什么了解各种故障排除命令至关重要，这可以使我们更容易发现问题。

类 Unix 系统带有许多强大的内置命令。在这篇文章中，我收集了我经常使用的几个最常用的。您可以使用这里的示例创建自己的备忘单来解决问题。

我们开始吧！

# lsof 命令

`[lsof](https://man7.org/linux/man-pages/man8/lsof.8.html)`代表“列出打开的文件”这无疑是最强大的故障排除命令之一，因为您可以从中获得大量信息。

## 检索属于活动进程的打开文件

没有任何参数，`lsof`列出了属于所有活动进程的所有打开文件。当您想要确定哪个进程正在使用您试图删除的文件时，这将非常有用。

要进行试验，请在您的终端中键入以下命令:

```
$ lsof
```

示例输出:

lsof 输出摘录

在另一个场景中，您可能想要检查一个可疑的流程。您可以检查该进程使用的文件以获得更多信息。

请注意，对于某些进程，您可能会得到“权限被拒绝”。这是因为您的用户不允许查看其他用户拥有的进程。在这种情况下，以`root`的身份运行命令，您将能够看到所有用户的所有进程。

## 按用户列出打开的文件

如果您想找出某个用户正在使用哪些文件，请键入以下命令:

```
$ lsof -u someuser
```

你会惊讶有多少打开的文件。为了缩小结果范围，您可能希望包含`[grep](https://man7.org/linux/man-pages/man1/grep.1p.html)`命令。

为了演示这个选项，我在后台启动了一个 Java 应用程序。

```
$ lsof -u kirshi | grep java
```

输出摘录:

带 grep 样本输出的 lsof

## 结束所有用户进程

如果您想要结束某个用户的所有进程，您可以通过执行以下命令轻松完成:

```
kill -9 `lsof -t -u someuser`
```

## 检索在特定端口上运行的进程

有时您想要列出特定端口上所有正在运行的进程。为此，请执行:

```
lsof -i :8090
```

示例输出:

具有指定端口输出的 lsof

请注意，`-i`参数列出了所有监听和已建立的连接。

如果不确定端口号，可以查询指定范围内的端口:

```
$ lsof -i :8090-9090
```

查找进程的另一种方法是使用 PID id:

```
lsof -p 23619
```

结果将包含属于`23619` PID id 的所有进程。

# netstat 命令

`[netstat](https://linux.die.net/man/8/netstat)`是监控网络连接的绝佳工具。

## 查找正在使用的端口

通过键入以下命令，您可以看到所有正在使用的端口，包括一些其他有用的信息:

```
netstat -tulpn
```

*   `-t`选项检查 TCP 连接。
*   `-u`选项检查 UDP 连接。
*   `-l` 选项告诉`netstat`只列出监听连接。如果您想查看所有连接，请使用`-a`选项。
*   `-p`选项显示了进程的 PID id。
*   `-n`选项显示数字地址，而不是试图解析主机、端口或用户名。

如果你想检查一个特定的端口，只需添加`grep`命令:

```
netstat -tulpn | grep 8090
```

示例输出:

netstat 输出

## 查找流程背后的用户

在某些情况下，知道正在运行的进程背后的用户名会很有用。添加`-e`选项来检索用户名。不要忘记移除`-n`选项；否则，用户名不会被解析。

```
$ sudo netstat -tulpe | grep 8090
```

用户的 netstat 输出

同样，如果您作为`root`进行查询，您将有权限查看所有进程的详细信息。

## 检查服务器状态

如果您想检查像`http`或`smtp`这样的服务器是否正在运行，您可以使用`grep`来过滤结果:

```
$ sudo netstat -tuple | grep smtp
```

netstat 检查 smtp 状态

# 卷曲命令

`[curl](https://man7.org/linux/man-pages/man1/curl.1.html)`通常用于在没有用户交互的情况下从服务器传输数据或向服务器传输数据。它支持 HTTP、FTP、SCP、SFTP、SMTP、LDAP 和许多其他协议。

## 检查服务运行状况

当您想要确定某个站点或服务是否正常运行时，它会派上用场。

要检查站点是否处于活动状态，请运行以下命令:

```
$ curl -Is [http://www.google.com](http://www.google.com)HTTP/1.1 200 OK
Content-Type: text/html; charset=ISO-8859-1
P3P: CP="This is not a P3P policy! See g.co/p3phelp for more info."
Date: Sun, 07 Feb 2021 22:22:10 GMT
Server: gws
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
Transfer-Encoding: chunked
Expires: Sun, 07 Feb 2021 22:22:10 GMT
Cache-Control: private
Set-Cookie: NID=208=WV1ENCL_yTQadGsKWAuYHUyBL0Htelh0nkkSNjnFpX2XjHEBj58-ldS9vjAl6MXH0jFwQC0oabKtcmS6jia2Wn9V-1C0CckpYyW17i8l3VTsQkL2RDLS43OBKxuE5UXiSfm9iSLbIxVV94oNcjC2SPuUfUIzwCJqgxDGi7Q3qRM; expires=Mon, 09-Aug-2021 22:22:10 GMT; path=/; domain=.google.com; HttpOnly
```

*   `-s`选项用于静音模式，禁用进度条和错误信息。
*   `-I`选项发送一个`HEAD` HTTP 请求，并且不返回请求体。

## 测试 telnet 功能

`curl`是一种快速测试 telnet 功能的方法。例如，您可能想要确认使用特定 TCP 端口从本地机器到远程主机的连接是否正常工作。

让我们测试一个运行在端口`8090`上的 Tomcat 服务器:

```
curl -v telnet://127.0.0.1:8090* Trying 127.0.0.1:8090...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8090 (#0)
```

如果我停止服务器，我会得到以下错误消息:

```
curl: (7) Failed to connect to 127.0.0.1 port 8090: Connection refused
```

## 测试你的应用程序和另一个服务之间的连接

您还可以验证您的应用程序和另一个服务(如数据库)之间是否存在连接。

```
$ curl -I -s myapp:5000
```

`myapp`是您的应用程序的名称，`5000`是您试图到达的数据库的端口号。

# ps 命令

`[ps](https://man7.org/linux/man-pages/man1/ps.1.html)`是另一个有用的命令，用于显示正在运行的进程的信息。

## 查找进程的 PID

`ps`命令通常与`-ef`选项结合使用:

```
$ ps -ef
```

*   `-e`选项显示所有进程。
*   `-f`提供了进程的详细信息。

样本输出:

ps 输出

您可以使用`grep`来缩小结果范围:

```
$ ps -ef | grep tomcat
```

您也可以将它与`aux`选项一起使用，以获得更详细的信息。

*   `-a`选项显示所有用户的进程。
*   `-u`代表面向用户的格式。
*   `-x`还显示后台运行的进程。

## 按内存对进程排序

按照内存使用情况对进程进行排序会很有用，这样可以找出是什么在消耗您的资源:

```
$ ps aux --sort=-%mem
```

ps 按内存排序

# 额外提示:cat 命令

你肯定熟悉`[cat](https://man7.org/linux/man-pages/man1/cat.1.html)`命令。虽然它通常不用于故障排除，但它仍然可以方便地识别问题。

例如，当您收到一个脚本失败的错误消息时，比如说在第 21 行，您希望看到那一行。您可以使用外部工具来可视化文件，以检查行号。但是您也可以使用`cat`来实现:

```
$ cat -n show-time.sh 1  #!/bin/bash
2  while true; do
3      sleep 5 
4      date +"Hi, the current time is %F %T"
5  done
```

*   `-n`选项显示文件中的行号。

如果您正在处理一个长文件，您可以使用`[less](https://man7.org/linux/man-pages/man1/less.1.html)`命令直接从命令行跳到所需的行号:

```
$ less +4 -N show-time.sh4     date +"Hi, the current time is %F %T"
5     done
```

*   `-N`选项显示行号。
*   `+`符号和数字跳转到文件中的那一行

# 结论

我们已经看到了如何使用像`curl`、`netstat`、`ps`和`lsof`这样的命令。这些命令在许多其他情况下也很有用。我建议浏览手册页，以发现更多的用例。

如果您喜欢本教程，您可能也会喜欢我关于其他 Unix 技巧和诀窍的文章:

[](https://medium.com/better-programming/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [## 鲜为人知但功能强大的 Unix 命令来提高您的技术技能

### 带有简单示例的简便 bash 命令

medium.com](https://medium.com/better-programming/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [](https://medium.com/better-programming/write-more-efficient-unix-scripts-with-heredoc-639719d0e660) [## 用 Heredoc 编写更高效的 Unix 脚本

### heredoc 简介，让脚本更简洁

medium.com](https://medium.com/better-programming/write-more-efficient-unix-scripts-with-heredoc-639719d0e660) [](https://medium.com/better-programming/automate-your-scripts-with-expect-9305faa6e2ff) [## 使用 Expect 自动化您的脚本

### 使用 Expect 脚本语言的强大功能来自动化您的程序

medium.com](https://medium.com/better-programming/automate-your-scripts-with-expect-9305faa6e2ff) 

感谢您的阅读，下次再见！