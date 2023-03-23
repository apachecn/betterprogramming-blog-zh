# 10 个常用的 Bash 脚本和命令

> 原文：<https://betterprogramming.pub/10-frequently-used-bash-scripts-and-commands-afe9e7fa8cea>

## 有用的 Linux 脚本和命令的备忘单

![](img/861a99eba4e935e0f7bfe004e3d57233.png)

Gabriel Heinzer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

有时，您需要使用 Bash 来完成与您过去完成的任务类似的任务。你是否不得不再次搜索相同的命令？如果你没有记住语法，你可以在模板的帮助下加快开发速度。这样，更容易修改代码以供将来使用。

在本教程中，我将提供一组方便的 Linux 脚本和命令。你可以把它当作一张小抄。我根据自己的经验收集了最常用的例子。

我们开始吧！

# 1.递归搜索文件以查找表达式

该命令将列出所有包含字符串`nltk`的文件名:

```
$ find . -type f -exec grep -l "nltk" {} \;
```

注意,`-l`选项只列出了文件名。

# 2.打印代码执行的运行时间

当您想要跟踪代码执行的时间时，此脚本非常有用:

# 3.搜索和替换文件中的字符串

该命令将所有文件中等于`localhost:8000`的字符串替换为`localhost:8080`；

```
$ find . -type f -exec grep -l "localhost:8000" {} \; | xargs sed -i 's/localhost:8000/localhost:8080/g'
```

# 4.删除特定文件

*   该命令删除所有以`.log`结尾的空文件:

```
$ find . -type f -name "*.log" -exec rm {} \;
```

*   要删除超过 25 天的所有文件，请运行以下命令:

```
$ find . -type f -mtime +25 -exec rm {} \;
```

# 5.如果满足条件，则对每个文件执行命令

该脚本遍历当前目录中的所有文件，并检查文件名是否以字母开头。如果条件得到满足，它将执行本例中的`echo`命令:

# 6.从远程服务器下载文件

使用此命令从服务器下载文件并将其保存在本地:

```
$ scp username@server:path/to/file destination_path
```

# 7.将文件上传到远程服务器

将本地目录复制到远程服务器:

```
$ scp -r /local/dir username@server:/remote/dir
```

此命令以新文件名将本地文件上传到服务器:

```
$ scp file.txt username@server:/remote/dir/newfilename.txt
```

# 8.在两台远程服务器之间复制文件

```
$ scp user1@server1:/dir1/file.txt user2@server2:/dir2
```

# 9.将参数传递给脚本

如果要将命令行参数传递给脚本，请在脚本中使用以下语法:

```
echo "The first argument is: $1"
```

执行:

```
./myscript.sh myargument
```

如果需要第二个参数，使用`$2`，以此类推。

# 10.将变量分配给命令结果

您可以使用命令替换使变量等于另一个命令的输出。例如，下面的代码向由`logname`命令检索的当前登录用户发送一封电子邮件:

# 结论

在本文中，我介绍了一些方便且常用的 Linux 脚本/命令。拥有现成的模板可以节省很多工作。

我希望这些示例对您接下来的任务有用。

我可能会写这篇文章的续篇来涵盖更多的场景。

如果您对更多与 Linux 相关的教程感兴趣，您可能会喜欢我的另一篇文章:

[](/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [## 鲜为人知但功能强大的 Unix 命令来提高您的技术技能

### 带有简单示例的简便 bash 命令

better 编程. pub](/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) 

感谢您的阅读，下次再见！