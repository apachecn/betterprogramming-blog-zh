# 使用 Git 和 Bash 别名提高您的生产力

> 原文：<https://betterprogramming.pub/useful-configs-and-aliases-for-git-and-local-development-environments-b7a0822591a6>

## git 和您的本地开发环境的有用配置和别名

![](img/c905af4c6c98ba17e236851debc9a492.png)

[Yancy Min](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

作为一名懒惰的工程师，我总是在寻找方法、技术和工具来帮助我提高技能，让我更有效地工作(例如，避免敲击一些键盘按键)。我每天都会使用一些工具，有时同一个命令或命令序列每天会被执行多次。Git 是目前管理源代码版本最常用的工具之一。掌握这个工具对于高效工作和更专注于编写源代码而不是执行 git 命令非常重要。

在本文中，我将介绍我在本地开发环境中使用的一些 git 命令和别名，以及我在日常命令行任务中使用的一些别名。

# Git 配置

通常，git 配置可以在`~/.gitconfig`文件中找到。文件的基本结构应该如下所示:

```
[user] : User related configs
[core] : Core Configurations for git
[alias]: Define git command alias
...
```

我将把重点放在 git 的别名部分，并提供一些有用的别名来帮助加速开发过程。这里出现的所有别名都应该添加到`~/.gitconfig`文件的`[alias]` 部分。

用命令`git la`列出所有定义的别名。这个别名允许我查看所有已定义的别名。

```
la = "!git config -l | grep alias | cut -c 7-"
```

# 克隆

*   `git cl ${repo_url}`:使用 repo URL 克隆新的存储库。
*   `git cld ${repo_url}`:使用 repo URL 和深度 1 克隆一个新的存储库(git 历史将不可用)。
*   `git clg ${project_name}`:根据项目名称从 my fork 克隆项目(例如`git clg dotenv`)。

# 检验

*   `git co`或`git co ${file/branch}`:检出一个文件或分支。
*   `git com`:查看当地一家主支行。
*   `git cod`:查看当地开发商分支机构。
*   `git nb ${branch_name}`:新建一个分支，切换到该分支。这也可用于从远程分支创建新分支(如`git nb ${branch_name} ${remote_branch}`)。

# 分支管理

*   `git b`:列出所有本地分支机构。
*   `git br`:列出所有远程分支。
*   `git ba`:列出远程跟踪分支机构和本地分支机构。
*   `git bc`:获取当前分行名称。
*   `git bd ${branch}`:删除本地分行。该分支必须与其上游分支完全合并。
*   `git bdd ${branch}`:即使没有和上游合并，也强制删除本地分支。
*   `git bdr ${branch}`:删除远程分行。

# 将文件添加到登台别名

*   `git a ${filename}`:添加文件内容到索引中。
*   `git ai`:将工作树中修改过的内容交互式地添加到索引中(实际上没用那么多)。
*   `git aa`:将工作树中所有修改的内容添加到索引中。

# 提交管理

*   `git c`:为`git commit`的别名。
*   `git cm ${message}`:用消息提交所有暂存文件。
*   `git cam ${message}`:将所有文件添加到 stage 并通过消息提交它们。一旦我完成了所有的更改，并且它们已经准备好进行交换，我就使用这个命令。它提供了一种简单快速的提交变更的方法。
*   `git amend`:修改上次提交。这有助于更改提交的描述。
*   `git rn ${message}`:添加所有已更改的文件并修改上次提交。每当我需要为分支中的最后一次提交添加新的更改而不是引入单独的提交时，我都会使用这个命令。

# 樱桃采摘

*   `git cp ${commit}`:将提交复制到当前分支。
*   `git cpa`:中止精选。如果有一些冲突，可以使用这个。
*   `git cpc`:继续精选过程。

# 获取远程分支

*   `git f`:为`git fetch`的别名。
*   从`origin`遥控器获取树枝。
*   `git fu`:从`upstream`遥控器取分支。
*   `git fa`:从所有已定义的遥控器获取分支。

# 推送别名

*   `git ps`:为`git push`的别名。
*   `git psf`:力`git push`。
*   `git psu` : `git push`同`set-upstream`。
*   `git pst`:将 git 标签推送到遥控器。
*   `git pso`:推送到`origin`远程仓库。
*   `git psuo`:推送到`origin`远程仓库，设置上游。
*   `git psao`:将所有分支推送到`origin`远程仓库。
*   `git psfo`:强制`git push`到`origin`遥控器。
*   `git psom`:将主分支推至`origin`远程。
*   `git psfom`:强制推动主分支至`origin`远程。
*   `git psuom`:将主分支推至`origin`远程，设置上游。
*   `git psoc`:将当前分支推至`origin`远程。
*   `git psfoc`:强制将当前分支推至`origin`远程。
*   `git psuoc`:将当前支路推至`origin`远程，设置上游。

# 提取别名

*   `git pl`:为`git pull`的别名。
*   `git pr`:用 rebase 拉动。
*   `git proc`:从`origin`远程拉当前支路。
*   `git pruc`:从`upstream`远程拉当前支路。
*   `git pom`:从`origin`遥控器拉出主分支。
*   `git pum`:从`upstream`远程拉动主分支。
*   `git pud`:从`upstream`遥控器拉出显影分支。
*   `git pod`:从`origin`遥控器拉出显影分支。

# 合并和重设基础

*   `git m`:为`git merge`的别名。
*   `git ma`:中止合并过程。
*   `git mc`:继续合并过程。
*   `git ms`:跳过合并过程。
*   `git reb`:T42 的别名。
*   `git reba`:中止重设基础过程。
*   `git rebc`:继续重置基础过程。
*   `git rebs`:跳过重置基础过程。
*   `git rebi`:交互式重置基础过程。

# 日志

*   `git lgc`:显示包含已更改文件的 git 提交日志。
*   `git lgd`:在一行中显示每次提交的 git 提交日志，并注明日期。
*   `git lgn`:每次提交时在一行中显示 git 提交日志。
*   `git lgt`:显示最后一条日志信息。
*   `git lgg`:以图形方式显示 git 提交日志。

# 差速器

*   `git d`:差异未分段变化。
*   `git dc`:差异阶段性变化。
*   `git last`:区分最后提交的变更。

# 状态

*   `git s` : Git 短状态，即使是`short`格式的分支和跟踪信息。
*   `git st`:为`git status`的别名。
*   `git sv`:用详细信息显示状态。

# 远程管理

*   `git r`:为`git remote`的别名。
*   `git ra`:向本地存储库添加一个新的遥控器。
*   `git rr`:从本地存储库中删除一个遥控器。
*   `git rv`:用详细信息显示 git 远程。
*   `git rao`:增加一个遥控器作为`origin`。
*   `git rau`:增加一个遥控器作为`upstream`。

# 其他人

下面是一些其他的 git 别名，涵盖了更多的 git 命令，如`git reset`、`git stash`和`git tag`。

# 命令别名

这里给出的别名是我每天在终端屏幕上使用的命令行工具的别名。为了设置这些别名，我们需要执行两个操作:

*   在文件上定义所有需要的别名，并直接保存在主页上。
*   修改 shell `rc`或`profile`文件以获取`aliases` 文件，使其在打开新的终端或标签**时可用。**

在我的本地机器上，我将所有的别名保存在下面的`~/.aliases`点文件中。因为我使用的是`zsh` shell，所以我修改了`~/.zshrc`来获取`~/.aliases`文件，在文件中添加了下面一行:

```
[ -s "$HOME/.aliases" ] && source "$HOME/.aliases"
```

如果您使用的是不同的 shell，您需要将上面的行添加到属于所用 shell 的`rc`文件中。例如，如果您正在使用`bash`，那么您需要将这一行添加到名为`.bashrc`的文件中。

# 四处移动的别名

## IP 地址

## 打包机

## RubyGems

## 轨道

## 无赖

## 码头工人

## 将（行星）地球化（以适合人类居住）

## 其他人

# 结论

掌握软件开发工具包括以高效、简单和快速的方式使用这些工具(不要浪费时间去敲击更多的键盘键)。别名可以通过引入快捷方式来帮助我们减少复杂、冗长或频繁的命令。

别名并不局限于我在这里介绍的命令。您可以为最常用的命令定义自己的别名。一个好的起点是查看您经常使用的命令的命令行历史。