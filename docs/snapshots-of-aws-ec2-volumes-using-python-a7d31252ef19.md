# 使用 Python 创建 AWS EC2 卷的快照

> 原文：<https://betterprogramming.pub/snapshots-of-aws-ec2-volumes-using-python-a7d31252ef19>

## *学习使用 Python 的 boto3 库创建和删除亚马逊 EBS 快照*

![](img/308de04ad3d5ceab75cda664e5b5cd0b.png)

数据是任何软件应用程序最重要的方面之一。因此备份数据成为应用程序开发过程中必不可少的任务之一。

为我们存储的数据创建快照是备份数据的一个很好的策略。

这是我们要关注的。我们将要讨论的主题是:

1.  什么是快照，为什么要创建快照？
2.  使用 [Python](http://python.org) 在[亚马逊 EBS](https://aws.amazon.com/ebs/) 中创建快照
3.  快照的删除

让我们一个一个地看一下:

# **什么是快照，为什么要创建快照？**

Amazon 的 EBS(弹性块存储)是一个块存储服务，设计用于 EC2。它提供了通过拍摄时间点快照将 EBS 卷上的数据备份到 AWS S3 的服务。

简而言之，在我们的数据库中发现的重要数据是通过根据需要在适当的时间间隔创建快照来备份的。

# **使用 Python 在 Amazon EBS 中创建快照**

为了创建快照，我们将使用 AWS Python 库 boto3。

假设我们有三个数据库托管在三个不同的 EC2 实例中，每个都有根卷和数据卷。我们想要创建数据卷的快照，因此让我们创建以下字典:

```
volumes_dict = {
                  'database-1' : 'volume-id-1',
                  'database-2' : 'volume-id-2',
                  'database-3' : 'volume-id-3',
               }
```

可以在 EC2 仪表板的 EBS 部分的 AWS 控制台中找到卷 id。

现在，让我们编写几行代码来创建快照:

# **删除快照**

创建快照的频率可能因删除而异，因此在删除时，对于给定的卷 ID，可能会出现多个快照。我们可以根据需要删除。

这里，我们将删除每个卷 ID 的所有快照。你也可以根据自己的需求修改代码，避免删除其中的一些。

让我们看看如何删除快照:

# 结束了

我们已到达文章的结尾。希望你学到了一些东西。欢迎评论和反馈。