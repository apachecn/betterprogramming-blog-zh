# 停止向您的 Git 存储库提交敏感数据

> 原文：<https://betterprogramming.pub/stop-committing-sensitive-data-to-your-git-repository-d2f7dd69232f>

## 如何解决此问题的分步指南

![](img/d70085c1f4493b309ddf49b20ac3c862.png)

照片由 [Pixabay](https://www.pexels.com/es-es/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 在[像素](https://www.pexels.com/es-es/foto/logotipo-de-seguridad-60504/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)上拍摄。

我很少在代码中看到密码或敏感信息。但是我经常看到的是提交给存储库的`.properties`文件中的数据库访问密钥、SSH 密钥、服务器证书密钥或其他资源访问密钥。

将密码等敏感信息放入`.properties`文件或代码本身的问题是，如果有人访问我们的存储库，他们将访问我们的资源。例如，我们确实可以通过 IP 地址来限制访问，但是最好从一开始就做好事情。

您可能没有注意到，从一开始就有一个文件，其中包含您的应用程序的属性以及对数据库或其他资源的访问数据。

在这种情况下，从您的`.properties`文件中删除敏感信息并再次提交给 Git 是不够的，因为 Git 保留了该文件的历史记录，并且该信息仍然在以前的版本中。

# 解决这个烂摊子

一旦我们意识到我们搞砸了，并且我们的密钥遍布 Git 历史，我们只能以最好的方式删除这些密钥。

直到不久前，做这件事还是相当乏味的。要重写 Git 历史，您必须使用命令`[git filter-branch](https://git-scm.com/docs/git-filter-branch)`，它为您更改的现有提交和任何相关提交更改 sha。这样做很痛苦。

另一种更简单快捷的方法是使用由开源社区构建和维护的开源 [BFG 回购清理器](https://rtyley.github.io/bfg-repo-cleaner/)工具。

你应该做的第一件事是为你的回购创建一个备份，以防出现问题。什么都不会发生，但是…你永远不知道。

好了，我们已经创建了包含我们的回购的文件夹的副本，所以让我们继续。

先从删除当前敏感信息开始。

例如，要删除包含敏感数据的文件，并保持最新提交不变，请执行以下操作:

1.  从 GitHub 上的 [BFG 回购清理器下载 jar 文件。](https://rtyley.github.io/bfg-repo-cleaner/)

![](img/7d3d410da4299a2ad5ca43ccca3aae84.png)

可执行的 jar 文件

2.为了方便起见，将其复制到您的项目文件夹中。完成后，您可以删除它，以免将其上传到存储库。

3.从文件中删除敏感数据，如应用程序的数据库密码。

4.提交更改。

5.执行以下命令:

*默认情况下，这个命令从所有 Git 历史(您所在的分支)中删除我们指定为参数的文件。*

执行该实用程序。

6.现在，您必须将您的更改强制推送至您的存储库:

强制推送

7.如果您现在检查文件在 Git 历史中的更改，您将会看到您的密码已经消失，因为 Git 历史中唯一保留的记录是您最后一次提交的内容。

8.将应用程序配置为从例如外部文件中读取这些属性。

注意:默认情况下，BFG 实用程序不会修改主分支上最新提交的内容，即使它会清除之前的所有提交。

这样做是为了避免中断应用程序部署，因为最后一次提交是我们在运行应用程序时使用的。因此，该工具不会通过回购历史将其删除。

总之，我们遵循了以下步骤:

*   我们从文件中删除了敏感信息，然后在没有该信息的情况下提交。
*   我们清理了 Git 历史，将这些属性和敏感数据提取到 Git 存储库外部的一个外部文件中，最后配置应用程序来使用它们。例如，您可以将属性文件存储在部署应用程序的机器上，但是要记住保护这个文件。

# 结论

犯这个错误是经常发生的事情。很容易忘记将项目属性保存在项目本身中，然后提交给 g it 的风险。

还有其他方法可以解决这个问题，比如使用 Git 命令`git filter-branch`，但是这要繁琐复杂得多。在本文中，我展示了使用 BFG 工具解决这个问题是多么容易。

不管怎样，如果你想在你的项目中包含敏感信息的文件，你可以通过使用`[gitignore](https://git-scm.com/docs/gitignore)`命令忽略它，永远不要把这种类型的文件提交给 Git repo(例如，我们可以忽略所有带有`.properties`扩展名的文件)。

我希望你觉得这有用！