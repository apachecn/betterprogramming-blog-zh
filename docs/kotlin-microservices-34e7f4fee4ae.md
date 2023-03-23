# 科特林微服务

> 原文：<https://betterprogramming.pub/kotlin-microservices-34e7f4fee4ae>

## 从爪哇搬到科特林

![](img/30008658e7de6e72ef039e095b9f0f60.png)

图片由[皮克斯拜](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1647341)的 Gerd Altmann 提供

我现在五十多岁了，又一次面对一种新的语言。有时候我在想，我为什么要自寻烦恼——我本可以轻松地熬到退休。但是我喜欢一门新语言带来的挑战——我喜欢理解它是如何让生活变得更简单的。或者，也许这是我永恒的乐观，有人最终会想出一种通用语言，简单到不需要创造其他语言。

Kotlin 是一种基于 JVM 的语言，很像 Java。事实上，大力推广 Kotlin 的 IntelliJ 为您的旧 Java 类提供了一个很好的“从 Java 转换到 Kotlin”菜单选项，它做得非常好。Kotlin 没有 Scala 的所有缺点，在 Java 生态系统中运行良好。所以我仍然可以使用 Maven(或者 Gradle)来构建我所习惯的插件。另一件好事是，它兼容 Spring Boot 和 WebFlux，这使得我的 WebFlux 项目的迁移变得轻而易举！

科特林有什么不同？

好了，可怕的空值已经被包含了。它还没有完全消失——它仍然需要与 Java 代码进行交互——但是它被更加明确地处理了。

函数也是 Kotlin 中的一级对象。这对我来说没什么大不了的——单抽象方法(SAM)接口总是让我觉得像函数对象一样，这让我很开心。

我们还去除了泛型的通配符类型，主要是通过能够指定泛型中使用的类型是 innie 还是 outie。再说一次，对我来说没什么大不了的，因为我已经习惯了狂野西部的外卡类型。

