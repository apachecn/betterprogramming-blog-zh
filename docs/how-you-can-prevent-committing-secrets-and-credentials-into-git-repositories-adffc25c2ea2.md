# 如何防止将秘密和凭证提交到 Git 存储库中

> 原文：<https://betterprogramming.pub/how-you-can-prevent-committing-secrets-and-credentials-into-git-repositories-adffc25c2ea2>

## 您的系统的安全性取决于其最薄弱的环节

![](img/9b8fb819670b06cca468c48b05e39698.png)

[阿吉特](https://unsplash.com/@arget?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hacker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 问题陈述

在软件开发生命周期中，我们需要一直处理凭证、密码和秘密。

早先我们在做应用开发的时候，数据库密码管理是我们唯一要考虑的事情。但是现在，随着云的使用越来越多，我们需要处理各种密钥、服务帐户、服务主体等。也是。

我经常看到，如果开发人员缺乏经验或不够成熟，无法理解安全方面，那么他们最终可能会提交密码、服务帐户或源代码库中的任何其他秘密。

如果您使用的是公共或公开托管的回购，甚至是整个组织都可以访问的私有回购，这可能是灾难性的。

请记住，无论您的技术政策有多强，您的系统的安全性取决于其最薄弱的环节。

# 现有解决方案

目前，有像 [SonarQube](https://www.sonarqube.org/) 和 [SonarLint](https://www.sonarlint.org/) 静态代码分析工具这样的解决方案，它们可以检测代码中是否存在任何密码或密码短语。

因此，这些对于检测这些东西是否已经被签入到源代码库中是很有用的。但是大多数时候，当代码已经签入时，这些分析是在服务器端运行的。

在 SonarLint 的例子中，开发人员甚至可以在签入代码之前在本地运行分析。但是 SonarLint 无法检测是否有来自 [AWS](https://aws.amazon.com/) 、 [GCP](https://cloud.google.com/) 等的服务账户或密钥。

此外，SonarLint 的本地验证主要是一个手动过程，不会阻止某人提交凭证，即使是在本地。

# 解决方案

为了克服这个挑战，我试图寻找一些开源项目，并找到了 AWS 实验室的一个项目，名为 [git-secrets](https://github.com/awslabs/git-secrets) 。

该项目可以很好地防止您将秘密和凭证提交到特定于 AWS 的 Git 存储库中。

我在寻找一种解决方案，可以扩展到谷歌云平台(GCP)的凭据。因此，我扩展了这个项目以增加对 GCP 的支持。

# 我如何使用它？

首先，您需要将 git-secrets repo 克隆到本地机器上。

```
git clone [https://github.com/deshpandetanmay/git-secrets.git](https://github.com/deshpandetanmay/git-secrets.git)
cd git-secrets/
```

如果您在 Unix 计算机上，则运行以下命令来安装该实用程序:

```
sudo make install
```

你可以看看在 Windows 或 macOS 上安装这个的说明[这里](https://github.com/deshpandetanmay/git-secrets#installing-git-secrets)。

安装完成后，您需要转到 Git repo，在那里您需要使用这个实用程序。为了演示，我将从我的 GitHub 克隆另一个 repo。

```
git clone [https://github.com/deshpandetanmay/cdr-data-generator.git](https://github.com/deshpandetanmay/cdr-data-generator.git)cd cdr-data-generator/
```

现在，要为这个 repo 安装`git secrets`,您可以运行以下命令:

```
git secrets --install
✓ Installed commit-msg hook to .git/hooks/commit-msg
✓ Installed pre-commit hook to .git/hooks/pre-commit
✓ Installed prepare-commit-msg hook to .git/hooks/prepare-commit-msg
```

这将安装可执行文件的秘密扫描，它也将安装三个钩子的回购。

要安装 AWS 和 GCP 特定检查:

```
$ git secrets --register-aws
$ git secrets --register-gcp
```

现在你都准备好了。为了测试一切是否如预期的那样工作，我在 GCP 创建了一个服务 JSON 账户，并将其复制到我的 repo 中。

现在，当我运行`git commit`命令时，我看到:

```
git commit -m "Updated config"
test.json:4:  "private_key_id": "d30b0f8858589d3d1294aee5b",
test.json:5:  "private_key": "-----BEGIN PRIVATE KEY-----<Actual Private Key>a\n-----END PRIVATE KEY-----\n",[ERROR] Matched one or more prohibited patternsPossible mitigations:
- Mark false positives as allowed using: git config --add secrets.allowed ...
- Mark false positives as allowed by adding regular expressions to .gitallowed at repository's root directory
- List your configured patterns: git config --get-all secrets.patterns
- List your configured allowed patterns: git config --get-all secrets.allowed
- List your configured allowed patterns in .gitallowed at repository's root directory
- Use --no-verify if this is a one-time false positive
```

它向我显示有一个名为`test.json`的文件，其中包含一个被禁止的模式，它阻止我提交 GCP 服务帐户。

在`git secrets`检测到误报的情况下，您运行下面的命令来忽略检查并继续提交。

```
git commit -m "Updated config" --no-verify
```

这不仅检测源代码文件中的秘密，还检测提交消息中的秘密，并阻止开发人员在提交消息中提交秘密信息。

到目前为止，目前的实现只支持 AWS 和 GCP，我正计划将其扩展到 Azure。

# 结论

总之，您可以使用上述技术来防止开发人员将凭证提交到源代码存储库中，并帮助保护您的应用程序的安全。

# 进一步阅读

1.  [https://github.com/deshpandetanmay/git-secrets](https://github.com/deshpandetanmay/git-secrets)
2.  [https://www.atlassian.com/git/tutorials/git-hooks](https://www.atlassian.com/git/tutorials/git-hooks)

```
Hey, if you enjoyed this story, check out [Medium Membership](https://deshpandetanmay.medium.com/membership)! Just $5/month!*Your membership fee directly supports me and other writers you read. You’ll also get full access to every story on Medium.*
```