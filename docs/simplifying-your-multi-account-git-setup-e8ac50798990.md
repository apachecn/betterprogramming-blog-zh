# 简化您的多帐户 Git 设置

> 原文：<https://betterprogramming.pub/simplifying-your-multi-account-git-setup-e8ac50798990>

## 在同一台机器上使用带有多个 SSH 密钥的 git 和 GitHub

![](img/7160923e88956941d5225bb1cbb68fd6.png)

迭戈·乔克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当您克隆 git 存储库以使用正确帐户的私钥和用户名时，您有改变主机名的习惯吗？通过将您的本地项目放在每个帐户的根文件夹中，并相应地更新您的 git 配置，您可以使您的生活更轻松，节省时间，并解放您的思想以进行更有影响力的工作。请继续阅读，了解如何操作。

假设你在 GitHub(或 GitLab，或 Bitbucket，或任何其他托管的存储库)上有两个帐户:

1.  **账户 A** —您的工作账户，以及
2.  **账户 B** —您的个人账户

这两个帐户都有自己独特的用户名，并使用自己独特的私钥。当在本地使用这些存储库中的代码时，您需要一种方法来指定每个存储库使用什么用户名和什么私钥。您有两种选择让两个帐户在同一台机器上工作。

# 又快又脏的方法

为了让您的本地 git 知道使用什么名称和私有密钥，您可以在`~/.ssh/config`中创建两个 ssh 配置，它们将包含如下内容:

```
Host github-**work**
    Hostname github.com
    IdentityFile ~/.ssh/id_rsa_**account_a**
Host github-**personal**
    Hostname github.com
    IdentityFile ~/.ssh/id_rsa_**account_b**
```

每次你克隆一个作品或者一个个人存储库，你都需要手动将远程主机从`github.com`更改为`github-work`或者`github-personal`。此外，在克隆之后，您需要设置一个相关的 git 用户名和 git 电子邮件到与相关帐户相关联的那个。

它会工作得很好，但有时你可能会忘记更改主机名，导致本地 git 使用默认帐户，或者你在工作项目中不小心将`user.email`设置为你的个人电子邮件。

总而言之，除了在每个克隆上浪费时间之外，您有时还会犯错误，并且只有在推送到远程给团队的其他成员造成不便之后才意识到这一点。很高兴有更好的方法。

# 更好的方法

通过围绕如何在本地存储项目建立一个结构，您可以加速您的开发过程，并且永远忘记改变克隆 URL。

## 步骤 1:将项目分成两个子目录

```
~/
	workspaces/
		**account_a**/
			project_panda/
			project_tiger/
			project_swan/
		**account_b**/
			project_mice/
			project_cricket/
			project_elephant/
```

## 步骤 2:修改 git 配置

对于您的**帐户 A，**添加以下配置。

更新`~/.gitconfig`以有条件地包含`~/workspaces/account_a`目录下项目的自定义帐户配置:

```
[includeIf "gitdir:~/workspaces/**account_a**/“]
path = ~/.gitconfig_**account_a**
```

现在创建`~/.gitconfig_account_a`文件来设置您的**帐户和**用户名和电子邮件，并使用私钥指向 ssh 配置:

```
[core]
    sshCommand="ssh -F ~/.ssh/**config_account_a**"
[user]
    name = Account A Name
    email = **account.a**.email@example.com
```

最后，创建`~/.ssh/config_account_a`文件来指定 github 用户名和私钥:

```
Host github.com
    Hostname github.com
    IdentityFile ~/.ssh/id_rsa_**account_a**
    IdentitiesOnly yes
    User **account_a**_user_name
```

类似的配置也适用于**账户 B** :

在`~/.gitconfig`:

```
[includeIf "gitdir:~/workspaces/**account_b**/“]
path = ~/.gitconfig_**account_b**
```

在`~/.gitconfig_account_b`中:

```
[core]
    sshCommand="ssh -F ~/.ssh/**config_account_b**"
[user]
    name = Account B Name
    email = **account.b**.email@example.com
```

最后，创造`~/.ssh/config_account_b`:

```
Host github.com
    Hostname github.com
    IdentityFile ~/.ssh/id_rsa_**account_b**
    IdentitiesOnly yes
    User **account_b**_user_name
```

## 步骤 3:更新远程

如果您之前使用了带有修改主机名的遥控器(例如，`github-work`和`github-personal`)，现在您可以将其恢复为原始主机名`github.com`:

```
git@**github-work**:awesome_you/project_panda.git -> git@**github.com**:awesome_you/project_panda.git
```

一旦遥控器更新，尝试一下，看看它是否工作。

## 第四步:尝试一下

现在，每当您想要为您的一个帐户克隆一个存储库时，请转到相应的目录并照常使用 git 命令。以下是**账户 A** 中的项目示例:

```
# cd ~/workspaces/**account_a**
# git clone git@github.com:awesome_you/project_panda.git
# cd project_panda
# git config --get user.email
**account.a**.email@example.com
```

以下是**账户 B** 中的项目示例:

```
# cd ~/workspaces/**account_b**
# git clone git@github.com:awesome_you/project_mice.git
# cd project_mice
# git config --get user.email
**account.b**.email@example.com
```

正如您所看到的，项目已经使用相应帐户的相关凭证进行了克隆，而不需要修改克隆 URL。

为同一个 SCM 托管提供者管理多个 git 帐户是很棘手的，需要对 git 配置进行一些调整。尽管毫无疑问，您可以对不同的帐户使用修改过的主机名，但是这种方法很容易出错，并且会意外地将个人帐户提交到您的工作报告中。为了提升您的开发过程，将您的项目分割在本地单独的根目录中，并且彻底忘记将作者固定在提交的变更上所引起的头痛。

我要感谢 Arnelle 的下面这篇文章，这篇文章启发了我写这篇文章:

 [## 设置多个 GitLab 帐户

### 更新，2019–03–07:我发布了这篇文章的更新版本，你可以在下面查看它…

medium.com](https://medium.com/uncaught-exception/setting-up-multiple-gitlab-accounts-82b70e88c437)