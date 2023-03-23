# 如何创建高效的 Bash 命令别名

> 原文：<https://betterprogramming.pub/how-to-create-productive-bash-command-aliases-c5418f9ffa6e>

## 加上一个流行和有用的别名列表

![](img/b88e0d0ae9e46d49d8aa928d1482d36a.png)

键入命令从来没有比使用别名更容易。现在，您可以使用这些别名来减少键入时间，灵活地工作，并在命令提示符下提高生产率。

如果您发现自己一遍又一遍地键入相同的命令，那么您就会知道键入这些小命令是多么的多余和耗时。

幸运的是，您可以通过创建别名来缩短您键入的命令的长度，从而摆脱这种情况。

命令别名是功能强大的命令，它使我们作为软件开发人员、系统管理员、数据科学家或开发人员的日常工作变得更加容易和快捷，因为我们生活在命令行中。

这实际上是各种命令 shells 最有用、最强大的特性之一。如果您几乎对所有事情都使用命令行，那么您可能每天都在重复使用相同的命令。使用别名，您可以使这些日常任务更容易运行。

alias 命令允许您通过使用在`.bashrc`、`.zschrc`、`.tcshrc`、`.cshrc`或任何其他来源文件中定义的缩写词或单个字符来运行任何命令或命令组(带选项和文件名)。

# 如何创建命令别名

命令别名只是运行命令的快捷方式名称。它们由一个单词甚至一个字母组成，您可以使用它来代替键入更长的命令。

为了创建一个别名，我们将使用`alias`命令，该命令用于创建别名。下面是语法:

```
alias name='command'
alias name='command arg1 arg2'
```

在上面的语法中:

1.  `alias`是创建命令别名的命令
2.  `name`是我们将称之为命令别名的名称
3.  `command`是我们键入别名命令时要运行的命令

所以有了这些，我们可以创建一个简单的例子，如下:

```
**alias** clr='clear'
```

在上面的例子中，我们使用命令别名将我们的`clear`(清除我们的命令行)命令缩短为`clr`。所以当你现在输入`clr`时，它调用`clear`命令并清除我们的命令。

您甚至可以将其缩短为`c`，使其成为一个单字母命令。是不是很酷？

现在，如果您运行上面的命令，您将只能在当前命令行中使用它，并且它只在当前登录会话期间有效。一旦您注销或重新启动系统，别名将消失。您可以通过将其永久化来解决这个问题。

为此，您需要在 shell/terminal settings/config/profile 文件中定义 alias 命令。大多数系统范围的别名通常在`/etc/bashrc`文件中定义，但是在某些情况下，您仍然需要在 shell 配置文件中重新定义它。

别名命令内置于各种 shells 中，包括`ksh`、`tcsh` / `csh`、`ash`、`bash`等。如果您正在使用下面的任何 shells，下面是您应该查找的文件:

1.  猛击:`.bashrc`，`.bash_aliases`
2.  Zsh: `.zshrc`
3.  鱼:`.config/fish/fish.config`
4.  Csh: `.cshrc`
5.  Tcsh: `.tcshrc`
6.  Ksh: `.kshrc`
7.  Ash: `.ashrc`

因此，例如，如果您使用默认的 bash，您可以将别名添加到您的`~/.bashrc`文件中。输入:

```
vi ~/.bashrc
```

然后，您现在可以将下面一行添加到文件中:

```
alias clr=’clear’
```

保存并关闭文件。

现在，使用以下命令重新加载`.bashrc`文件:

```
$ source ~/.bashrc
```

有了这个，你现在可以永远随时运行`clr`(只要你的系统还活着)。

要删除别名，您可以运行`unalias`命令，如下所示…

```
unalias <alias_name>
unalias -a # to remove all
```

…或者从文件中删除命令，并使用`source`重新加载。

# 命令别名指南

在您开始向终端添加新的命令别名之前，有几件事情您需要知道并遵守。在许多方面，别名使得执行许多任务变得更加容易和高效。但是它可能会使您最终不再记得实际的命令和选项，或者可能会在现有的关键系统命令之间产生冲突。

为了避免这种情况，下面是创建命令别名时要遵守的一组通用规则:

*   在打字方面应该有一些实质性的缩短。将四个字母的命令转换成两个字母几乎不值得。没有必要为`ls`创建一个`l`别名。
*   您不应该为每个可能的命令行选项创建单独的别名
*   不要因为可以重命名命令就重命名命令。比如，不要把`ls`改名为`l`。
*   这些应该是你(半)经常使用的命令，而不是你很少使用的命令。反正你会忘记你创造了一个化名。
*   当心单字母别名——你可能会不小心引发它。使用类似`r`的东西来关机是个坏主意。
*   如果可能，保留命令的名称。你只是添加了一些你可能会忘记的选项(例如，`cp -iv`可以作为`cp` 的别名，而不是`CPV`或`c`)。
*   话虽如此，如果您偶尔还想在没有指定选项的情况下执行原始命令，请稍微更改或修改命令名
*   如果不是一行程序，请尝试 shell 脚本而不是别名

# 流行的命令别名

您可以创建大量的命令别名，这实际上取决于您的创造力和您最常用的命令。

以下是常用的命令别名:

```
**alias** pl='pwd; ls'
**alias** dirs="ls -al | grep '^d'"
**alias** lf="ls -aFG"
**alias** lm="ls -al|more"
**alias** h="history"
**alias** clr="clear" # Clear your terminal screen
**alias** ip="curl icanhazip.com" # Your public IP address# MS-DOS
**alias** dir="ls"
**alias** copy="cp"
**alias** rename="mv"
**alias** md="mkdir"
**alias** rd="rmdir"
**alias** del="rm -i"# GIT
**alias** ga='git add'
**alias** gaa='git add .'
**alias** gau='git add --update'
**alias** gb='git branch'
**alias** gbd='git branch --delete '
**alias** gc='git commit'
**alias** gcm='git commit --message'
**alias** gcf='git commit --fixup'
**alias** gco='git checkout'
**alias** gcob='git checkout -b'
**alias** gcom='git checkout master'
**alias** gcos='git checkout staging'
**alias** gcod='git checkout develop'
**alias** gd='git diff'
**alias** gda='git diff HEAD'
**alias** glg='git log --graph --oneline --decorate --all'
**alias** gld='git log --pretty=format:"%h %ad %s" --date=short --all'
**alias** gm='git merge --no-ff'
**alias** gma='git merge --abort'
**alias** gmc='git merge --continue'
**alias** gnit='git init'
**alias** gp='git push'
**alias** gpl='git pull'
**alias** gpom="git push origin master"
**alias** gpr='git pull --rebase'
**alias** gr='git rebase'
**alias** gs='git status'
**alias** gss='git status --short'
**alias** gst='git stash'
**alias** gsta='git stash apply'
**alias** gstd='git stash drop'
**alias** gstl='git stash list'
**alias** gstp='git stash pop'
**alias** gsts='git stash save'# NPM
**alias** ni='npm install'
**alias** nis='npm install --save'*## get rid of command not found ##***alias** cd..='cd ..'

*## a quick way to get out of current directory ##***alias** ..='cd ..'
**alias** ...='cd ../../../'
**alias** ....='cd ../../../../'
**alias** .....='cd ../../../../'
**alias** .4='cd ../../../../'
**alias** .5='cd ../../../../..'**alias** path='echo -e ${PATH//:/\\n}'
**alias** now='date +"%T"'
**alias** curdate='date +"%d-%m-%Y"'*## Browsers ##*
**alias** firefox='/opt/firefox13/firefox'
**alias** chrome='/opt/google/chrome/chrome'
**alias** opera='/opt/opera/opera'

*#default ff*
**alias** ff=firefox

*#default browser*
**alias** browser=chrome## pass options to free ##
**alias** meminfo='free -m -l -t'

## get top process eating memory
**alias** psmem='ps auxf | sort -nr -k 4'
**alias** psmem10='ps auxf | sort -nr -k 4 | head -10'

## get top process eating cpu ##
**alias** pscpu='ps auxf | sort -nr -k 3'
**alias** pscpu10='ps auxf | sort -nr -k 3 | head -10'

## get GPU ram on desktop / laptop##
**alias** gpumeminfo='grep -i --color memory /var/log/Xorg.0.log'
```

# 结论

命令别名是减少输入和记忆命令所花时间的好方法。它提高了您在 shell/终端上的工作效率。

然而，由于别名的优点，您不应该仅仅因为可以创建任何命令别名就可以覆盖其他命令。

感谢阅读！