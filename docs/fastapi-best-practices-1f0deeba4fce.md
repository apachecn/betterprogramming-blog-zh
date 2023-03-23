# FastAPI 最佳实践

> 原文：<https://betterprogramming.pub/fastapi-best-practices-1f0deeba4fce>

## 我们在初创公司生产了 1.5 年之后，形成了一份坚持己见的最佳实践和惯例清单。

![](img/b42cd7adb284d1c1c2c24babaa8c3750.png)

DALL-E 生成的图像，一个潮人坐在宽大的垂直屏幕前

尽管 FastAPI 是一个很棒的框架，有很棒的文档，但如何为初学者构建更大的项目并不十分明显。

在过去 1.5 年的生产中，我们一直在做一些好的和坏的决定，这些决定极大地影响了我们的开发者体验。其中有一些值得分享。

**内容**

1.  项目结构。一致且可预测
2.  过度使用 Pydantic 进行数据验证
3.  使用数据验证与数据库的依赖关系
4.  分离和重用依赖关系。依赖关系调用被缓存
5.  如果您只有阻塞 I/O 操作，请不要让您的路由异步
6.  迁徙。蒸馏器
7.  background tasks > asyncio . create _ task
8.  小心动态重力场
9.  分块保存文件
10.  如果必须使用 sync SDK，那么在线程池中运行它。

