# 识别未使用的 AWS EBS 卷，并将其删除

> 原文：<https://betterprogramming.pub/identify-unused-aws-ebs-volumes-and-get-rid-of-it-f108db2ae887>

## 了解如何识别未使用的 EBS 卷，以及如何使用 Python 脚本删除它们

![](img/340682f3a2dea31cfe800767f704eae9.png)

照片由[罗马卡夫](https://unsplash.com/@romankraft?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/volumes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果您已经使用了 [AWS EC2](https://aws.amazon.com/ec2/) 一段时间，那么您可能也已经为 EC2 实例使用了 [EBS](https://aws.amazon.com/ebs/) 卷。

但是由于 EC2 实例的多次创建和终止，附加到那些被终止的实例的一些 EBS 卷仍然保留在那里。这些书没有任何用途，会产生费用。在本文中，我们将了解如何通过识别和删除那些未使用的卷来节省资金。该作品可分为以下几个副主题:

1.  为 [Python](http://python.org) 脚本准备先决条件
2.  找出未使用的 EBS 卷
3.  以高效的方式删除未使用的 EBS 卷

让我们一个一个地看。

# **为 Python 脚本准备先决条件**

对于 Python 脚本，我们将使用 [AWS boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) 库和 python-dotenv 库来加载环境变量。因此，请在继续之前安装这两个库。

现在，让我们创建一个名为`delete-unused-ebs-volumes`的目录，其中包含脚本和环境文件。

```
mkdir delete-unused-ebs-volumes
cd delete-unused-ebs-volumes
```

在这个目录中，让我们创建一个名为`.env`的环境文件，其内容如下:

```
#aws credentials
AWS_ACCESS_KEY_ID = <your access key>
AWS_SECRET_ACCESS_KEY = <your secret key>
AWS_REGION = <your region>
```

现在，我们可以进入下一步了。

# **2。找出未使用的 EBS 卷**

让我们首先在之前创建的目录中创建一个名为`delete_unused_ebs_volumes.py`的 Python 文件。

让我们在那里添加下面几行:

这里，我们只是导入了所需的库，使用`dotenv.load_dotenv()`加载了环境变量(默认情况下，它采用我们已经创建的`.env`文件)，最后，创建了`ec2`的客户端，我们将进一步需要它。

现在，我们可以开始研究 EBS 卷，找出哪些正在使用，哪些没有使用。让我们添加以下代码:

首先，我们创建了一个列表`volumes_to_delete`，其中存储了未使用卷的 id。然后，我们调用客户端的`describe_volumes()`方法，该方法将返回一个列表，其中包含给定区域中所有卷的详细信息。

我添加了额外的日志来可视化卷列表的详细信息，但是找出未使用的卷的核心概念是:如果给定卷的附加列表为空——意味着它没有附加到任何实例，并且卷的状态是`available` —那么这个特定的卷是未使用的，因此可以添加到`volumes_to_delete`的列表中。

现在，我们已经得到了未使用的卷 id 列表。我们将进行下一步:删除。

# **3。高效删除未使用的 EBS 卷**

现在，我们将遍历`volumes_to_delete`列表中的卷 id，并使用客户端的`delete_volume()`方法执行删除。

我们还将使用`waiters`来确保删除成功完成。

让我们添加更多代码:

完整的脚本和其他相关文件可以在这个 [GitHub 资源库](https://github.com/ashutoshkarna03/delete-unused-aws-resource)中找到。

我们可以将这个脚本作为一个 cron 作业或一个定期调度的任务来运行，这样可以节省一些钱。

我想我们已经到了这首曲子的结尾。感谢阅读，随时欢迎评论和建议。