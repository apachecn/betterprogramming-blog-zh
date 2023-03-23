# 用 Heredoc 编写更高效的 Unix 脚本

> 原文：<https://betterprogramming.pub/write-more-efficient-unix-scripts-with-heredoc-639719d0e660>

## heredoc 简介，让脚本更简洁

![](img/0d16af1b9f3ec45bbf126e424020904c.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

一个[这里的文档](https://en.wikipedia.org/wiki/Here_document)，通常被称为 heredoc，是一段代码，它通过输入流向交互程序或命令发送一个命令列表。您可以在 bash、zsh、ksh、csh 和许多其他 Unix shells 中使用它。它通常与`ssh`、`sftp`、`cat`和`tee`命令一起使用。

Heredoc 是一个强大的工具，可以让您的 shell 脚本更加整洁和高效。当您想要执行多个命令时，它非常有用。我们将通过简单的例子来介绍它最有用的特性。

我们开始吧！

# 句法

Heredoc 使用以下形式的语法:

```
some command << delimiter
Heredoc block of code
delimiter
```

它需要`<<`重定向操作符和一个分隔符标记。分隔符标记可以是任何唯一的标记。通常命名为`EOF`或`END`。

# 示例用法

## 多行 sftp 命令

为了理解使用 heredoc 的好处，让我们看一下下面的脚本:

该程序执行多个`sftp`命令。首先，它在您的本地`tmp`目录中创建一个文件。然后，它使用`sftp`命令将文件传输到远程服务器。假设您有一个名为`file`的远程文件。该脚本将删除它，并用新上传的文件替换它。

将脚本保存为`.sh`文件，并在您的终端中运行。注意，我已经设置了一个 SSH 密钥来登录我的远程服务器。因此，我不需要提供密码。

这是输出:

```
$bash simple_script.shsftp> put /tmp/somefile /home/user/file_tmp
sftp> -rm /home/user/file
sftp> rename /home/user/file_tmp /home/user/file
sftp> quit
```

这种方法没有错。然而，它可以更优雅。

下面是如何使用 heredoc 重写脚本:

执行脚本:

如你所见，结果是一样的。现在比较这两个脚本。第二个更整洁，不是吗？

## 使用 ssh 的多行命令

让我们看看在使用`ssh`时如何利用 heredoc 的特性。假设您想要执行多个命令(例如，在远程服务器上创建目录)。你会这样做:

```
ssh -T user@host "mkdir /home/user/dir1"
ssh -T user@host "mkdir /home/user/dir2"
```

这会有用的。但是，您必须多次执行`ssh`命令，效率不是很高。

Heredoc 的做法如下所示:

```
ssh -T user@host << EOF
mkdir /home/user/dir1
mkdir /home/user/dir2
EOF
```

## 使用 cat 的多行命令

让我们看看另一个有用的例子。当与`cat`命令结合使用时，Heredoc 非常方便。您可以将多个命令传递给`cat`,并作为单个命令或脚本执行它们:

```
cat << EOF
The current date and time is: $(date)
The current kernel version is: $(uname -r)
The current directory is: $PWD
EOF
```

输出:

```
The current date and time is: Sat Jan 9 16:09:06 CET 2021
The current kernel version is: 19.6.0
The current directory is: /home/user/heredoc
```

请记住，如果您引用分隔符，参数将不会被解析:

```
cat << "EOF"
The current date and time is: $(date)
The current kernel version is: $(uname -r)
The current directory is: $PWD
EOF
```

输出:

```
The current date and time is: $(date)
The current kernel version is: $(uname -r)
The current directory is: $PWD
```

## 参数和命令替换

Heredoc 接受参数和命令替换。例如，我们已经在我们的`sftp`程序中使用了`{user}`作为参数替换。另一个例子是我们刚刚在上面使用的`$date`命令。

## 取消制表符缩进

使用 heredoc，您可以取消制表符缩进。这可以简单地通过向重定向操作符添加一个`-`来完成:

```
cat <<-EOF
First line
      Second line
EOF
```

这导致:

```
First line
Second line
```

## 简洁的评论

heredoc 的另一个有用的特性是，你可以让你的脚本中的注释更具可读性。想象一下，你要写一篇很长的评论。通常情况下，你会在每个注释行前面放一个`#`符号。Heredoc 通过使用如下的`:`命令使它变得更加优雅:

```
: <<'EOF'
This is a test comment
to demonstrate heredoc
features
EOF
```

# 结论

在本教程中，我们已经看到了 heredoc 最强大的特性。

现在您知道了如何更高效、更优雅地执行多个命令，编写更可读的注释，以及在 heredoc 脚本中执行参数和命令替换。

如果您喜欢这篇文章，您可能会喜欢我关于 Unix 提示和技巧的文章:

[](https://medium.com/better-programming/automate-your-scripts-with-expect-9305faa6e2ff) [## 使用 Expect 自动化您的脚本

### 使用 Expect 脚本语言的强大功能来自动化您的程序

medium.com](https://medium.com/better-programming/automate-your-scripts-with-expect-9305faa6e2ff) [](https://medium.com/better-programming/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) [## 鲜为人知但功能强大的 Unix 命令来提高您的技术技能

### 带有简单示例的简便 bash 命令

medium.com](https://medium.com/better-programming/lesser-known-but-powerful-unix-commands-to-polish-your-tech-skills-b2ceec3e078a) 

感谢您的阅读和快乐编码！