我要做的是把我现有的一个 Java 微服务转换成 Kotlin。我将使用我的文章[如何使用 Kubernetes Cron Jobs 定期阅读新闻](https://levelup.gitconnected.com/how-to-use-kubernetes-cron-jobs-to-periodically-read-the-news-8b3b4513f8b7)中的 [readnews](https://github.com/rkamradt/readnews/tree/v1.1) 项目作为起点，并从中创建一个新项目 [readnewskotlin](https://github.com/rkamradt/readnewskotlin/tree/v0.0) 。我不会直接复制任何文件，我只是用来参考。我使用 IntelliJ 思想创建了一个新的 Maven 项目，并使用`kotlin-archetype-jvm`生成所需的文件。如果您愿意，您可以设置一个配置来运行它，只是为了确保一切正常。

那么，我们如何从 Java 微服务转变为 Kotlin 微服务呢？如果您使用 Maven，第一步是引入 Kotlin 编译器并禁用 Java 编译器。这是通过包含两个插件完成的，一个是`kotlin-maven-plugin`，另一个是`maven-compiler-plugin`。我来自 Maven 原型的项目为我添加了`kotlin-maven-plugin`，但是我添加了`maven-compiler-plugin`和一些我通常使用的其他东西。所以我的 pom.xml 的插件部分现在看起来像这样:

我需要将 Spring Boot 父库和依赖项添加到 Kotlin 标准库依赖项中，所以我用下面的代码替换了依赖项部分:

这给了我在 Kotlin 开始创建微服务所需的一切。

我做的下一件事是删除原型为我生成的代码，尽管我保留了目录结构。对此，我添加了 Spring 想要的应用程序类，以了解从哪里开始。我用以下定义创建了一个文件`ReadNewsApplication.kt`:

```
[@SpringBootApplicatio](http://twitter.com/SpringBootApplicatio)n
class ReadNewsApplicationfun main(args: Array<String>) {
    SpringApplication.run(ReadNewsApplication::class.java, *args)
}
```

注意，main 函数在类之外(类是空的),因为 Kotlin 没有静态函数。

让我们看看我们在这个服务中使用的数据类。我把这个文件叫做`Inserts.kt`:

```
class Inserts(
    [@Id](http://twitter.com/Id) val id: String,
    val status: String? = null,
    val totalResults: Int? = null,
    val articles: List<Articles>? = null)class Articles(
    val source: Source? = null,
    val author: String? = null,
    val title: String? = null,
    val description: String? = null,
    val url: String? = null,
    val urlToImage: String? = null,
    val publishedAt: String? = null,
    val content: String? = null)class Source (
    private val id: String? = null,
    private val name: String? = null)
```

Kotlin 有一个叫做`data class`的特殊类类型，我们可能用过，但是，它生成的访问器函数与我们正在使用的 MongoDB 驱动程序不兼容。因此，我们使用简单的类和主构造函数中定义的字段。注意，主构造函数只是附加在类名后面，所有的初始值都是在那里设置的。另外，注意类型后面的`?`——这表示这是一个可以有 null 的值。

但是科特林不是把 null 去掉了吗？不—它仍然需要处理来自任何 Java 库的输出。我们指定的方式是将问号附加到类型上，这样编译器就知道在必要时强制执行空检查。如果没有空值，我们如何在 Kotlin 中进行空值检查？猫王操作员`?:`(之所以这么叫，是因为侧身表情符号看起来像头发蓬松的猫王！)会有所帮助。拿这个函数来说:

```
fun deNull(x: String?):String = x ?: "elvis"
```

在这种情况下，x 可以是 null(因为它的类型是`String?`)，所以函数使用`?:`返回不为 null 的 x 和为 null 的 Elvis。返回类型是没有`?`的`String`,所以你永远不用担心检查它是否为空——编译器可以放松一下，喝一杯。

除了关键字`implements`被替换为`:`之外，数据库存储库接口保持不变:

```
@Repository
interface InsertsReactiveRepository : 
                ReactiveMongoRepository<Inserts, String>
```

接下来，我们可以进入控制器类。首先，这是 Java 版本:

```
@RestController
@RequestMapping("/v1/headlines")
public class ReadHeadlinesControllerV1 {
    private static final int *MAX_LIMIT* = 1000;

    private final InsertsReactiveRepository newsReactiveRepository;

    ReadHeadlinesControllerV1(
        final InsertsReactiveRepository newsReactiveRepository) {
        this.newsReactiveRepository = newsReactiveRepository;
    }

    @GetMapping(path="", 
          produces = MediaType.*TEXT_EVENT_STREAM_VALUE*)
    Flux<Inserts.Articles> getFromMongo(final Instant from, 
                final Instant to, 
                final Long limit) {
        long actualLimit = limit == null 
                 || limit == 0 
                 || limit > *MAX_LIMIT* ? *MAX_LIMIT* : limit;
        return newsReactiveRepository
                .findAll()
                .flatMap(r -> Flux.*fromIterable*(r.getArticles()))
                .filter(r -> filterByDate(r, from, to))
                .limitRequest(actualLimit);
    }

    private boolean filterByDate(
           final Inserts.Articles record,
           final Instant from,
           Instant to) {
        if(record == null || record.getPublishedAt() == null) {
            return false;
        }
        Instant theDate;
        try {
            theDate = Instant.*parse*(record.getPublishedAt());
        } catch(Exception ex) {
            return false;
        }
        return (from == null || theDate.isAfter(from)) &&
            (to == null || theDate.isBefore(to));
    }

}
```

这是科特林的版本:

```
@RestController
@RequestMapping("/v1/headlines")
class ReadHeadlinesControllerV1(
    val newsReactiveRepository: InsertsReactiveRepository) {
    val maxLimit = 1000L

    @GetMapping(path = [""], 
             produces = [MediaType.*TEXT_EVENT_STREAM_VALUE*])
    fun getFromMongo(
       @RequestParam(value="from", defaultValue="") from: String,
       @RequestParam(value="to", defaultValue="") to: String,
       @RequestParam(value="limit", defaultValue="1000") limit: Long): Flux<Articles> =
        newsReactiveRepository
            .findAll()
            .flatMap **{** r **->** Flux.fromIterable(r.articles) **}** .filter **{** r **->** filterByDate(Instant.parse(r.publishedAt), from, to) **}** .limitRequest(if (limit > maxLimit) maxLimit else limit)

    fun filterByDate(published: Instant, from: String, to: String) =
        from.*isBlank*() || published.isAfter(Instant.parse(from))
            && to.*isBlank*() || published.isBefore(Instant.parse(to))

}
```

在不损失信息的情况下更简洁一点。我应该指出几件事。

紧跟在类名后面的主构造函数是一个很好的节省空间的装置，非常直观。对于`flatMap`和`filter`函数，如果最后一个参数是函数，可以放在括号外面(这种情况下函数是唯一的参数，所以省略括号)。

为了测试它，我从`readnews`项目中复制了`docker-compose.yaml`，它将启动 MongoDB 和我在上一篇文章中创建的新闻阅读应用程序。您需要从[newsapi.org](https://newsapi.org/)获得一个密钥，并将其设置为`NEWSAPI_KEY`环境变量。然后你可以启动 MongoDB 并运行新闻阅读应用程序:

```
docker-compose up -d mongodb
docker-compose up app
```

我必须分别启动它们，以便 MongoDB 有机会在应用程序试图插入之前启动。应用程序应该运行，吐出一些日志，然后退出。现在你可以启动新的应用程序了。在 IntelliJ IDEA 中，您可以创建一个新的配置，将它指向主类，然后运行它。一旦它启动，你就可以卷曲:

```
curl http://localhost:8080/v1/headlines
```

它应该会把你不想看的新闻都删掉。不要忘了之后的`docker-compose down`来阻止 MongoDB 成为你笔记本中不受欢迎的客人。

这就是创建一个简单的 Kotlin 微服务的全部内容。显然，这并不是 Kotlin 的全部，但是我将继续探索它，并找到使用它来简化我作为开发人员的生活的方法。老实说，我很高兴我完成了这个练习——kot Lin 似乎是对 Java 的真正改进，没有 Scala 的可读性。

感谢您在我努力完成未来的又一次迭代时一直支持这位老人！

本文中使用的存储库:

[](https://github.com/rkamradt/readnewskotlin/tree/v0.0) [## rkamradt/readnewskotlin

### 科特林 readnews 服务的一个版本。创建一个帐户，为 rkamradt/readnewskotlin 的开发做出贡献…

github.com](https://github.com/rkamradt/readnewskotlin/tree/v0.0) [](https://github.com/rkamradt/readnews/tree/v1.1) [## rkamradt/readnews

### 阅读存储在 mongo 数据库中的新闻。在 GitHub 上创建一个帐户，为 rkamradt/readnews 的开发做出贡献。

github.com](https://github.com/rkamradt/readnews/tree/v1.1) 

本文引用的文章:

[](https://levelup.gitconnected.com/how-to-use-kubernetes-cron-jobs-to-periodically-read-the-news-8b3b4513f8b7) [## 如何使用 Kubernetes Cron Jobs 定期阅读新闻

### 阅读新闻的微服务。

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-use-kubernetes-cron-jobs-to-periodically-read-the-news-8b3b4513f8b7)