*本文只包含了我们遵循的部分指南，所以请随意查找 github 的原始资源库*[](https://github.com/zhanymkanov/fastapi-best-practices)**，其中有详细的最佳实践的完整列表，已经获得了一些积极的反馈(在*[*r/Python*](https://www.reddit.com/r/Python/comments/wrt7om/fastapi_best_practices/)*中有一天排名第一的热帖，在 GitHub 上第一周就获得了 250 颗星)。**

# *1.项目结构。一致且可预测*

*有许多方法来构建项目，但是最好的结构是一致的、直接的、没有惊喜的结构。*

*   *如果看项目结构不能给你一个项目是关于什么的概念，那么结构可能是不清楚的。*
*   *如果你不得不打开包来理解什么模块位于其中，那么你的结构是不清楚的。*
*   *如果文件的频率和位置感觉是随机的，那么你的项目结构是坏的。*
*   *如果看模块的位置和名字不能让你知道里面是什么，那么你的结构是非常糟糕的。*

*虽然 Sebastián Ramírez 提出的项目结构(其中我们根据文件的类型(例如 api、crud、模型、模式)来分离文件)对于范围较少的微服务或项目来说是很好的，但是我们不能将其放入我们具有大量域和模块的 monolith 中。*

*我发现一个更具可扩展性和可发展性的结构受到了网飞[调度](https://github.com/Netflix/dispatch)的启发，做了一些小的修改。*

```
*fastapi-project
├── alembic/
├── src
│   ├── auth
│   │   ├── router.py
│   │   ├── schemas.py  # pydantic models
│   │   ├── models.py  # db models
│   │   ├── dependencies.py
│   │   ├── config.py  # local configs
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   ├── service.py
│   │   └── utils.py
│   ├── aws
│   │   ├── client.py  # client model for external service communication
│   │   ├── schemas.py
│   │   ├── config.py
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   └── utils.py
│   └── posts
│   │   ├── router.py
│   │   ├── schemas.py
│   │   ├── models.py
│   │   ├── dependencies.py
│   │   ├── constants.py
│   │   ├── exceptions.py
│   │   ├── service.py
│   │   └── utils.py
│   ├── config.py  # global configs
│   ├── models.py  # global models
│   ├── exceptions.py  # global exceptions
│   ├── pagination.py  # global module e.g. pagination
│   ├── database.py  # db connection related stuff
│   └── main.py
├── tests/
│   ├── auth
│   ├── aws
│   └── posts
├── templates/
│   └── index.html
├── requirements
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── .env
├── .gitignore
├── logging.ini
└── alembic.ini*
```

*当一个包需要来自其他包的服务或依赖项时—使用显式模块名称导入它们。*

```
*from src.auth import constants as auth_constants
from src.notifications import service as notification_service
from src.posts.constants import ErrorCode as PostsErrorCode*
```

# *2.过度使用 Pydantic 进行数据验证*

*Pydantic 有一组丰富的特性来验证和转换数据。*

*除了具有默认值的必填和非必填字段等常规功能外，Pydantic 还内置了全面的数据处理工具，如 regex、有限选项的枚举、长度验证、电子邮件验证等。*

# *3.使用数据验证与数据库的依赖关系*

*Pydantic 只能验证客户端输入的值。使用依赖关系根据数据库约束(如电子邮件已存在、找不到用户等)验证数据。*

*额外的好处是，使用一个公共的依赖项消除了为每条路由编写测试来验证 post_id 的需要。*

# *4.分离和重用依赖关系。依赖关系调用被缓存*

*依赖项可以被多次重用，并且不会被重新计算——默认情况下，FastAPI 在请求的范围内缓存依赖项的结果，也就是说，如果我们有一个调用服务`get_post_by_id`的依赖项，我们不会在每次调用这个依赖项时都访问 DB——只有第一次函数调用时。*

*知道了这一点，我们就可以很容易地将依赖关系分解成多个更小的功能，这些功能在一个更小的域上运行，并且更容易在其他路由中重用。例如，在下面的代码中，我们使用了三次`parse_jwt_data`依赖:*

1.  *`valid_owned_post`*
2.  *`valid_active_creator`*
3.  *`get_user_post`，*

*但是`parse_jwt_data`只被调用一次，在第一次调用中。*

# *5.如果您只有阻塞 I/O 操作，请不要让您的路由异步*

*在幕后，FastAPI 可以有效地处理异步和同步 I/O 操作。*

*   *FastAPI 在[线程池](https://en.wikipedia.org/wiki/Thread_pool)中运行`sync`路径，阻塞 I/O 操作不会阻止[事件循环](https://docs.python.org/3/library/asyncio-eventloop.html)执行任务。*
*   *否则，如果路由被定义了`async`，那么它会通过`await`被定期调用，FastAPI 相信你只做非阻塞的 I/O 操作。*

*需要注意的是，如果在异步路由中信任失败并执行阻塞操作，事件循环将无法运行下一个任务，直到阻塞操作完成。*

*第二个警告是，非阻塞可访问的或发送到线程池的操作必须是 I/O 密集型任务(例如，打开文件、数据库调用、外部 API 调用)。*

*   *等待 CPU 密集型任务(例如繁重的计算、数据处理、视频转码)是没有价值的，因为 CPU 必须工作才能完成任务，而 I/O 操作是外部的，服务器在等待该操作完成时什么也不做，因此它可以继续下一个任务。*
*   *由于 [GIL](https://realpython.com/python-gil/) ，在其他线程中运行 CPU 密集型任务也是无效的。简而言之，GIL 一次只允许一个线程工作，这使得它对 CPU 任务毫无用处。*
*   *如果您想优化 CPU 密集型任务，您应该将它们发送给另一个进程中的工作人员。*

# *6.迁徙。阿伦比奇。*

1.  *迁移必须是静态和可逆的。如果您的迁移依赖于动态生成的数据，那么请确保唯一动态的是数据本身，而不是其结构。*
2.  *使用描述性名称和 slugs 生成迁移。Slug 是必需的，并且应该解释这些变化。*
3.  *为新迁移设置人类可读的文件模板。我们使用`*date*_*slug*.py`模式，例如`2022-08-24_post_content_idx.py`*

```
*# alembic.ini
file_template = %%(year)d-%%(month).2d-%%(day).2d_%%(slug)s*
```

# *7.background tasks > asyncio . create _ task*

*背景任务可以像处理路由一样有效地运行阻塞和非阻塞 I/O 操作(`sync`函数在线程池中运行，而`async`函数在后面等待)*

*   *不要对工作人员撒谎，不要将阻塞的 I/O 操作标记为`async`*
*   *不要将它用于繁重的 CPU 密集型任务。*

# *8.分块保存文件*

*不要希望你的客户会发送小文件。*

# *9.小心动态重力场*

*如果您有一个可以接受类型联合的 pydantic 字段，请确保验证器明确知道这些类型之间的区别。*

***不可怕的解决方案:***

1.  *正确排序字段类型:从最严格的到宽松的。*

*2.验证输入只有有效字段。*

*Pydantic 忽略联合类型的 ValueErrors 并迭代它们。如果没有有效的类型，则引发最后一个异常。*

*3.如果字段很简单，请使用 Pydantic 的智能联合(> 1.9 版)*

*如果字段是简单的，比如`int`或`bool`，这是一个很好的解决方案，但是对于复杂的字段，比如类，这是行不通的。*

*没有 Smart Union:*

*使用智能联盟:*

# *10.如果您必须使用 sync SDK，那么在线程池中运行它*

*如果您必须使用 SDK 与外部服务交互，而它不是`async`，那么在外部工作线程中进行 HTTP 调用。*

*举个简单的例子，我们可以使用 starlette 中众所周知的`run_in_threadpool`。*

*FastAPI 是一个工具，可以用来轻松地构建简单得令人捧腹的项目和复杂得令人恐惧的项目。这并不是说缺乏来自上面的约定是不可维护项目的根源，而是缺乏一致性。*

*不管你有什么规则，唯一应该遵守的规则是与你的规则保持一致。找到一套行之有效的约定，迭代它们，并向他人倡导它们。如果您已经有了，请在[问题页面](https://github.com/zhanymkanov/fastapi-best-practices/issues)上与其他人分享。*

```
***Want to Connect?**If you liked the article— follow me on [twitter](http://twitter.com/zhanymkanov), I will share my experience with startups, development, and crypto.*
```