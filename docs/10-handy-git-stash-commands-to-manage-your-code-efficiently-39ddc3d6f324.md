# 10 个方便的 Git-Stash 命令来有效地管理您的代码

> 原文：<https://betterprogramming.pub/10-handy-git-stash-commands-to-manage-your-code-efficiently-39ddc3d6f324>

## 从基础到高级 git-stash 用例

![](img/beee9d1ebcdca3705f98b539e9094762.png)

Arnold Francisca 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

[Git](https://git-scm.com/) 无疑是使用最广泛的版本控制系统之一。

如果您曾经使用过 Git，您可能使用过`[git-stash](https://git-scm.com/docs/git-stash)`命令。它将您的本地修改保存在一个未完成更改的堆栈中。你可以做些别的事情，然后轻松地重新应用这些变化。当您必须快速切换到另一个分支，但又不想丢失当前的所有工作时，这很有用。

在这个简短的教程中，我们将探索一些基本的和更高级的`git-stash`用法，这些用法可以在管理代码时节省您的时间和精力。例如，列出 stashes 而不应用更改，仅取消 stashes 特定文件，等等。

我们开始吧！

# 用例

## 1.创建一个仓库

要将您的修改保存到存储区，请键入:

```
$ git stash
```

新创建的(未跟踪的)文件呢？它们保留在工作树中。如果你也想把它们藏起来，添加`— all`选项:

```
$ git stash --all
```

## 2.列出所有藏匿处

```
$ git stash liststash@{0}: On master: demo_master2
stash@{1}: On test: demo_test
stash@{2}: On master: demo_master
```

在上面的例子中，我修改了名为`test` 和`master`的两个分支中的文件。

## 3.应用隐藏

应用最新的存储:

```
$ git stash apply
```

要应用特定的存储，您必须提供存储 ID:

```
$ git stash apply stash@{0}
```

这将把 ID 为`0`的 stash 应用到工作树，而不会从 stash 列表中删除它。

## 4.打开一个储藏室

如果您想要应用更改并从存储列表中删除存储，请使用`git-stash pop`:

```
$ git stash pop stash@{0}
```

注意，如果不提供特定的 stash ID，它将从列表中删除最上面的 stash:

```
$ git stash pop
```

## 5.显示更改而不应用它们

有时我们只想看看代码中有什么变化，而不需要重新应用它。`show`命令为我们做了这件事。

要显示最后一次存储的更改:

```
$ git stash show
```

它会打印出这样的内容:

```
.../main/java/jpa/AddressJpaController.java
 | 2 +- 1 file changed, 1 insertion(+), 1 deletion(-)
```

要查看最近存储的内容:

```
$ git stash show -p
```

结果会是这样的:

注意，为了简洁起见，我缩短了输出。

对我来说，更有用的是查看特定存储中的修改。我只需要提供想要的藏匿 ID:

```
$ git stash show -p stash@{1}
```

示例输出:

您将能够在您的终端中修改更改。

## 6.从 stash 创建一个新分支

有时，当您试图应用一个存储时，您可能会遇到冲突，因为您运行存储的分支已经发生了显著的变化。`git stash apply`操作将会失败。

在这种情况下，您可以通过创建一个包含所需存储更改的新分支来避免冲突。`git stash branch`命令恢复原来隐藏的状态，没有冲突:

```
$ git stash branch new_branch stash@{2}
```

注意，这个命令将从列表中删除存储，就像`git stash pop`命令一样。

如果您没有提供一个存储 ID，它将应用存储列表中的最新 ID。

## 7.显示修改过的文件名

要查看哪些文件已更改，请将`— name-only`选项添加到`git stash show`:

```
$ git stash show --name-onlymyproject/src/main/java/AddressJpaController.java
```

## 8.应用特定文件中隐藏的更改

假设您已经对几个文件进行了更改并隐藏了它们。稍后，您希望重新应用更改，但只能从某个/某些文件中应用。

一种方法是应用所有的存储，提交所需的文件，并取消其他文件的存储。但是有一种更有效的方法。您可以使用`[restore](https://git-scm.com/docs/git-restore`)`命令(从 Git 2.23+开始可用)并提供文件名:

```
$ git restore -s stash@{0} -- <filename>
```

您已经学习了如何获取修改过的文件的名称。所以，让我们来试试:

```
$ git stash show --name-only stash@{1}myproject/src/main/java/jpa/AgeController.java
myproject/src/main/java/jpa/AddressJpaController.java 
```

卸载特定文件:

```
$ git restore -s stash@{1} -- src/main/java/jpa/AgeController.java
```

## 9.删除所有隐藏条目

如果你想清理你的仓库，使用`clear`命令。请注意，此操作可能无法恢复。

```
$ git stash clear
```

## 10.删除单个存储条目

`drop`命令从堆栈中删除最新的存储:

```
$ git stash drop
```

您还可以提供一个 ID 来删除特定的存储:

```
$ git stash drop stash@{2}
```

再次提醒，小心行事，因为可能无法找回藏匿的毒品。

# 结论

我们已经看到,`git-stash`命令是管理我们的代码和保持我们的提交历史干净的一种方便的方式。了解如何在各种情况下使用该命令以充分利用它是值得的。

我希望你今天学到了新的有用的东西。感谢您的阅读，下次再见！