# 如何通过 SSH 连接到 AWS Elastic Beanstalk 实例

> 原文：<https://betterprogramming.pub/how-to-connect-to-an-aws-elastic-beanstalk-instance-via-ssh-ccae869d050>

## 在 AWS EB Linux 实例中启用 SSH 访问

![](img/fdb48bdb1b5578fa30f9e81dca1f5752.png)

由 [Boitumelo Phetla](https://unsplash.com/@writecodenow?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我最近遇到了一个我无法解决的关于我的 EB 实例的问题。找出发生了什么的唯一方法是通过 SSH 直接访问实例。然而，通过 SSH 访问 AWS Elastic Beanstalk Linux 实例并不那么简单。要通过 SSH 访问 EB 实例，首先必须启用它。

现在让我们看看需要做些什么来在 AWS Elastic Beanstalk 环境中启用 SSH 访问，并通过 SSH 访问 EB Linux 实例。

# 先决条件

开始之前，您需要设置一个有效的 AWS EC2 密钥对。

> “由公钥和私钥组成的密钥对是一组安全凭证，用于在连接到 Amazon EC2 实例时证明您的身份。Amazon EC2 在你的实例上存储公钥，你存储私钥。” *—* [*亚马逊 EC2 密钥对和 Linux 实例*](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)

AWS 文档声明"*对于 Linux 实例，私钥允许您安全地 SSH 到您的实例。*“因此，这就是为什么您需要一个 AWS EC2 密钥对。

遵循 AWS 官方文档中的这个指南，了解如何设置有效的 SSH 密钥对。

具体来说，检索一个`.pem`或`.ppk` SSH 私有密钥。现在，您需要将您的密钥放在您的主目录下的`.ssh`目录中。

如果您是 Linux 或 macOS 用户，请将您的 SSH 私钥放在以下目录下:

```
~/.ssh
```

在 Windows 计算机上，将 SSH 密钥文件放在以下目录中:

```
C:\Users\<YOUR_ACCOUNT _NAME>\.ssh
```

确保用您的 Windows 帐户名称替换`<YOUR_ACCOUNT_NAME>`。

在这两种情况下，如果您没有`.ssh`目录，请创建一个。

# SSH 到 AWS 弹性 Beanstalk Linux 实例

首先，进入您的本地 Elastic Beanstalk 应用程序目录。然后，启动以下命令:

```
eb ssh
```

如果您的 EB 环境中没有启用 SSH，您将得到以下错误:

```
ERROR: This environment is not set up for SSH. Use "eb ssh --setup" to set up SSH for the environment.
```

现在，按照错误消息的建议，运行下面的命令:

```
eb ssh --setup
```

[EB CLI](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html) 将打印:

```
WARNING: You are about to setup SSH for environment "<your_eb_environment_name>". If you continue, your existing instances will have to be terminated and new instances will be created. The environment will be temporarily unavailable. To confirm, type the environment name:
```

键入您的 EB 环境的名称。请注意，您可以从消息的第一句中找到的`<your_eb_environment_name>`变量中复制它。

现在，`eb`将要求您选择您之前创建的 SSH 私有密钥。

```
Select a keypair.1\. <your_SSH_key_name>
2\. [ Create new KeyPair ]
(default is 1): 
```

键入`Enter`或`1`来选择您的 SSH 私钥。如果您没有将 SSH 私有密钥放在正确的目录中，如上所示，您将得到一个错误，过程将停止。

现在，正如上面的`WARNING`消息中提到的，`eb`将终止您现有的实例，并用可以通过 SSH 访问的新实例来替换它们。

现在，您可以使用`eb ssh`命令 SSH 到您的 EB 实例中。启动时，将要求您选择想要通过 SSH 访问的 EB 实例，如下所示:

```
Select an instance to ssh into
1) i-0670eb7302902c011
2) i-0898ccba32f30f4b8
(default is 1):
```

键入与您要访问的 EB 实例相关联的编号，您将获得以下结果:

```
Amazon Linux 2 AMIThis EC2 instance is managed by AWS Elastic Beanstalk. Changes made via SSH WILL BE LOST if the instance is replaced by auto-scaling. For more information on customizing your Elastic Beanstalk environment, see our documentation here: [http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers-ec2.html](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers-ec2.html)
```

正如该消息中提到的，请记住，一旦实例被销毁以缩小规模，通过 SSH 所做的任何更改都将丢失。

现在，您可以通过 SSH 在命令行中启动 bash 命令。

瞧啊！您刚刚学习了如何通过 SSH 访问您的 EB 实例。这对于监控它们、访问日志或调试特别有用。

# 结论

在本文中，您学习了如何使用`eb`启用 SSH。具体来说，您了解了如何通过 SSH 访问 AWS Elastic Beanstalk Linux 实例。首先，您看到了如何生成 EC2 密钥对。然后，您看到了如何设置`eb`并使用您的 SSH 私有密钥 SSH 到 EB Linux 实例。这将允许您直接在实例中启动 bash 命令。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。