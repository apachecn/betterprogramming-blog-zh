# 从整体服务过渡到微服务

> 原文：<https://betterprogramming.pub/transitioning-from-monolith-to-microservices-428f95c17c3e>

## 通过一个实际的 Django 应用程序示例

![](img/40b699ebf37da89075d30d9879cf7915.png)

Jan Krepík 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去大型单一应用程序开发的日子里，部署应用程序不是一项简单的任务。它从多台服务器的采购订单开始，随后是几天的机架安装、布线和配置。如果你幸运的话，你的部署在第一次尝试时就成功了，因为所有相关方几乎考虑到了所有可能出错的地方。这些参数通过要求最少数量的简单步骤来部署应用程序，为 monoliths 奠定了基础。

快进到今天，您只需在云中点击一个按钮，就可以调配大量基础架构，您会发现自己正生活在基础架构自动化工具的黄金时代。基础设施可用性的这种巨大转变是微服务出现的主要催化剂之一。

# 在以下情况下，您可能不需要微服务架构

微服务的受欢迎程度可见一斑。你很难找到一个以开发人员为中心的供应商，在他们的博客或文档中没有提到微服务。例如，Kong 提供的 [Kong Gateway](https://konghq.com/kong/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community) 和 [Kong Mesh](https://konghq.com/kong-mesh/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community) 专门支持企业从整体架构迁移到[微服务架构](https://konghq.com/learning-center/microservices/microservices-architectures/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community)。

然而，重要的是要知道微服务并不是每个业务应用的灵丹妙药。这里有一些好的指标，表明你目前的整体架构可能是足够的。

## 你在伸缩方面没有困难。

有些业务领域的应用流量相对稳定，可能不需要使用微服务架构带来的动态伸缩。微服务的一个主要优势是按需扩展的能力。如果扩展对您的企业来说不是挑战，您可能不需要微服务。

## 您的整体架构已经足够灵活，可以满足市场需求。

也许你有足够的远见来保持应用程序的灵活性，并根据需要隔离功能。快速引入新特性和功能的能力是微服务的另一个关键原则。如果你已经能够做到这一点，干得好！

## 部署您的应用程序没有问题。

部署速度是微服务的另一个主要优势。如果您的部署管道已经足够快满足您的业务需求，您可能不需要微服务。

# 为什么我应该转向微服务架构？

在评估了你的业务环境后，你可能会决定是否有改进的空间。您可能还记得流量高峰使您的应用程序超载的时候。您可能还记得有一次，一些积压的技术债务需要纠正，并破坏了您的交付时间表。然后，可能有一次你的销售团队在几个月内销售一个新的集成和可用的功能，而实际上，可能需要一年多的时间才能做好。

如果这些情况中的任何一种引起了您的共鸣，那么您的应用可能会从过渡到微服务中受益。让我们考虑一下您组织中的一些关键角色，以及每个角色的主要优势。

*   **企业利益相关者** —这个人关心竞争定位、市场特征和 [Gartner 魔力象限](https://konghq.com/blog/leader-2021-gartner-mq-api-management/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community)。开发定义良好的微服务可以让您的公司更快地部署功能，满足市场需求，并将自己定位为行业领导者，而不是跟随者。
*   **产品或项目经理**——这个人关心团队士气、准确的时间表和支持业务需求。开发定义良好的微服务允许您的经理给出更好的时间表，并通过减少技术债务来保持开发团队的士气。
*   **开发人员**——这个人想要解决有趣的问题，编写新的特性，并且因为他们的商业影响而得到认可。开发定义良好的微服务减少了在一个大型复杂代码库中解决问题的需要，并通过减少与 monolith 开发和部署相关的压力让他们更加满意。

# 是什么推动了向微服务的转变？

上述每个关键人物都可以成为实施微服务的驱动力，但最常见的是，开发团队推动过渡。毕竟，开发人员实现了微服务。导致个体微服务开发的人物角色之间的讨论通常非常抽象，因此重要的是要理解言外之意。

企业利益相关者:我们需要能够更快地对市场做出反应！竞争对手 A 已经有特性 X 了！

产品或项目经理:我们尽力了。微服务部署和开发规划面临许多挑战。

**开发者**:如果我们没有那么多技术债务，我们就能更快地添加新功能。

这些观点(您可能听过类似于您组织中的观点)强调了实施微服务的必要性，但从未使用过微服务这个词！

# 我如何衡量这一过渡的成功？

为了衡量你是否成功地从整体服务过渡到微服务，你*必须*测量、测量、测量。如果没有投资回报(ROI)，就没有理由过渡。只有当你有合适的度量标准时，你才能成功地衡量投资回报率。让我们考虑几个重要的指标。

# 将一个代码变更投入生产需要多长时间？

这是很重要的一条！对于 monolith，即使相对简单的代码更改也需要多层测试来防止回归。考虑这些测试需要多长时间，以及运行这些测试所涉及的人力。然后，一旦测试全部通过，就有了实际的部署。代码一提交就启动秒表，一旦变更在生产中生效就停止秒表。*那个*将是你的微服务开发的衡量标准。

# 你达到 SLA 了吗？

我们在相对宽松的意义上使用 SLA，因为这个术语对不同的业务有不同的含义。

简而言之，让我们将 SLA 定义为主动满足主要业务需求所需的时间。

例如，如果您的企业运行一个针对银行欺诈警报的 API 服务，那么在过去的一个月或一年中，您的服务响应客户 API 请求的可靠性如何？

# 重要的不可测量的事物

虽然硬性数字很重要，但还有其他非常重要但难以正式衡量的指标。

定期对团队进行民意测验，以检查士气并评估团队的整体能量。

他们对甩代码和部署感到兴奋吗？他们的工作更有活力吗？快乐的开发团队是富有成效的开发团队！

# 我如何过渡到微服务？

并非每个企业都采用相同的方法进行微服务过渡。

然而，在高层次上，每个组织都需要一个计划，支持基础设施，支持指标，和一些(喘息！)文化改变。以这些项目为基线，让我们进入细节！

# 想办法

最基本的，您的攻击计划应该包括以下步骤:

**从识别整体结构中的低影响功能开始。**从低影响功能开始，而不是关键业务功能。第一次进行这种转变会有一个轻微的学习曲线，随之而来的是风险因素。如果你在经营一家网上书店，你可能不想首先迁移你的库存或采购系统，因为这些是固有的业务关键功能。将你的图书评级或用户评论转移到你自己的微服务会降低风险。

**确定关键部件和部件之间涉及的交通流量。**确定了功能后，您需要将 monolith 代码库中的哪些组件提取到微服务中？用户将能够直接到达微服务，还是 monolith 将代理请求？

**将所有这些细节放入一个高层次的目标中，将所有的实施步骤整理成文。**通过预测和记录单个功能的过渡过程，您可以更好地审核和重复该过程。

# 使用 API 网关

你的 monolith 应用程序为你的用户提供了一个单一的入口。对于微服务，看起来您现在必须跟踪几个“前门”促进微服务之间以及与外界的通信似乎是一项艰巨的任务。

一个 [API 网关](https://konghq.com/learning-center/api-gateway/?utm_source=guest&utm_medium=devspotlight&utm_campaign=community)抽象出调用支持业务功能的微服务的细节，再次提供了所有请求必须进入的单一“前门”。API 网关处理认证、授权和流量路由到正确的微服务目的地。孔的门道让这变得无比简单。通过几个命令，您将抽象出复杂的通信流量。

# 测量，测量，测量

虽然微服务架构有许多目标，但关键的胜利是灵活性、交付速度和弹性。在为代码提交和生产部署完成之间的差异建立基线后，为微服务测量相同的过程。

类似地，为“业务正常运行时间”建立一个基线，并将其与微服务实施后的基线进行比较。“业务正常运行时间”是您的架构中必要组件所需的正常运行时间，因为它与您的主要业务目标相关。

使用 monolith，您将所有组件部署在一起，因此一个组件的故障可能会影响整个 monolith 应用程序。当您过渡到微服务时，您创建的微服务组件对整体结构中剩余部分的影响应该最小，如果有的话。

例如，如果你的企业是一家书店，你的关键业务是产品(书籍)和支付系统。假设你已经将你的图书评分抽象成一个微服务。在这种情况下，您的业务仍然可以运行，并且如果图书评级服务关闭，影响将会很小，因为您的客户主要想做的是购买图书。

# 做 DevOps

转型的最后一个但极其重要的部分与文化有关。文化变革是困难的，但这是微服务转型成功的必要条件。开发团队不再编写应用程序，然后将它扔给运营部门进行部署。新常态包括 CI/CD 管道、自动化、指标、监控等等。每一个团队成员都需要跨职能和大力协作才能取得成功！

# 示例演示

对于我们的例子，我们将采用一个非常常见的由前端和后端代码组成的整体架构，并展示如何将部分功能提取到它自己的微服务中。

在本例中，我们将使用由 SQLite 数据库支持的 Django 应用程序，该数据库有两个“应用程序”:电影和评级。我们将把评级应用程序提取到托管在 Kong Gateway 后面的自己的微服务中，这是我们使用官方 Kong 图像的[指令](https://hub.docker.com/_/kong)设置的。

# 安装

在这一节中，我们不会对每个命令进行过多的描述，因为唯一的目标是为您提供一个项目模板。

```
$ mkdir moviedb && cd moviedb/
$ python3 -m venv venv && source venv/bin/activate
$ pip install django && pip freeze >> requirements.txt
$ django-admin startproject moviedb .
$ ./manage.py startapp movies
$ ./manage.py startapp ratings
$ ./manage.py migrate
```

至此，我们已经有了一个完整的 Django 设置和一个默认的 SQLite 数据库，并且所有的迁移都已就绪。我们将从两个模型开始:

简单解释一下，我们有两个持久化到数据库的模型:`Movie`和`Rating`。`Rating`有一个与`Movie`模型相关的`movie`字段，用于建立这两个模型之间的关系。在大多数情况下，你会有一部有很多收视率的电影。

从操作上来说，这意味着负载会随着评分数量的增加而增加，从而降低页面的加载速度。我们不会让一部电影的收视率降低网站的速度，而是将收视率转移到他们自己的微服务 Kong Gateway 后面。

为了简单起见，我们将把`ratings`代码移到它自己的 Django 项目(名为`ratings_service`)中，与`moviedb`代码分开。

```
$ mkdir ratings_service && cd ratings_service/
$ python3 -m venv venv && source venv/bin/activate
$ pip install django && pip freeze >> requirements.txt
$ django-admin startproject ratings_service .
$ ./manage.py startapp ratings
$ ./manage.py migrate
$ cp ../moviedb/ratings/models.py ./ratings/
```

我们需要复制的关键部分是`ratings/models.py`文件。从那里，整个`ratings`应用程序可以从`moviedb`项目中移除。

然而，我们确实需要对`Ratings`模型做一个调整，因为我们不再依赖于`Movie`。我们需要改变的只是 ratings 中文本字段的外键引用。

在对模型进行调整之后，我们为模型变更创建并运行迁移。

```
$ ./manage.py makemigrations
$ ./manage.py migrate
```

解释我们为什么做出这种改变是很重要的。专注于功能但在操作上一般化将帮助您从微服务方法中获得最大收益。在这种情况下，我们新的微服务专门从事评级管理，但它是通用的，可以接受任何电影标题并为其保存评级。

为了使评级更加独特——如果有多部同名电影——我们也可以使用一个 [EIDR](https://en.wikipedia.org/wiki/EIDR) 数字。更进一步，我们甚至可以将`movie`字段改为更通用的字段，并保存除电影之外的数据对象的评级！

我们还需要修改最后一个代码。有了`moviedb`项目的评级代码，我们需要发出一个 HTTP 请求，因为我们不能在`Movies`和`Ratings`上进行数据库级的连接。在我们的电影列表视图中，我们需要更新视图类:

```
class MovieListView(ListView):
  model = Moviedef get_queryset(self):
    return Movie.objects.all().select_related()
```

对此:

请注意，这在技术上仍然是一个阻塞调用，因为页面将等待结果。但是，这种方法消除了硬性依赖，允许您围绕这个特定的请求编写逻辑。

最后，我们将在我们的`ratings_service`中创建一个小的`Dockerfile`，在容器运行时与 Kong 一起运行。

```
# Dockerfile
FROM python:3
WORKDIR /home/chad/ratings_service
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD [ "python", "manage.py", "runserver", "0.0.0.0:9000" ]
```

我们还需要在`ratings_service`的设置中设置`ALLOWED_HOSTS = ["*"]`，以确保流量可以通过。

```
# ratings_service/ratings_service/settings.py
ALLOWED_HOSTS = ["*"]$ docker build -t ratings_service .
$ docker run -d -p 9000:9000 ratings_service
```

现在我们已经把`moviedb`和`ratings_service`分开了，我们需要告诉孔如何服务流量。我们将添加一个服务和路由，这样 Kong 就知道如何代理我们的请求:

```
$ curl -i -X POST http://localhost:8001/services/ -d 'name=ratings-service' -d 'url=http://host.docker.internal:9000'
HTTP/1.1 201 Created$ curl -i -X POST http://localhost:8001/services/ratings-service/routes -d 'hosts=ratings-service.local' -d 'paths=/'
HTTP/1.1 201 Created
```

现在，我们可以访问我们的评级服务！

```
$ curl -i -X GET --url http://localhost:8000/ --header "Host: ratings-service.local"
[{
    "movie": "Gone with the Wind",
    "created_at": "2021-08-08 16:01:46",
    "explanation": "Classic movie!",
    "rating_value": 9
}, {
    "movie": "The Little Shop of Horrors",
    "created_at": "2021-08-08 16:01:46",
    "explanation": "I'm a mean green mother from outer space and I'm bad!",
    "rating_value": 7
}]
```

我们有意跳过了数据加载步骤，因为对于本例来说，这些步骤并不十分重要。毫无疑问，你已经有了大量的数据来测试。这里的重点是如何挑选一块 monolith 的功能，提取它，并将其放入自己的代码库，然后将其作为 Kong Gateway 背后的微服务应用。

# 下一步是什么？

在从整体服务过渡到微服务这个复杂的主题中，我们已经涉及了很多内容！首先，我们考虑了一些微服务可能*不*适合您的业务环境的情况。

然后，我们研究了组织过渡到微服务所涉及的利益相关方，接着是组织需要获取的指标，以确定其过渡是否成功。最后，我们通过一个具体的例子介绍了进行这种转变的一些关键实践。

在实践中，有比我们在这个例子中所能得到的更多的东西。然而，我们能够强调集成一个像 Kong 提供的这样的成熟且有弹性的 API 网关的重要性。一旦您实现了 Kong Gateway，您还可以利用插件生态系统来获得更多微服务的好处，如[指标或监控](https://docs.konghq.com/hub/#analytics-monitoring)，以及通过[部署自动化](https://docs.konghq.com/hub/#deployment)实现 DevOps。