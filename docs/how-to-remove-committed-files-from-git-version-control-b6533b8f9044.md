# 如何从 Git 版本控制中移除提交的文件

> 原文：<https://betterprogramming.pub/how-to-remove-committed-files-from-git-version-control-b6533b8f9044>

## 这样做时，不要删除不应该推送到存储库的文件

![](img/d412c99ccfd8c265ac7775a846ff9146.png)

照片由 [Yancy Min](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们中的大多数人可能已经将我们不想要的东西提交给了存储库，甚至可能是公共的存储库:凭证、构建文件、临时文件。潜在问题:

*   已发布的敏感数据(例如，包含数据库登录凭据或 API 密钥的环境文件)
*   建立不需要就定期更改的文件
*   特定于用户的文件(例如，个人 IDE 设置)

一个很好的例子是。如果您使用 JetBrains 制作的 ide(例如 IntelliJ idea)，则会创建 IDEA 文件夹。让我们假设有人不小心添加了这个文件夹，这使得它受到版本控制。每当有人对他的个人 IntelliJ 配置进行更改时，Git 都会告诉我们已经更改了这个文件。但是其他开发者不应该被迫使用相同的配置。此外，这使得合并/重定基础更加困难。

因此，我想从版本控制中删除这个文件夹，并确保没有人可以再将它添加到存储库中。但是，我不希望它从我的本地机器上被删除，因为需要这个文件夹。因此，我们不能简单地删除文件并提交它，因为这也会在本地删除这些文件。

因此，我们需要一种方法，在不修改本地数据的情况下，从版本控制中删除提交的文件。幸运的是，使用 Git，我们可以很容易地解决这个问题。

![](img/f5869f729b60d6d3e7bf9c0ab39d7eb3.png)

## 如何从 Git 版本控制中移除提交的文件

1.  创建一个`.gitignore`文件，如果你还没有的话
2.  编辑`.gitignore`以匹配您想要忽略的文件/文件夹
3.  执行以下命令:`git rm --cached *path/to/file*`。Git 将列出它已经删除的文件。如果您想要保留本地副本，但是要从存储库中删除它，那么应该使用`--cached`标志。
4.  使用`git status`验证这些文件正在从版本控制中删除
5.  将更改推送到存储库

完成这些步骤后，这些文件应该从版本控制中移除，但不能从任何本地机器上移除。任何其他开发人员在你推送你的更改后再次拉应该没有问题。

注意:仍然可以从 Git 历史中检索这样的数据。因此，如果它是您想要删除的敏感数据，您需要将其从 Git 历史中删除。如果您想从 Git 存储库中删除这些数据，请查看 [huffleHog](https://github.com/dxa4481/truffleHog) 。