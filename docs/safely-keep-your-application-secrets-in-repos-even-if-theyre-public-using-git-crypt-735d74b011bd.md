# 使用 Git-Crypt 在 Repos 中安全地保存您的应用程序秘密，即使它们是公开的

> 原文：<https://betterprogramming.pub/safely-keep-your-application-secrets-in-repos-even-if-theyre-public-using-git-crypt-735d74b011bd>

## 秘密处理的另一种方法

![](img/a3ec4a486a4bf2ba2492339b442d39d6.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

许多软件项目使用秘密，通常是外部 API 的密钥或凭证来访问外部资源，如数据库。您的应用程序在运行时需要这些密钥，因此您需要能够在部署应用程序时提供它们，或者作为准备部署环境的一个步骤。

在本文中，我将向您展示如何使用 [git-crypt](https://github.com/AGWA/git-crypt) ，以便您可以安全地将您的应用程序秘密保存在您的源代码库中——即使它们是公开的。

# 应用程序机密的问题是

大多数项目都有某种秘密密钥或凭证。例如，如果您的应用程序托管在 [Heroku](https://www.heroku.com) 上，您可以使用如下命令向 Heroku 应用程序提供一个 API 密钥:

```
$ heroku config:set API_KEY=my-sooper-sekrit-api-key
```

通过在(重新)部署应用程序之前运行这个命令，您可以在运行时给它一个名为`API_KEY`的环境变量*和值`my-sooper-sekrit-api-key`。然而，在 Heroku 的*之外(或者您部署应用程序的任何地方)跟踪这些秘密值*仍然是一个挑战。*

我总是试图设置我的项目，以便我可以运行一个命令来从头开始部署它们，而不需要任何单独的手动步骤。对于我们的例子，这意味着我需要将值`my-sooper-sekrit-api-key` *存储在某个地方*，以便我的部署代码可以使用它(在本例中，运行上面的`heroku config:set...`命令)。

我的项目源代码总是存储在 Git 中，通常托管在 GitHub、Bitbucket 或其他一些源代码托管服务上。我*可以*在我的源代码库中存储我的`API_KEY`值。然而，这也有一些缺点:

*   我不能和任何人分享我的知识库，除非我对他们访问我的秘密感到舒服。这意味着我所有包含秘密的应用程序存储库都必须是私有的。
*   据推测，GitHub/bit bucket/anywhere 的许多工作人员也会接触到我的秘密，这我可能会不同意(取决于秘密)。
*   如果我后来选择公开，很容易忘记私有存储库中的秘密。所以我可能会不小心泄露重要的秘密。

我可以将我的秘密存储在与我的应用程序源代码分开的地方，但是这有它自己的问题:

*   我需要一种方法从部署时或部署前存储的任何地方获取我的秘密，并允许我的部署代码访问它们。
*   我的秘密可能没有我的源代码存储得那么可靠。例如，我可以将秘密保存在笔记本电脑上的一个`.env`文件中，并确保我永远不会将它签入 Git 存储库。但是，如果我丢失了该文件(例如，如果我的笔记本电脑损坏/被盗)，那么我也就失去了那个秘密。

# git-crypt

Git-crypt 旨在解决这个问题，每当您将秘密推送到您的 Git 存储库时对它们进行加密，每当您取出它们时对它们进行解密。从你的角度来看，这是显而易见的。因此，就您和您的部署代码而言，这些秘密是明文形式的，但是其他人无法阅读它们——即使您的源代码在公共 GitHub 存储库中。

让我们看一个例子。

## 1.安装 git-crypt

在 [git-crypt 安装页面](https://github.com/AGWA/git-crypt/blob/master/INSTALL.md)上有针对 Linux、Mac 和 Windows 的说明。

如果像我一样，你正在使用安装了[家酿](https://brew.sh/)的 Mac，你可以运行:

```
$ brew install git-crypt
```

## 2.创建新的 Git 存储库

```
$ mkdir myproject 
$ cd myproject 
$ git init 
$ echo "This is some text" > file.txt 
$ git add file.txt 
$ git commit -m "Initial commit"
```

现在我们有了一个包含单个文本文件的 Git 存储库。

## 3.设置存储库以使用 git-crypt

```
$ git-crypt init
```

您应该会看到输出:

```
Generating key...
```

在我们做任何其他事情之前，请运行以下命令:

```
$ git-crypt export-key ../git-crypt-key
```

这个命令创建了为这个存储库生成的 git-crypt 对称密钥的副本。我们把它放在这个存储库上面的目录中，这样我们就可以在多个 g it 存储库中重用同一个密钥。

默认情况下，git-crypt 将生成的密钥存储在文件`.git/git-crypt/keys/default`中，因此您可以通过运行`cp .git/git-crypt/keys/default ../git-crypt-key`获得相同的结果。

这个`git-crypt-key`文件很重要。这是能解开我们仓库里所有加密文件的钥匙。我们稍后会看到如何使用这个键。

## 4.告诉 git-crypt 加密哪些文件

假设我们的应用程序需要一个 API 密匙，我们希望将它存储在一个名为`api.key`的文件中。

在我们将该文件添加到我们的存储库之前，我们将告诉 git-crypt，我们希望在提交时对`api.key`文件进行加密。

我们通过使用`.gitattributes`文件来实现。这是一个我们可以用来向 Git 存储库添加额外元数据的文件。它不是特定于 git-crypt 的，所以您的存储库中可能已经有了一个`.gitattributes`文件。如果有，就添加相关的行。不要替换整个文件。

在我们的例子中，我们没有一个`.gitattributes`文件，所以我们需要创建一个。`.gitattributes`文件包含以下形式的行:

```
[file pattern] attr1=value1 attr2=value2
```

对于 git-crypt，文件模式需要匹配我们希望 git-crypt 加密的所有文件，并且属性总是相同的:`filter`和`diff`，这两个属性我们都设置为`git-crypt`。

因此，我们的`.gitattributes`文件应该包含以下内容:

```
api.key filter=git-crypt diff=git-crypt
```

创建该文件，并将其添加并提交到您的 Git 存储库:

```
$ echo "api.key filter=git-crypt diff=git-crypt" > .gitattributes 
$ git add .gitattributes 
$ git commit -m "Tell git-crypt to encrypt api.key"
```

我在我的`.gitattributes`文件中使用了字面文件名`api.key`，但是它可以是包含您想要加密的文件的任何文件模式，所以我可以使用`*.key`，例如。或者，您可以为每个要加密的文件添加一行。

如果您试图用一个模式条目保护几个文件，那么很容易在您的`.gitattributes`文件中出错。因此，我强烈推荐阅读 [git-crypt 自述文件](https://github.com/AGWA/git-crypt#gitattributes-file)的这一部分，它强调了一些常见的问题。

## 5.添加一个秘密

现在我们已经告诉 git-crypt 我们想要加密`api.key`文件，让我们将它添加到我们的存储库中。

在提交真正的秘密之前，先添加一个虚拟值并确认它已成功加密，这是测试您的设置的一个好主意。

```
$ echo "dummy value" > api.key
```

我们还没有将`api.key`添加到 Git 中，但是我们可以通过运行以下命令来检查*要*做什么:

```
$ git-crypt status
```

您应该会看到以下输出:

```
encrypted: api.key 
not encrypted: .gitattributes 
not encrypted: file.txt
```

因此，即使`api.key`文件还没有提交到我们的 Git 存储库中，这也告诉您 git-crypt 会为您加密它。

让我们添加并提交文件:

```
$ git add api.key 
$ git commit -m "Added the API key file"
```

## 6.确认我们的秘密被加密了

我们已经告诉 git-crypt 加密`api.key`，并且我们已经将`api.key`添加到我们的存储库中。然而，如果我们看一下`api.key`，似乎没有什么不同:

```
$ cat api.key 
dummy value
```

原因是 git-crypt 透明地加密和解密文件，就像你把它们推入和拉出你的仓库一样。因此，`api.key`文件看起来像一个普通的明文文件。

```
$ file api.key 
api.key: ASCII text
```

确认你的文件真的被加密了的一个方法是把你的库推到 GitHub。当您使用 GitHub web 界面查看`api.key`文件时，您会看到它是一个加密的二进制文件，而不是文本。

一种更简单的方法是运行以下命令，看看没有解密密钥的存储库会是什么样子:

```
$ git-crypt lock
```

现在，如果我们看一下我们的`api.key`文件，事情就不同了:

```
$ file api.key 
api.key: data $ cat api.key 
GITCRYPTROܮ7y\R*^
```

您将看到一些与我得到的不同的垃圾输出，但很明显文件是加密的。这是存储在 GitHub 上的内容。

要恢复明文`api.key`文件，运行:

```
$ git-crypt unlock ../git-crypt-key
```

`../git-crypt-key`文件是我们之前使用`git-crypt export-key...`保存的文件。

# 检查站

让我们快速回顾一下我们现在所处的位置:

*   使用`git-crypt init`在 git 存储库上初始化 git-crypt。
*   使用`.gitattributes`中的文件模式告诉 git-crypt 加密哪些文件。
*   将加密我们存储库中所有指定的文件。
*   `git-crypt unlock [path to keyfile]`将解密加密的文件。

`git-crypt-key`文件很重要。没有它，您将无法解密存储库中的任何加密文件。拥有该文件副本的任何人都可以访问您存储库中的所有加密机密。所以你需要保证文件的安全。

# 重用您的 git-crypt 密钥文件

我们使用`git-crypt init`和`git-crypt export-key`来创建我们的`git-crypt-key`文件。但是，如果我们必须为我们的每个存储库拥有一个单独的密钥文件，那么我们就没有很好地改进我们的秘密管理。

幸运的是，对多个 git 存储库使用相同的 git-crypt 密钥文件非常容易。

要使用现有的密钥文件，只需在设置 git 存储库以使用 git-crypt 时使用`git-crypt unlock`而不是`git-crypt init`，如下所示:

```
$ mkdir my-other-project   # At the same directory level as `myproject` 
$ cd my-other-project 
$ git init 
$ echo "Something" > file.txt 
$ git add file.txt 
$ git commit -m "initial commit" 
$ git-crypt unlock ../git-crypt-key
```

如果在将任何文件添加到 Git 存储库之前运行`git-crypt unlock`命令，您将看到如下消息:

```
fatal: You are on a branch yet to be born 
Error: 'git checkout' failed 
git-crypt has been set up but existing encrypted files have not been decrypted
```

这仍然工作得很好，但是有点混乱，所以我确保在运行`git-crypt unlock...`之前添加并提交至少一个文件。

重用您的 git-crypt 密钥文件很方便，但这确实意味着如果其他人得到了您的密钥文件的副本，*您所有的加密秘密就会暴露。*

这与使用像 [LastPass](https://www.lastpass.com/) 或 [1password](https://1password.com/) 这样的密码管理器是同样的安全权衡。你不用管理多个秘密(密码)，每个都有暴露的风险，而是把它们都放在一个安全的地方，用一个主密码来打开那个的*。*

这里的想法是，管理一个重要的秘密比管理许多次要的秘密更容易。

# 何时不使用 git-crypt

Git-crypt 是一种很好的方法，可以将应用程序需要的秘密保存在 Git 存储库中，与应用程序源代码放在一起。然而，就像其他安全措施一样，并不是*总是*合适或明智的。

以下是一些需要考虑的事项，以确定它是否是您特定项目的正确解决方案:

*   git-crypt 是为 git 存储库中的大多数文件可以保持明文形式的情况而设计的，您只需要加密一些包含秘密的文件。如果您需要加密存储库中的大部分或所有文件，那么其他解决方案可能更合适。
*   一旦某人有了密钥文件，就没有简单的方法来撤销对存储库中秘密的访问，也没有简单的方法来轮换(即替换)密钥文件(尽管更改 git-crypt 密钥文件没有多大帮助，除非您也轮换存储库中的所有实际秘密)。
*   git-crypt 只加密文件的*内容*。因此，如果您的存储库的*元数据*也是敏感的(例如，文件名、修改日期、提交消息等等)，这就不合适了。
*   一些 GUI Git 应用程序可能无法可靠地使用 git-crypt。*注:虽然自述中提到的 Atlassian SourceTree 的具体情况，已经被* [*固定*](https://jira.atlassian.com/browse/SRCTREE-2511) *。*

在 [git-crypt 自述文件](https://github.com/AGWA/git-crypt#limitations)的这一部分有更多的信息。

# 使用 git-crypt 的更好方法

与直接管理 git-crypt 密钥文件相比，有一种更好的方法来管理加密的存储库:将 git-crypt 与 [gpg](https://gnupg.org/) 集成，这样您就可以使用 gpg 私钥来解密 git 存储库。这也允许您向 Git 存储库添加多个合作者，而无需在各方之间传递任何秘密。

然而，这需要更复杂的设置，所以我们将在另一篇文章中讨论。