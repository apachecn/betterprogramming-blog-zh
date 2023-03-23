# 用 Python 创建可远程访问的键盘记录器

> 原文：<https://betterprogramming.pub/creating-a-remotely-accessible-keylogger-with-python-56107b0218fa>

## 简单的基于套接字的程序

![](img/d5f10bfff16cf277d8263fc5b7eb9262.png)

[张杰](https://unsplash.com/@jay_zhang?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 什么是键盘记录器

键盘记录器是黑客最常使用的程序。

虽然系统管理员和网络安全专业人员也使用它，但它用于向远程机器泄漏数据，往往会给它带来坏名声。简单地说，键盘记录器捕获并记录目标机器的击键。它通常使用电子邮件或网络协议将捕获的击键发送到远程机器，有时由黑客运行。

通过过滤这些捕获的击键，黑客有时可以找到敏感数据，如密码、信用卡号或个人身份信息。今天，我们将创建一个键盘记录器，以增强对安全专业人员和道德黑客的理解。

# 我们在做什么

该项目将使用 Python，并依赖于套接字服务器。一个套接字将在我们的客户机上运行，充当服务器来接收数据并将其打印到终端。第二个文件将在我们的目标机器上运行，用于监视、格式化和发送击键到服务器。

# Python 中的套接字

套接字是网络上运行的两个程序之间双向通信链路的一个端点。套接字绑定到一个端口号，以便 TCP 层可以识别数据要发送到的应用程序。在 Python 中，不到五行代码就可以编写出套接字。要对客户端套接字进行编码，我们必须将一个端口绑定到一个 IP，并将其设置为监听模式，如下所示:

```
import socket
host = socket.gethostname()
port = 12345
s = socket.socket()     # TCP socket object
s.bind((host,port))
s.listen(5)
```

充当客户端的套接字只需连接到主机和端口，如下所示:

```
import socket
host = socket.gethostname()
port = 12345
s = socket.socket()     # TCP socket object
s.connect((host,port))
```

在你的键盘记录器中使用套接字的好处是，它允许你监视远程机器的击键，用于…系统管理目的。

# 发送格式化数据

基于上面的代码，我们的服务器将简单地监听连接客户端发送的数据，并将其打印到终端屏幕上。这将通过一个无限的“while True”循环来完成，如第 16–19 行所示。它必须以字节解码数据，如第 19 行所示。

我们的客户机将使用一个`on_press`监听器为服务器构建一个特制的字符串。它首先初始化一个要发送的字符串，`logstring`,并返回一个回车(第 1 行)。当每个字母被输入时，它会把它附加到`logstring`，只有当我们按下 enter 键时，它才会把字符串发送给客户机(见第 23 行)。

第 9-18 行的意思是添加非字母键，如 *Esc* ，这些键在 Python 的`*pynput.keyboard*` 库中添加了前缀 *Key* 。出于可读性的目的，这些非字母元素被添加到各自的行中。

这两个代码片段应该给你足够的数据来完成剩下的代码。要测试键盘记录器，首先运行您的服务器，然后运行您的客户端，然后输入。要查看完整的键盘记录程序，请查看我的 GitHub，链接如下。

我希望这个小项目能增强你的网络安全知识，帮助你踏上掌握网络安全的旅程。

黑客快乐！

[](https://github.com/AleksaZatezalo/KeyLogger) [## GitHub—AleksaZatezalo/键盘记录器

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/AleksaZatezalo/KeyLogger)