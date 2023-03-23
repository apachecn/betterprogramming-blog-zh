# 运行 Shell 脚本的 5 种方法

> 原文：<https://betterprogramming.pub/5-ways-to-run-a-shell-script-7829fc80b49c>

## 你用了多少？

![](img/20ba2a3caa7285ec07ba460f90c0fa6b.png)

由 [Troy 陈](https://unsplash.com/@cbben?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/keyboard?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

编写有效的 shell 脚本的能力是任何程序员工具箱中的必备工具，因为它使一切变得更加容易。

也是蛮上瘾的。一旦你掌握了窍门，你就开始让你生活中越来越多的部分自动化，直到你成为这个人。

然而，在本文中，我们不会编写 shell 脚本。相反，我们将看看五种最常见的执行方式。我说的最常用是指我或我认识的人最常使用的。如果你知道任何其他巧妙的方法或技巧，一定要写在评论里。我的默认 shell 是 [bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 。

# 1.从文件运行

这是执行脚本最简单也是最常见的方式。您只需编写脚本，然后通过在命令行中键入其名称来运行它。

为了能够做到这一点，存储脚本的文件夹必须能够被系统发现，因此您必须将它添加到`[$PATH](https://en.wikipedia.org/wiki/PATH_(variable))`变量中。按照惯例，这个文件夹叫做`bin`。你可以这样把它添加到`$PATH`:

```
export PATH="$HOME/bin:$PATH"
```

但是，如果您想将脚本存储在其他文件夹中，该怎么办呢？没必要担心。您可以轻松地在脚本和 bin 文件夹之间创建一个符号链接，这样无论它存储在哪里都可以被发现。假设你在一个不是`bin`的文件夹中，你想把你的脚本`cool_script`保存在那里。您可以使用以下命令:

```
ln -s cool_script ~/bin/cool_script
```

最后一件重要的事情是使用以下命令向脚本添加可执行权限:

```
chmod u+x cool_script
```

# 2.用指定的解释器运行

这种方法非常简单。这甚至不需要您在脚本开始时设置一个 shebang。或者，如果您设置了 shebang 并指定了其他解释器，它将与 shebang 无关地运行。

如果您的根目录中有一个名为`simple.sh`的脚本，您可以通过以下方式用 bash 运行它:

```
bash ~/simple.sh
```

或者如果你喜欢`zsh`:

```
zsh ~/simple.sh
```

我更喜欢第一种方法，但是在某些情况下你也需要这样做。

# 3.在后台运行 nohup 命令

假设您`ssh`进入一个远程服务器做一些工作，并启动一些长时间运行的进程。如果您现在中止连接，作业将被终止，工作将永远无法完成。这就是`nohup`命令非常有用的地方。

当与远程终端的连接挂断时，操作系统向所有从该终端启动的程序发送`SIGHUP`消息。然后，程序要么立即停止执行，要么被构建来捕获`SIGHUP`消息并继续运行，或者在退出之前做一些清理工作。

在后台运行某些东西的第一个选项是使用`$`标志。这将进程放入后台作业列表中(可以用`jobs -l`列出)。但是，当 shell 关闭时，它仍然会接收到`SIGHUP`消息。

第二个选项是使用`disown`命令。这将从 shell 的作业列表中删除该作业。其中一个含义是它不会接收到`SIGHUP`命令。我们越来越接近了，但是如果我们终止`ssh`连接，例如，我们的程序在试图从`stdin`读取或向`stdout`写入时就会失败。

第三个也是最理想的选择是使用`nohup`命令。这个宝贝做了三件重要的事情，基本上将过程与外壳分开:

*   关闭`stdin`。
*   将`stdout`和`stderr`重定向到名为`nohup.out`的文件。
*   使进程不接收`SIGHUP`。

我们必须将它与`$`配对，让它在后台运行，这不是默认模式。例如，如果我们想这样运行`cool_script`:

```
nohup cool_script $
```

# 4.exec 命令

当您以常规方式运行脚本时，shell 会生成一个新的子进程。这叫分叉。`exec`命令用于防止这种情况。它允许你用跟随`exec`的命令完全替换当前的外壳。当前进程被破坏，没有子 shell 被分支。例如:

```
exec cool_script
```

那么，我们为什么要用这个呢？我不会在我写的每个脚本中都使用`exec`，但它有时会很有用。

假设您在 Mac 上，默认 shell 是`zsh`，但是您想在`bash`中做一些工作。您可以简单地运行`exec bash`将默认的内存 shell 改为 bash。

另一件有用的事情是将默认的 bash shell 替换为受限的 shell，因为您可能想要控制用户在您的系统上可以做什么。然后简单运行`exec rbash`就大功告成了。

# 5.安排脚本在特定时间运行

稍后运行脚本的最简单和最容易的方法是使用`at`命令。假设您想在今天下午 4 点运行`cool_script`。只需这样做(假设脚本位于根目录):

```
at -f ~/cool_script teatime
```

除了喝茶时间，你可以指定任何你想要的，比如`now + 2 minutes`、`tomorrow`或`15:08`。

第二种选择是使用`crontab`工具。如果您想在每天下午 4 点运行`cool_script`，那么您可以使用`crontab -e`来编辑您的 crontab 文件，或者创建一个不存在的文件。然后像这样编写 cron 作业:

```
0 16 * * * /path/to/cool_script
```

第三个选择是利用`launchd`，这是一个自动化脚本的非常好的工具，只有在 Mac 上才适用。

让我们从在`~/Library/LaunchAgents`创建一个`.plist`文件开始:

```
touch ~/Library/LaunchAgents/com.schedule-test.daemon.plist
```

现在用您选择的编辑器打开该文件，并向其中添加以下字段:

这为每 60 秒运行我们的脚本创建了一个配置，如`StartInterval`中所定义的。`RunAtLoad`表示加载作业定义后将立即运行任务。

要启动作业运行，请执行以下操作:

```
launchctl load ~/Library/LaunchAgents/com.schedule-test.daemon.plist
```

为了阻止它:

```
launchctl unload ~/Library/LaunchAgents/com.schedule-test.daemon.plist
```

如果您想安排您的作业在特定日期运行，您可以将类似这样的内容插入到您的`.plist`文件中:

```
<key>StartCalendarInterval</key>
<dict>
 <key>Minute</key>
 <integer>42</integer>
 <key>Hour</key>
 <integer>13</integer>
 <key>Weekday</key>
 <integer>2</integer>
</dict>
```

这将在每周二下午 1:42 运行您的脚本。很酷，不是吗？

# 结论

在本文中，我们研究了我认为执行 shell 脚本的五种最常见的方式。感谢您花时间阅读本文。我希望你从中获得一些新的见解或价值。