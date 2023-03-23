# 用 Python 和 Django 分解芹菜≥4.x

> 原文：<https://betterprogramming.pub/breaking-down-celery-4-x-with-python-and-django-e95eeb7de2a6>

## 用芹菜分配您的 Python 任务

![](img/3cb65e5cee787cad93b511a15b0ef405.png)

图片由 [pelambung](https://pixabay.com/users/pelambung-601793/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=769129) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=769129)

随着数据对更快计算方式的需求增加，处理大量数据已成为一种必然。如果你花了一定的时间编程，你会意识到同步阻塞的编程方式只能让你到此为止。最终，您碰壁了，需要转向分布式模型来扩展您的计算性能。

当谈到 Python 中的分布式计算和异步工作时，主流框架——工具箱中必须知道的——是[芹菜](http://www.celeryproject.org)。

Celery 是一个利用高级消息队列协议(AMQP)在一个或多个节点上实现分布式计算的消息队列模型的包，高级消息队列协议是用于面向消息的中间件的[开放标准](https://en.wikipedia.org/wiki/Open_standard) [应用层](https://en.wikipedia.org/wiki/Application_layer)协议。

![](img/6ea98ced97d4a8eb31433f2cb97d9b89.png)

高级 Python 芹菜架构流

Celery 不仅仅是一个执行分布式异步工作的框架，它还有助于自动化日常工作，例如通过类似 cron 的表达式调度任务，或者通过 celery chords 执行大数据 map-reduce 风格的分布式工作。

芹菜版本 3.x 和 4.x 之间有巨大的差异，很容易迷失在杂草中。本文旨在帮助您配置和运行最新的 celery 4 . x 版本，无论是独立的还是在您的 Django 应用程序中。

# 先决条件

*   确保你已经安装了 Python ≥ 3.6 和更高版本——你可以在这里获得一个副本[。](https://www.python.org/downloads/)
*   安装您选择的代理。理解架构是围绕可伸缩性设计的非常重要，因此尽早选择正确的代理非常重要。代理是中间件，以无缝、分布式的方式促进 Python 服务之间的通信。

芹菜为以下经纪人提供现成的支持:

*   [RabbitMQ](https://www.rabbitmq.com/download.html) (可提供自托管或托管服务)。
*   Redis(可提供自托管或托管服务，参见 Redis 部分下面的链接)。
*   AWS SQS(提供免费层，通常不免费)。我个人不喜欢使用这个代理，因为它需要相当多的 SQS 权限来动态创建队列，在一些生产环境中这可能是不可接受的。
*   Zookeeper 是一个分布式共享数据服务，从 Celery 4.x 开始仍处于试验阶段。

为了简单起见，我们将重点放在 Redis 上。要让其他后端工作，您不需要做太多的更改——主要是在初始化 celery 类时更改 URL。

如果您想跳过 Redis 安装，可以选择利用托管云 Redis。尝试 [redislabs](https://redislabs.com/redis-enterprise-cloud/pricing/) ，它有一个免费层，您可以使用它进行测试运行，参见“当您需要运行独立的 Celery 时”一节。

## 如何在 MAC 上安装 Redis broker？

对于 Redis，替代操作系统请参考 [Redis 快速入门指南](https://redis.io/topics/quickstart)

```
$ brew install redis
```

以下是一些有用的命令。

计算机启动时启动 Redis:

```
$ ln -sfv /usr/local/opt/redis/*.plist ~/Library/LaunchAgents
```

通过`launchctl`启动 Redis 服务器:

```
$ launchctl load ~/Library/LaunchAgents/homebrew.mxcl.redis.plistt
```

为了确保 Redis 的安装能够正常工作，执行这个命令，它将返回`PONG`:

```
$ redis-cli ping
```

# 当你需要运行独立芹菜

让我们准备一个清新的环境。我们将创建一个简单的程序，演示如何在独立的 Python 应用程序中使用 celery。我们将使用 requests 库对 CoinDesk 的比特币价格指数(XBP) API 进行简单的 REST API 调用，获取比特币价格。

用下面的文件夹和文件结构创建一个名为`celery_demo`的新文件夹:

![](img/ff971c5048a3d15e80de2e50e51fd9bf.png)

芹菜示范项目结构

创建一个 Python 虚拟环境，这样可以更好地隔离 Python 包并防止依赖冲突。`cd`到您的`celery_demo`文件夹，并创建一个虚拟环境，其中最后的`venv`是虚拟环境的名称。

```
$ python3 -m venv venv
```

激活您的`venv`虚拟环境:

```
source venv/bin/activate
```

现在，让我们安装所需的 Python 包和用于 Redis 代理的 [Redis Python 包](https://github.com/andymccurdy/redis-py):

```
$ pip install celery[redis] requests
```

将以下代码添加到`celery_demo/celery_tasks/tasks.py`。这段代码演示了如何将任何 python 函数转换成 Celery 任务并执行异步调用。函数顶部的`@task`装饰器将我们的基本 Python 函数标记为芹菜任务。我们的示例函数从 CoinDesk 获取最新的比特币价格。

芹菜任务. py

创建另一个名为 `**celery_demo/run_task.py**` **t** hat 的 python 脚本，我们将用它来测试我们的芹菜味道。您可以像运行任何其他 norm Python 脚本一样运行它。

芹菜跑 _task.py

如果您使用 Redislab 托管服务，您需要通过设置`REDIS_URL`环境变量来添加 Redislab URL。下图显示了托管 Redis 实例的位置。

![](img/eb3c2e3f0518974a5a122cc823e3874c.png)

如果不使用本地 Redis，请将 redislab.com Redis URL 复制到环境变量中

```
$ export REDIS_URL=redis://{YOUR_DATABASE_NAME}:{YOUR_REDISLAB_PASSWORD}@redis-16062.c52.us-east-1-4.ec2.cloud.redislabs.com:16062/0
```

让我们执行我们的芹菜应用程序:

![](img/844d82eea3afcd1bc974b458fc384af6.png)

在上图中，我正在使用我的 Pycharm IDE 运行脚本。如果您在终端上，您可以发出这个命令来获得相同的结果:

```
$ python run_task.py
```

你有没有注意到所有的东西都被返回为假的？这是因为我们还没有启动 worker 您发送给 Celery 的请求已经排队，但没有得到服务。

# 发射芹菜

要启动 Celery，您需要提供 broker 参数，这是通过队列发送消息所必需的。如果要检索已完成的函数或任务返回的状态和结果数据，需要设置 back end 参数，如以下代码所示:

```
app = Celery('tasks', broker=os.environ.get('REDIS_URL', 'redis://localhost:6379/0'), **backend**='redis')
```

我们将使用 Redis 作为我们的后端参数。注意:你除了 Redis 还有其他选项，比如[SQLAlchemy](http://www.sqlalchemy.org/)/[Django](http://djangoproject.com/)ORM、 [Memcached](http://memcached.org/) 、 [Redis](https://redis.io/) 、[RPC](https://docs.celeryproject.org/en/stable/userguide/configuration.html#conf-rpc-result-backend)([rabbit MQ](http://www.rabbitmq.com/)/AMQP)，或者你可以定义自己的定制后端比如 AWS S3 或者 [MiniO](https://min.io/) 。更多详情[此处](https://docs.celeryproject.org/en/stable/getting-started/introduction.html)。

现在尝试启动一个 Celery worker，然后打开另一个终端窗口，激活虚拟环境，再次启动 Python 脚本。

一号航站楼窗口:

```
$ celery -A celery_tasks.tasks worker -l info
```

二号终端窗口:

```
$ python run_task.py
```

参数:

```
**-A**, **--app is the** app instance to use module.attr_name**-l**, **--loglevel is** logging level, options are DEBUG, INFO, WARNING, ERROR, CRITICAL, or FATAL
```

# 定期任务独立 Python 芹菜

Celery 提供了运行 cron 的能力，就像被称为周期性任务的计划作业一样。在遵循相同的生产者-消费者模型时，它们的工作方式略有不同。在周期性任务的上下文中，术语生产者是指`Beat`(参见前面的芹菜流程图)，它向工人发送信号，以在特定的时间间隔/cron 表达式中执行工作。

让我们用独立定期调度程序的配置来覆盖我们的`tasks.py`:

芹菜独立定期任务

Celery 要求 workers 和 beat 都在运行，以便执行预定的作业。以下命令在一个命令中启动 workers 和 beat，尽管在生产环境中它们需要作为独立的服务运行:

```
$ celery -A celery_tasks.tasks worker -l info -B
```

您可能不希望在同一个命令中运行 Celery worker 和 Beat 的主要原因是，本质上您创建了一个单点故障，并且否定了客户端消费者-生产者模型——通常 Beat 和 worker 应该在单独的隔离进程中执行，这些进程要么通过 supervisor 运行，要么在 K8s pods 或 docker 容器中运行。如果您喜欢在单独的进程中运行，您可以在不同的终端窗口中执行以下命令:

```
$ celery -A celery_tasks.tasks worker -l info$ celery -A celery_tasks.tasks beat -l info
```

# 将芹菜添加到 Django 3.0 应用程序中

让我们看看如何将相同的芹菜任务配置到 Django 项目中。理想情况下，您应该为您的新 Django 项目创建一个新的虚拟环境。确保在 Django 项目中运行之前运行的 pip install Celery 和 Redis 命令。

```
$ pip install django
```

我们将使用默认的 Django admin start 项目来自动生成一个简单的 HelloWorld Django 应用程序，这样我们就可以用我们之前创建的 celery 任务对它进行改进。

```
**$** django-admin startproject django_celery_site
```

一旦创建了 Django 应用程序，您的文件夹结构应该如下所示:

![](img/1aaf7560372fddd1cd0919c061cbaf1c.png)

`cd`放入内层的 django_celery_site 文件夹，外层的并不重要，你可以根据自己的需要重新命名。

```
$ cd django_celery_site
```

让我们在 Django 项目中加入芹菜。我们需要做的第一件事是创建以下文件 django `_celery_site/django_celery_site/celery.py`，并在下面添加以下代码，由用于注册我们的任务的 celery 上下文组成:

`celery.py`

`Celery()`对象包含 Celery 任务和配置。通过定义`config_from_object`和`namespace`，我们将在 Django 的`settings.py`中设置任何以`'CELERY_'`开头的变量。我称之为“名称空间配置”。

请注意，不必在 Django settings.py 文件中指定配置。您可以选择在初始化 Celery `app`上下文的同一个文件中添加所有的 Celery 配置。

您的 settings.py 将包含以`CELERY`开头的所有内容的以下芹菜名称空间配置:

Django 芹菜设置. py

如果`USE_TZ = True`(时区激活)，则确保设置相应的`CELERY_TIMEZONE`。

最后一步是确保 Django 在初始化时加载芹菜`app`，在`django_celery_site/django_celery_site/__init__.py`中添加下面的代码片段:

姜戈芹菜 __init__。巴拉圭

下一步是创建您的`django_celery_site/django_celery_site/tasks.py`,您的所有 Python 任务都将由 Celery 调用:

```
from __future__ import absolute_import, unicode_literals
from celery import task, shared_task

@task()
def latest_bitcoin_price(currency_code: str = None) -> object:
    *# Copy same bitcoin logic here our task will virtually peform the same logic as within our standalone celery example* pass
```

为了启动您的芹菜，请使用启动芹菜的项目名称。因为您的`celery.py`位于`django_celery_site`中，所以这是您需要运行 worker 的目录。

```
$ celery -A django_celery_site worker -l info --concurrency=1 -n wkr1@hostname
e$
```

# 姜戈方式的定期任务

运行 Django 周期性任务可以通过启用 Django 项目 settings.py 中的一些配置来实现。由于周期性任务不需要返回结果，我们可以创建原始任务的副本，将其命名为`latest_bitcoin_price_notify_task`，并进行一些小的修改，以便可以将结果推送到第三方服务[pushsafe.com](https://www.pushsafer.com/)。

使用 pushsafe 是出于说明的目的——对于生产部署，有更多可扩展的解决方案，如带有 Firebase 云消息的 SNS。

让我们在我们的`settings.py`中定义一个新条目`CELERY_BEAT_SCHEDULE`，如图所示:

```
CELERY_BEAT_SCHEDULE = {
    'latest_bitcoin_price_notify_task': {
        'task': 'latest_bitcoin_price_notify',
        'schedule': crontab(minute='*/1'),
        'args': ('USD')
    },
}
```

将`PUSHSAFER_PRIVATE_KEY`添加到环境变量中:

```
$ export PUSHSAFER_PRIVATE_KEY=yourprivatekeyhere
```

让我们把这些放在一起:

芹菜任务推送通知

由于我们在开发环境中，为了方便起见，我们可以使用 all in one 命令:

```
$ celery -A django_celery_site worker -l info -B
```

# 最后的想法

在本文中，我们已经看到了如何快速地将发行版添加到 Python 应用程序中。在下一系列芹菜教程中，我们将会看到芹菜的最佳实践。

我希望你喜欢这篇文章，请在下面留下反馈和评论。快乐编码。

[](https://medium.com/better-programming/python-celery-best-practices-ae182730bb81) [## Python 芹菜最佳实践

### 帮助您用 Celery 构建可伸缩的分布式应用程序的提示和技巧

medium.com](https://medium.com/better-programming/python-celery-best-practices-ae182730bb81)