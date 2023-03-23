# 如何在 AWS Elastic Beanstalk 上调度 Django 命令

> 原文：<https://betterprogramming.pub/how-to-schedule-a-django-command-on-aws-elastic-beanstalk-21c237ee635b>

## 使用 cron 作业在 AWS EBS 上运行 Django 命令的最新指南

![](img/4d398cae885fe5d3c6586966a6cd9d79.png)

作者照片。

最近，我希望每小时运行一次脚本，这也需要我的 [Django](https://www.djangoproject.com/) 应用上下文。虽然我确实找到了一些在线指南，但它们要么已经过时，要么不够全面，而且与最近[AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)(EBS)的变化不一致。

EBS 允许我们以无缝和可管理的方式运行 Django 等 web 应用程序。这也意味着我们不应该在当前随机分配的 EC2 实例上设置任何本地 cron 作业。相反，我们是在我们的 EBS 环境中进行的。

在本指南中，我将概述如何定义 Django 命令、创建 EBS 配置文件，以及部署和测试新创建的调度脚本。

假设我们的脚本叫做`update_emails.py`，它的目的是每天为一个特定的 Gmail 账户获取新邮件。

# 姜戈指挥部

首先，让我们创建 Django 命令。

1.  (可选)如果您还没有一个`commands`目录，您需要创建一个。在您的项目中，在您选择的 Django 应用程序下，创建一个`management`目录，然后创建一个`commands`目录。确保它们被初始化为包目录(即它们有`__init_.py`文件)。关于自定义`manage.py`命令的更多信息可在文档中找到[。](https://docs.djangoproject.com/en/3.1/howto/custom-management-commands/)

您的目录树应该如下所示:

```
├── your_app
│   ├── __init__.py
│   ├── management
│   │   ├── __init__.py
│   │   └── commands
│   │       └── __init__.py
...
```

2.在`commands`目录下，创建一个名为`fetch_emails.py`的新文件，并对其进行编辑，使其包含您的代码:

# 弹性 Beanstalk 配置文件

现在我们已经准备好了自定义的 Django 命令，我们需要请求 EBS 定期运行它(例如，每天一次)。

在您的根项目目录中，在`.ebextensions`目录中，创建一个新的配置文件。确保其名称符合 [EBS 惯例](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html)。给我们的取名`fetch_emails_cron.config`吧。

然后，编辑`fetch_emails_cron.config`，使其包含以下脚本:

***2022 年 9 月更新—*** *参考* [*这个 StackOverflow 线程*](https://stackoverflow.com/questions/19997343/run-manage-py-from-aws-eb-linux-instance/64599849#64599849) *更新的环境和 app 路径如果你用的是 Python 3(感谢 callout @*[emmazarusu](https://medium.com/@littleoduozu?source=user_profile-------------------------------------))*。*

上面的脚本首先创建两个文件，然后运行一个命令。这两个文件是:

1.  这是运行 Django 命令的 bash 文件。在它的内容中，您可以看到我们正在激活我们的本地虚拟环境，其中包括我们的 Django 上下文和已安装的包，然后运行我们的自定义 Django 命令。
2.  `fetch_emails_cron` —这实际上不是一个文件，而是一个 cron 作业，它将在每天上午 12 点运行我们新创建的 bash 文件，正如我们在上面的 EBS 配置文件中指定的`0 0 * * *` 。如果你需要不同的时间表，你可以使用 crontab Guru。

最后一个`rm_old_cron`命令是删除我们 cron 作业的任何旧版本，这发生在每次向 EBS 部署新版本时。

# 部署和测试

如果你已经安装了 [EB CLI](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html) ，你可以简单地运行`eb deploy`。一旦完成，运行`eb ssh`。这将建立一个到您的 EB 环境的 SSH 连接，以便您可以检查您的 cron 作业是否已经成功安装。

为此，在 SSH 终端中，运行`ls /etc/cron.d/fetch_emails_cron`并确保 cron 作业确实存在。

# 结论

就是这样。您已经成功地设置了 Django 命令，以便在您的 EBS 环境中重复运行。