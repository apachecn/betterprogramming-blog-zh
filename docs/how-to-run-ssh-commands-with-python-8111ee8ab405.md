# 如何在 Python 中抽象 SSH 命令

> 原文：<https://betterprogramming.pub/how-to-run-ssh-commands-with-python-8111ee8ab405>

## 使用 Popen 从 Python 脚本中执行有效的 ssh 调用

![](img/b97e31228fcfd2e4bad7f33b48c3f786.png)

由[阿吉特](https://unsplash.com/@arget?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hacking?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

我喜欢用 Python 构建脚本。解决恼人的问题、收集一些基本数据或运行一些命令完全是轻而易举的事情。能够连接到远程机器并在其上执行命令是事情变得有点棘手的地方。

像 Ansible 这样的大型工具可以抽象出大量的连接逻辑，但是像这样的工具可能会矫枉过正，并且比简单的脚本需要更长的设置时间。

在过去，我曾多次求助于发出原始的系统调用，或者试图在 Bash 中完成一些事情。在脚本中向 ssh 传递带有多个参数的复杂命令会变得很快。那么，在不离开 Python 的舒适环境的情况下，有什么简单、轻松的方法可以做到这一点呢？当然是 Python 的标准库之一。

在本文中，我们将探索`Subprocess`库。具体来说，我们将看看如何使用`Popen`来抽象 ssh 系统调用，并使管理长时间运行的命令变得更加容易。让我们来看看。

# 入门指南

在我们继续之前，准备一两台测试主机通过 ssh 访问是很有帮助的。理想情况下，您应该已经在这些远程主机上的`authorized_keys`中拥有了您的公共 ssh 密钥。这将通过 ssh 提供对主机的访问，而不必在命令行上输入密码或将其存储在某个文件中(这不是一个好的做法)。

如果您习惯于在 ssh 中使用密码，并且不知道从哪里开始，请查看关于生成新的 ssh 密钥对的 GitHub 文章[。](https://docs.github.com/en/github-ae@latest/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

在继续执行脚本之前，确保您可以通过命令行上的 ssh 访问测试主机。

# 基本脚本

让我们看看如何构建一个简单的 Python 脚本来在远程主机上运行 ssh 命令:

```
#!/usr/bin/env python3import time
from subprocess import Popen, PIPEdef run_ssh_cmd(host, cmd):
    cmds = ['ssh', '-t', host, cmd]
    return Popen(cmds, stdout=PIPE, stderr=PIPE, stdin=PIPE)
```

这里发生了很多事情，所以让我们一次分解一个元素。

首先，我们从`subprocess`获取所需的`Popen`和`PIPE`对象，然后构建一个简单的方法来运行 ssh 命令(稍后将详细介绍时间库)。使用`Popen`我们传递一个命令列表和它们各自的参数；`cmds`的变数。这有点冗长，但比简单地传递一个插入的字符串更安全、更干净。

在这个列表中，我们调用`ssh`，然后使用`-t`参数分配一个伪终端。接下来，我们传递要连接的主机和要在该主机上运行的以下命令。

这个方法返回了一个`Popen`对象，我们为底层调用的所有输入和输出设置了管道。接下来我们将探索如何使用管道进行输出。

# 构建用于输出的命令

让我们假设我们想要连接到一个远程主机并列出主目录的内容。我们将在现有脚本中添加以下内容:

```
results = run_ssh_cmd('my_remote_host.com', 'ls -l').stdout.read()
print(results)
```

为了从`Popen`命令中获得`stdout`，我们在之前设置的相应管道上调用`read()`。现在应该可以执行 ssh 命令，并在控制台中显示目录的内容。

# 构建无输出的命令

如果我们真的不关心 ssh 命令的输出会怎么样呢？如果我们需要并行运行一堆缓慢的命令，而不是等着它们一个一个地完成，那该怎么办？使用`Popen`我们可以轻松实现这一点。让我们更新我们的脚本:

```
ssh_cmds = []
ssh_cmds.append(run_ssh_cmd('host1.com', 'cmd 1'))
ssh_cmds.append(run_ssh_cmd('host2.com', 'cmd 2'))while all(cmd.poll() is None for cmd in ssh_cmds):
    time.sleep(1)
    print('not done yet')print('done!')
```

在这个新版本的脚本中，我们多次发出`run_ssh_cmd`方法，并将结果存储在一个列表中(不是每次都从管道中读取)。

在一个`while`循环中，我们检查每个命令的结果是否仍然是`None`，然后在等待一秒钟后再次尝试。直到列表中的所有命令不再是`None`时，该循环才会返回。如果您担心命令花费太长时间或被挂起，您可以添加一个超时。

连接超时可以直接添加到`ssh`中，使用:

```
-o ConnectTimeout=10
```

如果您想给`while`循环添加一个超时，您可以添加一个新变量，并在每次循环迭代后递增。一旦变量达到一定的秒数，发出`break`并引发错误。

使用这个脚本，我们可以并行启动一系列`ssh`命令，然后等待它们同时完成。现在让我们来看看清理任何不需要的输出。

# 让 SSH 更安静

如果您注意到控制台中底层 ssh 调用的无关输出，您可能希望将其静音。在这种情况下，如果您想让`ssh`更安静地执行，可以在底层 ssh 调用中添加`-q`标志。

如果您仍然注意到额外的输出，您可能需要抑制`stderr`。您可以通过添加以下内容将`stderr`连接到`/dev/null`:

```
ssh -t <host> <cmd> 2> /dev/null
```

如果您连接到新主机，并注意到系统提示您接受主机密钥，您可以通过向`ssh`命令参数添加以下内容来禁用严格的密钥检查:

```
-o StrictHostKeyChecking=no
```

感谢您的阅读！如果你喜欢这个故事，可以考虑看看我的 [4 简单库，快速测试 Python 代码](https://medium.com/swlh/4-simple-libraries-to-quickly-benchmark-python-code-8d3dfd288d7a)文章，创建一个高效的 Python 开发工作流。