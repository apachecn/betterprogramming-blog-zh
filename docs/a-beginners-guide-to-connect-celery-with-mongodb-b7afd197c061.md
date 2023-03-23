# 连接芹菜和 MongoDB 的初学者指南

> 原文：<https://betterprogramming.pub/a-beginners-guide-to-connect-celery-with-mongodb-b7afd197c061>

## 使用 Celery Mongo 数据库调度程序简化异步任务

![](img/7dc69472db631d8b03b70a3349c1da55.png)

照片由[在](https://unsplash.com/@dosejuice?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上剂量果汁

大多数现实世界的应用程序都是为处理大量用户、复杂的数据处理和计算而构建的。所有这些都在后台完成，不会影响用户体验。在本文中，我将在一个 Django 项目中解释 Celery MongoDB 调度器的工作原理、Celery 和 Redis 与 MongoDB 的连接。

## 芹菜具有处理这些责任的能力

根据维基百科:Celery 是一个基于分布式消息传递的开源异步任务队列或作业队列。虽然它支持调度。

## 芹菜有两个特点:

1.  Worker:它有执行任务的子进程。
2.  Beat:定期向代理发送任务。

# 体系结构

![](img/646efdeffba8886aaad3b643dd952f0d.png)

维卡斯·高塔姆的芹菜建筑

我来解释一下。在这里，我们将任务存储在 MongoDB 的一个集合中，Celery Beat 定期从 MongoDB 中挑选任务并传递给 Redis。Redis 有存储任务的任务队列。工作进程从任务队列中挑选任务并执行任务。生成的数据再次被发送回 MongoDB，并存储在一个单独的集合中。

现在让我向你展示这是多么容易做到的。

# 安装所有依赖项

## 1.芹菜

[https://docs . celery project . org/en/stable/getting-started/introduction . html](https://docs.celeryproject.org/en/stable/getting-started/introduction.html)

```
pip install celery
```

## 2.Celerybeat-mongo

**在 MongoDB 中存储任务:**在 MongoDB 中存储任务我们将使用`celerybeat-mongo`这个在 GitHub 中及时维护的伟大项目。[https://github.com/zmap/celerybeat-mongo](https://github.com/zmap/celerybeat-mongo)

```
pip install celerybeat-mongo
```

## 3.雷迪斯

Redis 是一个经纪人。

```
pip install redis
```

**注意:**对于 windows，同样[下载 MSI 文件](https://github.com/MicrosoftArchive/redis/releases/download/win-3.2.100/Redis-x64-3.2.100.msi)。

## 4.MongoDB

MongoDB 是一个 NoSQL 数据库。

[查看此页面进行安装](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)。

## 5.MongoEngine

要连接 Django 和 MongoDB:

```
pip install mongoengine
```

# 项目目录结构

```
---Blogs
  |---Blogs
    |---__init__.py
    |---settings.py
    |---urls.py
    |---celery.py
  |---UserRegistration
    |---tasks.py
    |---utils.py
  |---BlogTasks
    |---tasks.py
    |---urls.py
    |---views.py 
manage.py
```

# 让我们分三步把这些连接起来

## 1.连接 Django 和 MongoDB

在 settings.py 文件中。`DBName`这里提到的将被创建，如果还没有存在的话。

```
**import** mongoenginemongoengine.connect(db='YourDBName', host='127.0.0.1**', port='27017'**)
```

## 2.连接芹菜和 Redis

在 settings.py 文件中。

```
*# CELERY SETTINGS* CELERY_BROKER_URL = **'redis://localhost:6379/0'** CELERY_ACCEPT_CONTENT = [**'json'**]
CELERY_TASK_SERIALIZER = **'json'** CELERY_RESULT_SERIALIZER = **'json'**
```

## 3.连接芹菜和 MongoDB

在 settings.py 文件中。

```
# CELERY MONGO SETTINGS
CELERY_RESULT_BACKEND = **"mongodb"** CELERY_MONGODB_BACKEND_SETTINGS = {
    **"host"**: **"127.0.0.1"**,
    **"port"**: 27017,
    **"database"**: **"jobs"**,
    **"taskmeta_collection"**: **"stock_taskmeta_collection"**,
}
```

将在您的数据库中创建一个名为 jobs 的集合，该集合将保存由任务执行生成的元数据。

## 芹菜. py

现在我们需要在 MongoDB 中插入一个任务。该任务将由芹菜杆采摘并由工人执行。

要插入一个任务，我们只需要运行一个包含所有任务的方法。检查下面的例子。

## BlogTasks/tasks.py

运行`create_tasks`，这将在`schedule`集合中插入一条记录。在这里你可以看到我们已经创建了一个名为`user-account-creation-task.` 的任务，由`user-account-creation-task`执行的方法出现在`UserRegistration.tasks`位置，方法是`user_account_creation_task`。

`user-account-creation-task`的频率是八小时。但是你也可以做类似`schedule[crontab][‘minute’] = ‘*/5’ and schedule[crontab][‘hour’] = ‘*’`的事情，这意味着每五分钟执行一次任务。但是，如果您没有将其更改为每八小时，那么您的任务将每八小时五分钟运行一次。

另外，请注意，我们使用已经安装的`celerybeat-mongo` GitHub 项目来创建任务、验证数据(使用`PeriodicTaskSerializer`)并保存在 MongoDB 中。

## 用户注册/tasks.py

```
**from** celery **import** task
**from UserRegistration**.utils **import account_creation** @task
**def account_creation**():
    account_creation()
```

## 用户注册/utils.py

```
def account_creation():
    # Apply your logic here
    print('Running account creation task!')
```

现在我们有了所有需要的库和代码文件，我们可以运行 celery worker 并检查结果。

## **运行 mongod 服务**

Linux 环境

```
service mongod start
```

## Windows CMD

在环境变量中添加 mongod 位置

```
C:\Program Files\MongoDB\Server\3.4\bin\
```

使用 cmd `mongod`启动 MongoDB 服务

## **Redis 服务**

Linux 环境

```
service redis start
```

## Windows CMD

在环境变量中添加 redis

```
C:\Program Files\Redis\
```

使用`redis-cli`启动 redis 服务

## **芹菜工**

首先，将您的目录更改为 manage.py 文件所在的目录

```
celery -A Blogs worker -l info
```

如果出现任何错误，安装`eventlet`并运行

```
celery -A Blogs worker -l info -P eventlet
```

## **芹菜打**

首先，将目录更改为 manage.py 文件所在的位置:

```
celery -A Blogs beat -S celerybeatmongo.schedulers.MongoScheduler -l info
```

这里我们使用由`celerybeat-mongo`提供的数据库调度程序。它的作用是从数据库中挑选任务并发送给工人。

如果一切都正确完成，那么在您的 **worker** cmd 中，您将会看到一个打印声明结果`Running account creation task!`

# 为什么我们应该使用数据库调度程序？

在 **CloudGain** 我们更喜欢使用数据库调度程序，因为所有的调度程序都在 MongoDB 中。这意味着我们可以轻松地操纵任务的频率并运行它们，而无需部署。这使得测试任务非常容易。

# 接下来呢？

芹菜非常有效，可以解决你的大部分问题，但它也有代价，那就是内存泄漏。你会在 GitHub 和其他地方发现许多关于这个的错误和问题。

因此，在我的下一篇文章中，我将解释如何检查内存泄漏并处理它们。

如果你有任何建议，请留下评论。