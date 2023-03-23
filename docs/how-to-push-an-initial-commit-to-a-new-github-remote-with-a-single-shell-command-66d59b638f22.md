# 如何用一个 Shell 命令将初始提交推送到新的 GitHub Remote

> 原文：<https://betterprogramming.pub/how-to-push-an-initial-commit-to-a-new-github-remote-with-a-single-shell-command-66d59b638f22>

## 别名降低了使用命令行界面的复杂性

![](img/5035199f22c29f3f4ed9281af8d2c080.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=161382)

我不是 CLI 或命令行界面的狂热爱好者，但是这一次我决定通过减少建立良好的源代码控制实践的努力来挑战自己。我通常拒绝 Xcode 将新项目置于源代码控制之下的提议，因为我经常尝试，不一定想要早期原型的记录。

但是，让没有源代码控制的项目不仅有本地提交，而且还被推送到远程存储库，这是非常复杂的。

直到现在…

# 用自制软件安装 GitHub CLI

如果您还没有安装 Homebrew，请打开“终端”并粘贴以下命令:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

一旦你有了自制软件，运行这个来安装 GitHub CLI:

```
brew install gh
```

安装 GitHub CLI 后，运行以下命令登录:

```
gh auth login
```

我使用 HTTPS 并通过浏览器进行验证，但您可能会选择不同的方式。

# 添加 GitHub CLI 别名

该命令将添加 GitHub CLI 所需的唯一别名:

```
gh alias set new 'repo create --private --push --source=.'
```

这只是创建一个私有的远程存储库，并推送它在当前文件夹中找到的任何内容。

运行`gh new`命令将创建远程并推送您的提交(如果您已经有一个本地存储库)。如果没有本地存储库，该命令将会失败，理由是该目录还不是 git 存储库。

# 添加 Git 别名

运行以下命令，向全局`~/.gitconfig` 文件添加一个新的 Git 别名:

```
git config --global alias.new '!new() { cd -- ${GIT_PREFIX:-.}; if getopts "rf:" arg; then mkdir "$OPTARG"; cd "$OPTARG"; fi; touch README.md; git init; git add .; git commit -m "Initial commit"; }; new'
```

感叹号(！)允许使用非 git 命令，但是它也将 shell 的默认位置更改为用户的主文件夹。

要解决这个问题，我们只需将目录更改为`GIT_PREFIX:-.`，这是调用 git 命令的地方。

```
git new
```

这个新别名允许您在当前文件夹中创建新的本地存储库，而不管它是包含文件还是空的。

```
git new -f MyNewProject
```

运行这个命令会创建一个名为`MyNewProject`的新的子文件夹。

子文件夹参数作为`$OPTARG`传递，因为它是`-f`选项字母之后的第一个参数。如果没有传递`-f`选项，那么任何其他参数都将被忽略，该命令的运行方式与当前文件夹中的`git new`相同，不会创建子文件夹。

# 组合别名

我考虑过各种方法来将别名组合成一个，一个接一个地调用它们，但是我认为在很多情况下一次做所有的事情可能是多余的。

假设我想用本地 repo 创建一个新的子文件夹，然后将它推送到一个新的 GitHub remote:

```
git new -f MyNewProject && cd MyNewProject && gh new
```

返回到我第一次调用命令的目录，这意味着如果我创建了一个子文件夹，我需要手动进入它。

如果我不想创建子文件夹，而是想在当前文件夹中创建一个新的存储库，我只需运行以下命令:

```
git new && gh new
```

如果您真的想将此组合添加为 Z-Shell 别名，请运行以下命令:

```
echo -n 'alias newgit="git new && gh new"' >> ~/.zshrc; source ~/.zshrc
```

只要记住这个`newgit`别名只能在你想用它的文件夹中调用。

你不能给它一个子文件夹名，因为它将在原始文件夹而不是子文件夹中运行`gh new`。