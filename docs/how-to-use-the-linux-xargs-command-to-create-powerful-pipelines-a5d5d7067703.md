# 如何使用 Linux Xargs 命令创建强大的管道

> 原文：<https://betterprogramming.pub/how-to-use-the-linux-xargs-command-to-create-powerful-pipelines-a5d5d7067703>

## 带有实例的 xargs 命令介绍

![](img/897e5c631d3bd63662e75401fec41fd5.png)

Photo by [小谢](https://unsplash.com/@guogete?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral).

`[xargs](https://man7.org/linux/man-pages/man1/xargs.1.html)`是一个 Unix 命令，用于从标准输入构建和执行命令。您可以将它与其他强大的 Unix 命令结合使用，如`grep`、`awk`等。，通过使用管道。简单地说，它将一个命令的输出作为另一个命令的输入进行传递。

它可以在大多数类似 Unix 的操作系统上运行。但是请注意，这个命令在 macOS 和 Linux 上的工作方式略有不同。本教程中的例子是在 Ubuntu 发行版上准备和测试的。

在本文中，您将通过简单的例子深入了解`xargs`的实际用例。

我们开始吧！

# 常见用途

首先，让我们看看`xargs`命令本身是如何工作的。

如果您尝试不带任何参数运行它，它将默认执行`echo`命令。要获得输出，只需键入一些内容，然后按下键盘上的 Ctrl+D:

```
$ xargs
hello
```

结果是:

```
hello
```

## 一次处理多个文件

通常，`xargs`与其他命令结合使用。为了向您介绍典型的用例，我需要一些测试文件。让我们直接使用`xargs`命令来创建我们的虚拟数据。

要一次创建多个文件，结合使用`xargs`和`touch`命令:

```
$ echo test1.log test2.log test3.log 1.txt 2.csv | xargs touch$ ls
1.txt  2.csv test1.log  test2.log  test3.log
```

注意，`echo`命令的输出作为`touch` 命令的输入被传递。

## 查找文件并执行各种操作

`xargs`最常见的组合之一是与`find`命令一起使用。当您想要执行重命名或删除多个文件等操作时，它会很有用。

假设您想要查找并删除名称中包含`test`的所有日志文件。让我们来玩虚拟数据:

```
$ find . -type f -name "*test*.log" | xargs ls
./test1.log  ./test2.log  ./test3.log$ find . -type f -name "*test*.log" | xargs rm -f$ ls
1.txt  2.csv  3.test
```

如您所见，首先，我执行了`ls`命令来检查我得到了哪些文件名。然后我运行`rm -f`命令删除这些文件。

但是，请记住，有些文件的名称中可能有空格。考虑下面的文件名:`something with white space.log`。

让我们尝试使用前面的命令删除该文件:

```
$ ls
1.txt   2.csv   3.test  'something with white space.log'$ find . -type f -name "*.log" | xargs rm -f$ ls
1.txt   2.csv   3.test  'something with white space.log'
```

不幸的是，我们没有得到预期的结果。那么，我们如何处理空白呢？使用`find -print0`选项和`xargs -0`选项:

```
$ find . -type f -name "*.log" -print0 | xargs -0 rm -rf$ ls
1.txt  2.csv  3.test
```

这样，`-print0`用一个`null` 字符分隔文件名，这样包含空格或换行符的文件名就可以被`xargs`正确解释。

## 将它与 grep 命令结合使用，可以在多个文件中查找模式

`xargs`的另一个典型用法是与`grep`命令一起使用。例如，让我们尝试通过扫描多个文件来找到字符串`hello`:

```
$ find . -type f | xargs grep 'hello'./1.txt:hello there
./3.test:hello here
```

## 从参数文件中读取输入

到目前为止，我们已经看到`xargs`命令接受来自标准输入的输入。它还可以从文件中读取项目。为此，添加`-a`选项，它代表`— arg-file` *。*

我已经创建了一个名为`files.txt`的输入文件:

```
$ cat files.txt 
3.test
1.txt$ xargs -a files.txt ls -lart
-rw-rw-r-- 1 kirshi kirshi 12 apr    2 21:27 1.txt
-rw-rw-r-- 1 kirshi kirshi 19 apr    2 21:27 3.test
```

我们刚刚对参数文件中列出的所有文件执行了`ls -lart`命令。

## 提示用户确认

有时当我们执行像`rm -f`这样的破坏性操作时，我们可能会要求用户确认。`-p`选项可以为我们做到这一点。代表`— interactive` 并提示用户确认是/否。

让我们来看看这个例子:

```
$ xargs -p -a files.txt rm -f
rm -f 3.test 1.txt ?...y$ ls
2.csv  files.txt
```

它非常方便地显示将要执行的操作。如你所见，这些文件已经被删除了，因为当程序询问时，我输入了`y`表示“是”。

## 逐行执行命令

上面的操作删除了所有文件。如果我们想一个一个地检查和确认呢？以下是`-n`选项(`— max-args`)发挥作用的方式。

它需要一些您希望`xargs`在每个命令行传递的参数。

让我们再次运行删除命令—这一次，使用`-n 1`:

```
$ xargs -p -n1 -a files.txt rm -f
rm -f 3.test ?...y
rm -f 1.txt ?...n$ ls
1.txt  2.csv  files.txt
```

因为我在终端中输入了`n`，所以保存了`1.txt`文件。

# 摘要

我们已经看到了如何结合使用`xargs`命令和其他命令来执行如下操作:

*   一次创建/操作多个文件
*   基于模式查找文件并对每个文件执行操作
*   从参数文件中读取输入
*   在执行命令之前提示用户进行确认

这些是一些最常用的情况，但是您可以使用这个命令做更多的事情。要探索其他特性，请阅读[文档](https://man7.org/linux/man-pages/man1/xargs.1.html)。

如果你有一些喜欢的`xargs`命令，欢迎在评论中分享。

如果您喜欢这篇文章，您可能也会喜欢我关于其他 Unix 技巧的文章:

[](/5-powerful-unix-commands-for-easier-troubleshooting-dd619d5e173a) [## 5 个强大的 Unix 命令，便于故障排除

### 一组经过验证的 Unix 命令，帮助您识别应用程序问题

better 编程. pub](/5-powerful-unix-commands-for-easier-troubleshooting-dd619d5e173a) [](/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [## 鲜为人知但功能强大的 Unix 命令来提高您的技术技能

### 带有简单示例的简便 bash 命令

better 编程. pub](/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [](/write-more-efficient-unix-scripts-with-heredoc-639719d0e660) [## 用 Heredoc 编写更高效的 Unix 脚本

### heredoc 简介，让脚本更简洁

better 编程. pub](/write-more-efficient-unix-scripts-with-heredoc-639719d0e660) 

感谢您的阅读，下次再见！