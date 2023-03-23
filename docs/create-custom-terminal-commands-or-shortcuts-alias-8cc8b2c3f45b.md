# 创建自定义终端命令(或别名)的两种方法

> 原文：<https://betterprogramming.pub/create-custom-terminal-commands-or-shortcuts-alias-8cc8b2c3f45b>

## 通过创建自定义终端命令来避免重复的命令

![](img/1939150f2becca855eefc4417147b35d.png)

在 [Unsplash](https://unsplash.com/s/photos/hacking?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Boitumelo Phetla](https://unsplash.com/@writecodenow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

当我们在软件上工作或在网络上查看某些东西时，我们会定期运行一些终端命令。有时候这些命令可能会很烦人。

我将从两个方面解释我们如何做到这一点。

# **创建自定义命令文件**

第一种方法，您可以创建一个定制的 shell 脚本文件，然后您可以添加定制的命令作为函数。让我们开始吧。

打开你的终端，然后进入根目录，激活用户。可以用 cd ~(option + N)去目录。

我们将在根目录下工作，因为。bash_profile 文件放在这里。顺便说一下，这些文件有一个。在它们名字的开头，你可以用 ls -a 命令列出它们。

无论如何，我们将创建一个文件来存储我们的定制命令。

```
touch ~/.custom_bash_commands.sh
```

使用此命令，将创建您的自定义命令文件。你可以再举一个例子。

通过下面的命令打开文件。这将使用“文本编辑”默认的 Mac 文本编辑器打开文件。你也可以放另一个编辑器名，比如 Sublime Text。

```
open .custom_bash_commands.sh -a “TextEdit”
```

将以下代码添加到文件中，然后保存。这将打印你好+你写的参数。

```
#!/bin/bashfunction hello() {
 echo “hello” $1 “!”
}
```

然后我们需要通过下面的命令在不重启终端的情况下对此进行评估。如果你不想这样，你可以重启你的终端。

```
source .custom_bash_commands.sh
```

回到终端，然后运行如下所示的命令。顺便说一下，你可以在任何你想去的地方运行它。

```
hello world
```

输出:hello world！

好工作。您创建了自定义命令。

现在，我将给出自定义命令的一个真实实例。如果你使用 git(版本控制系统),你必须运行一些烦人的 Git 命令。比如 git add。和 git commit-m“message”，和 git push origin master。

现在，我们可以合并这些并减少它们。

```
function commit_changes() {
  git add .
  git commit -m “$1”
  git push origin $2
}
```

将上述函数添加到您的自定义命令文件中，然后保存该文件。运行以下代码进行评估。bash_profile

```
source .custom_bash_commands.sh
```

然后运行 commit_changes 命令，如下所示。这将用 commit_message 将您的更改提交并推送到主分支。

```
commit_changes commit_message master
```

好工作。现在，回到第二种方式。

# **创建自定义别名**

第二种方法是创建自定义别名。使用别名前缀，您可以自定义命令。我将在上面提到的同一个例子中解释别名。

```
alias commit_changes =”git add . && git commit -m ‘$1’ && git push origin $2”
```

然后把它和。bash_profile。

```
source .custom_bash_commands.sh
```

无需创建自定义命令文件，也可以将其添加到。bash_profile 文件。不要忘记运行下面的命令。

```
source .bash_profile
```

别名的另一个简单例子，你可以像下面这样做

```
alias go_project_dir = “cd /Users/Desktop/project/”
```

这些都取决于你。您可以随意定制命令。

请注意，编写 shell 脚本很有趣。这些有助于你的工作。请随意使用它们。

[![](img/b9d6d295e12a7a764735723db8274e34.png)](https://www.buymeacoffee.com/batikan)