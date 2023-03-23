# Python 芹菜最佳实践

> 原文：<https://betterprogramming.pub/python-celery-best-practices-ae182730bb81>

## 帮助您用 Celery 构建可伸缩的分布式应用程序的提示和技巧

![](img/e37f30d730064dce5e57577a957ed412.png)

照片由[法比奥](https://unsplash.com/@fabioha?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

构建具有弹性的独立系统已经足够具有挑战性了。加上分销，你突然有了更多需要担心的活动部件。自然，一个软件中活动的部分越多，维护起来就越复杂，花费的时间也越多——Python Celery 也不例外。

在 Celery 系列文章的第一部分中，我们介绍了如何使用独立的 python 开始使用 Celery，并将其集成到您的 Django web 应用程序项目中。

[](https://medium.com/better-programming/breaking-down-celery-4-x-with-python-and-django-e95eeb7de2a6) [## 用 Python 和 Django 分解芹菜≥4.x

### 用芹菜分配您的 Python 任务

medium.com](https://medium.com/better-programming/breaking-down-celery-4-x-with-python-and-django-e95eeb7de2a6) 

在这一部分中，我们将研究您应该遵循的最佳实践，以使启用 Celery 的应用程序更有弹性、性能更好，并提高监控和可观察性。

# 芹菜适合正确的使用案例

人们很容易认为芹菜是解决所有可信问题的通用解决方案。当你对这个工具不够了解的时候，很容易试图让它适应每一个用例。但是当您有一个简单的用例并且您不寻求分布时，使用芹菜可能是多余的。如果您有需要节流的资源，像 AWS SQS 这样的简单队列就足够了——它比配置 Celery 更容易配置和维护。

Celery 的用处在于执行需要耗费性能和资源的任务——例如，在 HTTP 请求的处理程序中，或者当需要处理复杂的计算或 ETL 工作时，这些工作可能需要时间来执行。在这种情况下，使用 Celery 是有意义的，因为尽管您通过提供的级别抽象失去了细粒度控制，但您获得了异步和分布行为。

虽然芹菜可以处理大数据，这取决于你如何编码你的工作，但它不是 Apache Spark 等开源解决方案的直接替代品——尽管芹菜可以补充 Spark，并让 Spark 做它最擅长的事情。

# 选择正确的结果后端

在 Celery 中，结果后端是当您使用 return 语句调用 Celery 任务时，存储任务结果的地方。

```
@task(name='imageprocessor.proj.image_processing')
def image_processing(images: list):
    results = []
    *# perform some work* return results # results stored in backend of your choice
```

选择正确的结果后端可能会为您节省几个小时的痛苦。维护良好的主要后端是 Redis，然后是 RabbitMQ。

如果将结果存储在数据库中，可能需要定期从数据库中清除旧数据。在使用 Redis 奖项时，您可以利用 Redis 内置的旧数据自动过期功能。

如果您使用 AMQP/RabbitMQ 作为结果后端，如下所示:

```
from __future__ import absolute_import

from celery import Celery

app = Celery('mypackage',
             broker='amqp://guest@localhost//',
             backend='amqp://guest@localhost//')
```

Celery 将创建队列来存储结果。如果不定期清除它们，这很容易使您的 RabbitMQ 服务器被数千个死队列淹没。

这个问题已经在 4.x 中通过使用下面的`CELERY_TASK_RESULT_EXPIRES`(或者在 4.1 版的`CELERY_RESULT_EXPIRES`)得到解决，它允许一个定期清理任务从 RabbitMQ 中删除过时数据。关于这方面的更多文档，见芹菜文档[这里](https://docs.celeryproject.org/en/master/userguide/configuration.html#std:setting-result_expires)。

值得注意的是，如果您的利用率很高，那么在下一个清理周期调用之前，如果您耗尽了资源，那么 RabbitMQ 服务器就有可能失败。因此，选择一个到期时间，确保清理过程足够频繁地发生，以避免出现问题。

# 使用分布式锁避免芹菜竞争情况

与`cron`一样，如果第一个任务没有在下一个之前完成，任务可能会重叠。如果这是一个问题，那么使用锁定策略来确保一次只能运行一个实例。

重要的一点是——如果由于某种原因，您的周期性功能不能重叠——如果您在不同的进程中有 Celery 实例，可能跨不同的服务器，或者在关键资源共享时竞争条件，那么就需要一个分布式锁定系统。

这里有一个典型的例子。您将定期任务设置为一分钟，但您的工作没有在指定的时间范围内完成。添加一个锁来防止两个工作者试图访问同一个资源的重复情况是有意义的。我们之前在[“用 Python 和 Django 分解芹菜≥4 . x”](https://medium.com/better-programming/breaking-down-celery-4-x-with-python-and-django-e95eeb7de2a6)中安装的 Python Redis 包提供了 Redis 锁，我们可以用它来防止分布式环境中的竞争情况。

我们可以进一步扩展上述内容，将它放入一个可重用的包装器中，我们可以将它标记到任何函数上——我们在任何时候都只需要执行一个实例。在装饰器下面，我们设置了一个锁超时时间，这个时间可以充分估计任务的持续时间——这样，如果任务或芹菜节点崩溃，任务最终将能够重新获得锁。

除了 Redis，还有其他适合分布式锁定实现的分布式开源解决方案，比如 ZooKeeper 和 [etcd](https://etcd.io/) [Python 客户端](https://github.com/haizi-zh/python-etcd-lock)。

# 数据库不应作为 AMQP 经纪人时期的替代品

当您将数据库用作代理时，随着芹菜集群中工作线程数量的增加，您会增加 IO 的风险。这可能会降低可能利用同一数据库的其他应用程序的速度。您可能会有同样的想法——您已经有了一个数据库，您不想在托管合适的代理时产生额外的成本。

然而，当您的应用程序很小时，一个合适的队列系统的长期成本超过了您可能获得的直接利益。像 [RabbitMQ](http://www.rabbitmq.com/) 这样的 AMQPs 利用数据在内存中的存储，所以你不会因为磁盘 IO 而损失性能。也不需要像使用数据库时需要的语句管理。

数据库带来了更多您需要担心的问题。假设芹菜集群中有 N 个工人，每个工人都需要根据请求获得某种锁。一旦获得了该行的排他锁，系统就需要处理更新(例如，将状态更新为“正在处理”)。之后，需要尽快释放锁(例如，通过提交事务)，以便其他工作人员可以访问队列。当这样的设计应用于数据库时，性能会显著降低。

# 努力以原子的方式编码

原子操作是一系列不可分割和不可约的数据库操作，要么全部发生，要么什么都不发生。尽管原子性的概念通常与数据库操作相关联，但它也可以应用于芹菜。

```
@app.task
def update_data():
    user.status = 'updated'
    user.save()
    r = twitter_get_profile()
    user.name = r.name
    user.save()
```

在这段代码中，我们有一个任务，它将用户状态设置为`updated`，保存它，向 Twitter 发出请求，然后更新用户名。这不是原子性的——如果请求失败，我们在数据库中会有一个不一致的状态(带有`status=updated`的用户和一个尚未更新的名称)。

正确的做法是首先发出请求，然后同时更新用户状态和名称:

```
@app.task
def update_data():
    r = twitter_get_profile() if r.status != 200:
        return user.name = r.name
    user.status = 'updated'
    user.save()
```

现在，我们的操作已经变得原子化——要么一切成功，要么一切失败。这是我们应该永远努力的。

# 经常设置检查点

当使用任务处理大量数据时，请始终确保有检查点，以便在出现故障时，可以从中断的地方继续，而不是重新处理整批数据。您可以利用`Memcache`或像 Redis 这样的键值对存储来恢复您的任务。检查点的目的是在失败的情况下，如果您需要重新启动 Celery 任务，将浪费的时间和精力减到最少。

# 给你的任务起个名字

默认情况下，Celery 根据模块的导入方式创建任务名称。为了避免与其他包冲突，使用标准的命名约定，比如`proj.package.module.function_name`。

```
@app.task(name='celery_tasks.tasks.add')
def add(a, b):
    return a + b
```

# 始终使用 auto_retry 和 max_retries

自动重试提供了在特定异常发生时使用相同的重试任务的能力。自动重试接受预期异常的列表，并在其中一个异常发生时重试任务。设置`max_retries`来防止无限循环的发生总是一个好主意。

```
from httplib import HTTPException@app.task(name='celery_tasks.tasks.train_ml_model', auto_retry=[CoinDeskClientException], max_retries=3)
def train_ml_model():
    return train_ml_model_result()
```

# apply_async 过延迟

Celery 提供了两个函数调用选项，`delay()`和`apply_async()`，用于调用 Celery 任务。`delay()`已经预先配置好，只需要将参数传递给任务——这足以满足大多数基本需求。

```
add.delay(5, 5)
add.delay(a=5, b=10)
```

`Apply_async`比预配置的延时更复杂，但也更强大。最好使用`apply_async`和特别设置的选项，以获得最大的灵活性。

```
add.apply_async(queue='low_priority', args=(5, 5))
add.apply_async(queue='high_priority', kwargs={'a': 5, 'b': 5})
```

# 总是定义队列

始终定义一个队列来处理优先级较低的作业。您可能希望至少有三个队列，一个用于高优先级任务，一个用于低优先级任务，一个默认队列用于普通优先级。可以选择设置`app.conf.task_create_missing_queues = True`。通过这种方式，您可以将队列创建委托给 Celery。

您可以对任何队列使用`apply_async`，只要您的任务知道`apply_async`使用的队列，Celery 就会处理它。如果没有提供，那么工作线程将只监听默认队列。

下面的命令可用于运行一个 worker，其中我们基于优先级指定队列:

```
(venv) $ celery -A celery_tasks.tasks worker -l info -Q default,low_priority,high_priority(venv) $ celery -A celery_tasks.tasks worker -l info -Q default -c 2
(venv) $ celery -A celery_tasks.tasks worker -l info -Q low_priority -c 1
(venv) $ celery -A celery_tasks.tasks worker -l info -Q high_priority -c 4
```

这种方法的额外好处是并发性。参数`-c`定义了工作线程创建了多少个并发线程。可以为自动缩放工作线程添加一个附加参数:

```
(venv) $ celery -A celery_tasks.tasks worker -l info -Q default --autoscale 4,2
(venv) $ celery -A celery_tasks.tasks worker -l info -Q low_priority --autoscale 2,1
(venv) $ celery -A celery_tasks.tasks worker -l info -Q high_priority --autoscale 8,4
```

应用上述组合，我们可以控制并行性，以增加排队工作的出列。

# 保持并发数量接近 CPU 核心数量

这条规则适用于几乎所有用于分布式计算的 Python 库:如果服务器有 8 个核心 CPU，那么最大并发数应该设置为 8 或 N -1，其中最后一个用于其他基本的操作系统功能。

这条经验法则可以帮助您在不过度使用资源的情况下获得最大可能的性能，而过度使用资源可能会减少通过分发获得的收益。

# 对单个队列应用优先级

就性能而言，分布在多个队列中的任务总是比将所有内容放在一个队列中要好。虽然这可能是真的，但是单队列任务可能具有不同的优先级，其中优先级可以用从 0 到 9 的整数范围来定义。

请注意，队列很有可能没有机会对消息进行优先级排序，因为它们可以在排序之前出队。

```
add.apply_async(queue='high_priority', priority=0, kwargs={'a': 10, 'b': 5})
add.apply_async(queue='high_priority', priority=3, kwargs={'a': 10, 'b': 5})
add.apply_async(queue='high_priority', priority=9, kwargs={'a': 10, 'b': 5})
```

为了获得想要的效果，需要进行一些设置:

```
CELERY_ACKS_LATE = True
CELERYD_PREFETCH_MULTIPLIER = 1
```

默认情况下，预取乘数是 4。

# 组块是你最好的朋友——经常做

如果你有几十万个对象，那么更谨慎的做法是分块处理它们。例如，`1 000 000`元素可以被分割成每个作业的`1000`元素块，给你队列中的`1000`任务。

```
@celery_app.task(name='celery_tasks.tasks.process_data')
def process_data(elements):
    return process_elements(elements)process_data.chunks(iter(elements), 1000).apply_async(queue='low_priority')
```

注意:块是按顺序执行的。我们可以将数据块转换成一组并行使用的数据。

```
process_data.chunks(iter(elements), 100).group().apply_async(queue='low_priority')
```

# 芹菜配置:选择最适合的

我们之前看到了如何通过`settings.py`配置 celery 设置，或者直接使用 Celery 应用程序上下文。以下是其他一些可能有用的加载芹菜配置的方法，这取决于你想如何安排你的配置。

您可能在网上遇到的大多数例子都使用了以下设置的一个或多个变体。重要的是要理解总体结果是相同的—您决定采取的方法完全取决于您希望如何安排您的配置。

```
import celeryconfigfrom celery import Celeryapp = Celery()app.config_from_object(celeryconfig)
```

您的`celeryconfig.py`将包含如下所示的设置常量值。因为我们在这里没有使用 namespace 属性，所以 Celery 希望从默认的`BROKER_URL`常量中找到 Redis broker URL 只是需要记住一些东西。

```
REDIS_URL = os.environ.get('REDIS_URL', 'redis://localhost:6379/0')
BROKER_URL = REDIS_URL
CELERY_RESULT_BACKEND = REDIS_URL
CELERY_ROUTES = {'task_name': {'queue': 'queue_name_task'}}
```

这是另一种使用 Celery 上下文对象直接进行更新的方法。

```
from celery import Celeryapp = Celery('tasks')REDIS_URL = os.environ.get('REDIS_URL', 'redis://localhost:6379/0')app.conf.update(
    result_expires=60,
    task_acks_late=True,
    broker_url=REDIS_URL,
    result_backend=REDIS_URL
)
```

如果您希望有一个类对象，您可以使用配置类获得相同的结果:

```
from celery import CeleryREDIS_URL = os.environ.get('REDIS_URL', 'redis://localhost:6379/0')app = Celery('tasks', broker=REDIS_URL, backend='redis')class Config:
    enable_utc = True
    timezone = 'Asia/Singapore'app.config_from_object(Config)
# or using the fully qualified name of the object:
#   app.config_from_object('module:Config')
```

`[app.config_from_envvar()](https://docs.celeryproject.org/en/stable/reference/celery.html#celery.Celery.config_from_envvar)`从环境变量中获取配置模块名

例如，从名为`CELERY_CONFIG_MODULE`的环境变量中指定的模块加载配置:

```
$ export CELERY_CONFIG_MODULE="celeryconfig.prod"
```

或者直接在试图运行一个工人时:

```
$ CELERY_CONFIG_MODULE="celeryconfig.prod" celery worker -l info
```

下面是如何注册`CELERY_CONFIG_MODULE`以便 Celery 应用程序上下文可以获取配置并加载它们:

```
import os
from celery import Celery#: Set default configuration module name
os.environ.setdefault('CELERY_CONFIG_MODULE', 'celeryconfig')app = Celery()
app.config_from_envvar('CELERY_CONFIG_MODULE')
```

# 强制进行监控和观察

当环境像今天这样复杂，多个工作人员在不同的机器上运行时，现代监控方法应该融入到部署流程中。

对已知问题的监控并不能解决越来越多的未知问题，如果没有可观察的系统，这些未知问题可能会出现—您不知道是什么导致了问题，也没有标准的起点/图表来找出问题。对于 Celery，随着节点数量的增加，系统变得更加复杂，这变成了 N 个故障点——当您发送请求时，这是一个黑盒。

从基础开始:日志。确保尽可能多地记录日志。这将有助于您在出现 bug 时跟踪出了什么问题。

```
from celery.utils.log import get_task_loggerlogger = get_task_logger(__name__)@app.task
def add(a, b):
    logger.info('Adds {0} + {1}'.format(a, b))
    return a + b
```

诸如 Newrelic、 [Sentry](https://sentry.io/) 和 [Opbeat](https://opbeat.com/) 等服务和工具可以很容易地集成到 Django 和 Celery 中，并帮助您监控错误。您还可以将它们与 Slack 集成，这样每当出现问题时您都会收到通知，同时还可以微调生成通知的内容。假阳性太多，你最终会忽略实际的错误。

下面是一些你可以用来提高你的监控和观察能力的工具

*   [Watchtower](https://watchtower.readthedocs.io/en/latest/) 是亚马逊 Web 服务 CloudWatch 日志的日志处理程序。您可以做的一件有趣的事情是将您的云观察日志映射到 [Grafana](https://grafana.com/) 以获得更好的[可视化](https://grafana.com/grafana/dashboards/9970)。
*   [Flower](http://flower.readthedocs.io/) 是芹菜任务现场监控的便捷工具。您可以检查哪些任务正在运行，并跟踪已执行的任务。缺点之一是它是一个独立的应用程序，可能无法与您现有的监控解决方案很好地结合。

# 不要低估测试

测试和调试 Celery 任务通常比我们在普通的独立和 web 应用程序中所习惯的要困难。Celery 提供了`[task_always_eager](http://docs.celeryproject.org/en/latest/userguide/configuration.html#std:setting-task_always_eager)`，这是一个很好的设置，便于测试和调试。

```
celery.conf.task_always_eager = Falseor celery.conf.CELERY_ALWAYS_EAGER = False , if you're using pre-4.0 CeleryYou can do this on a per-test basis
Make sure it’s not activated in a production environment as you loose distribution ability.
```

如果你把它设置为`True`，无论何时你调用`delay`或`apply_async`，它都会同步运行任务，而不是把它委托给一个工人。这简化了本地环境中的调试，并促进了自动化测试。

# 最后的想法

在构建 Celery 分布式应用程序时，我们已经介绍了许多最佳实践。

总而言之，在用 Celery 构建分布式系统时，测试应该是开发工作中不可或缺的一部分。在努力通过监控和可观察性获得可见性的同时，实践这些将有助于您在出现问题时导航调试遗忘的深渊。

我希望您喜欢这篇文章，并且这里的信息可以帮助您构建更好的支持 Celery 的应用程序。如果您有任何意见或反馈，请在下面留言。

编码快乐！