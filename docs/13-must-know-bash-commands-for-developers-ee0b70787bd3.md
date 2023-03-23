# 开发人员必须知道的 13 个 Bash 命令

> 原文：<https://betterprogramming.pub/13-must-know-bash-commands-for-developers-ee0b70787bd3>

## 提高您的工程效率

![](img/357e93b2be2aae859ca4d5578a9282ad.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/commands?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

每个软件开发人员都必须知道如何使用 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 命令。在某些情况下，一个好的用户界面可能不可用，所以唯一剩下的就是命令行。通过命令行运行命令甚至比通过一些图形界面更快。

Bash 是 Unix 系统中一个强大的工具。通过终端命令，它允许执行许多操作，在某些情况下具有与根用户相同的权限。Bash 如此吸引人和受欢迎的另一个原因是编写脚本和组织一些流的能力。按顺序重复运行命令可以显著减少错误并节省大量工程时间。

下面是最常见的 Bash 命令列表，可以简化工程师的工作。

# 13 个 Bash 命令

## 1.限位开关（Limit Switch）

`ls`列出当前目录中的内容，或者将该目录作为一个参数进行检查。

## 2.激光唱片

`cd`代表*变更目录*。将目录作为参数传递，当前目录将被更改为您提供的目录。例如，命令`cd /private/Applications`会将当前目录更改为`/private/Applications`。

## 3.mkdir

`mkdir`用于创建一个新的目录(文件夹)。应该在该命令后提供目录的名称。

## 4.丙酸纤维素

`cp`将文件从一个目录复制到另一个目录。第一个参数是源文件，第二个参数是目标文件。使用示例将如下所示:`cp readme.txt private/readme.txt`。这将把文件`readme.txt`复制到目录`/private`中，并保持相同的文件名`readme.txt`。

## 5.平均变化

`mv`将文件从一个目录移动到另一个目录。它的语法类似于`cp`命令，我们需要提供源文件和目标文件。

## 6.空间

`rm`删除指定文件。该命令也可用于删除目录，但需要一个附加参数`-r`。例如，命令`rm -r /private`将删除目录`/private`和其中的所有文件。

## 7.猫

`cat` 是*串联*的缩写。这是 Unix 中读写文件的标准工具。要读取文件`readme.txt`，我们需要写`cat readme.txt`，然后文件的内容就会显示出来。要写入文件，使用命令`cat > readme.txt`。如果该文件不存在，将被覆盖或创建一个新文件。使用命令`cat >> readme.txt`将文本添加到文件中。这里的区别仅在于`>`和`>>`操作符。`>`将输出重定向到新文件或覆盖现有文件，而`>>`将输出重定向到新文件或附加到现有文件。

## 8.可做文件内的字符串查找

`grep` 是 Unix 系统中的另一个工具。它是为文件或输入流中的简单文本搜索而设计的。它有许多参数，可以使用正则表达式搜索多个文件。最简单的用法是`grep 'hello' readme.txt`，它在文件`readme.txt`中查找单词 *hello* 。

## 9.xargs

`xargs` 与其他命令配合使用效果最佳。它接受输入并执行命令，向它提供来自输入的参数。最简单的例子就是`echo "new_dir" | xargs mkdir`。文本`new_dir`作为输入传递给`xargs`命令。然后命令`mkdir new_dir`将被执行，一个新的目录`folder1`将被创建。通过管道操作员`|`进行连接。该运算符将一个命令的输出作为输入传递给下一个命令。

## 10.chmod

`chmod` 改变给定文件的权限。在 Unix 系统中，文件可以具有读、写和执行权限。使用此命令，可以设置文件所需的权限。例如，`chmod +w readme.txt`给予文件`readme.txt`写权限，而`chmod -w readme.txt`删除它。

## 11.出口

`export` 列出所有环境变量并设置一个新变量。如果没有提供参数，它将显示所有环境变量。例如，`export JAVA_HOME=/Applications/jre/jdk`将为当前的 Bash 会话设置一个环境变量`JAVA_HOME`。如果我们需要在所有会话中永久使用这个变量，那么我们需要将这个命令放到初始化文件中。(对于 Bash，是`~/.bashrc`。)

## 12.砰

`ping` 是一个简单的命令，用于检查远程资源是否可达。例如，运行`ping google.com`来查看 google.com 网站是否可以接受请求并做出响应。

## 13.卷曲

`curl` 是另一个伟大的工具。它用于通过各种协议向服务器发出请求。众所周知，它通过 HTTP(-S)协议向 REST API 发出请求。最简单的例子是`curl [https://www.google.com](https://www.google.com.)`。它将返回网站的 HTML 响应。这个实用程序是为传输数据而设计的，因此，它具有传输数据的所有功能。设置自定义标题、提交表单数据、流式传输数据只是其中的一部分。

# 结论

这不是所有 Unix 命令的最终列表。每个开发人员自己决定哪些 Bash 命令重要，哪些不重要。这完全取决于任务，是一个习惯和可用性的问题。此外，每个命令都有多个可以显著影响行为的参数。深入研究每个命令的最佳方式是使用参数`--help`运行它。这将返回命令的描述以及所有可能的参数。

学习 Bash 命令并每天应用它们可以提高工程效率。它们应该以简单的方式工作和使用。但是伴随着巨大的权利和机遇而来的总是责任。小心使用它们，因为它们既有用又有害。