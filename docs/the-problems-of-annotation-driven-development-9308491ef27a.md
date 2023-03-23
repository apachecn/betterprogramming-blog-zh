# 注释驱动开发的问题

> 原文：<https://betterprogramming.pub/the-problems-of-annotation-driven-development-9308491ef27a>

## 关于依赖注释的陷阱的讨论

![](img/e7f60b1a9a2e11f9d6a281fe1853ff96.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将讨论当我们在应用程序中过度使用注释时出现的问题，我们称之为“注释驱动开发”或“框架驱动开发”使用带有注释的框架有各种各样的理由，尽管框架也会给我们带来相当多的问题。

我们将尝试解释使用框架注释所固有的所有复杂性，但同时，尝试保持客观的观点以避免影响您的决策。

框架在很多时候非常有用，但是一如既往，任何过度的东西都是有害的。

开始吧！

# 介绍

在过去的十年中，像 [Spring](https://spring.io/) 这样的框架已经在软件开发者社区中广泛使用。使用这种框架为我们带来了巨大的好处，尤其是在创建新项目时，我们节省了大量的时间。

问题是，它们只给我们的代码库带来好处吗？大多数社区认为大多数时候没有缺点。今天，我们将试着向你解释每一个决定都有利弊。甚至采用像 Spring 这样非常流行的框架！

# 他们有什么好的

总的来说，框架的好处是它们已经被成千上万的公司广泛采用并应用于生产中。这意味着它已经在生产中经过了彻底的测试，这让我们在应用中采用它们时充满了信心。

另一个很大的优势是，我们可以通过使用注释来配置应用程序中的所有内容，从而节省大量时间。例如，我们可以使用 [Spring 数据](https://spring.io/projects/spring-data)让我们的持久层在几分钟内工作，只需要一个接口和一些注释，并且我们已经准备好测试我们的持久层。

除了节省我们宝贵的时间之外，Spring abstracts(尤其是对经验不足的开发人员而言)是我们在构建软件时经常遇到的一些问题。我们这样说是什么意思？例如，Spring 将 [Resiliency4j](https://resilience4j.readme.io/docs) 库集成到它的一个模块中，允许开发人员通过使用注释来配置弹性模式。这为一些不完全理解如何实现这些模式的开发人员简化了事情。

另一个好处是，在使用框架时，更容易实现团队间的一致性，尤其是在大型组织中。

![](img/2d4960a7b943c59c6928a37e0706e4b8.png)

安娜斯塔西娅·切平斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

能够使用一个注释，然后“神奇地”得到我们工作所需的一切，这种能力是非常诱人和令人满意的，主要是因为我们节省了时间，而且我们几乎立即就能获得成就感。

到目前为止，一切听起来都很棒，尽管我们不得不提醒你“闪光的不一定是金子”！

尽管我们前面提到了这些好处，但我们可能会想到几个问题:

*   当我们使用这些注释时，我们理解 Spring 在幕后做什么吗？
*   当我们使用它们时，我们是否完全理解了性能、资源使用或可维护性的所有可能含义？
*   假设一个新加入者不知道这些注释做什么，他们有多大可能快速理解我们的代码？
*   如果在生产中出现问题，那么当代码在生产中运行时，是否有足够的清晰度和可见性？

如果这些问题的答案是否定的，我们认为我们迟早会有一个大问题。

我们来看看为什么！

# 问题

## **框架开发者障碍**

在我们的领域中，我们遇到了许多被认为是“框架开发人员”的开发人员这是什么意思呢？我们的意思是，这些开发人员主要依靠一个给定的框架来开发他们在日常工作中必须做的任何事情。他们甚至不能停下来一分钟，想一想，是否有一种更简单、更清晰的方法来达到同样的目的，不使用注释，甚至不使用框架。在某种程度上，他们唯一的权力就是这个特殊的框架。

他们似乎不太相信自己，如果不依靠他们最喜欢的框架，就无法用简单的语言编写一段简单的代码。我们认为任何增加到我们代码库的开销都应该被仔细考虑。不仅仅是框架，甚至是库。

很好地理解内部机制以预测性能问题甚至错误行为是非常重要的。

## 没有意识到引入新的弱点

每个框架都会将大量的依赖项引入到我们的应用程序中，一旦导入，依赖项越多，我们的应用程序就越容易受到未来或现有漏洞的攻击。

这意味着，对于每一个额外的依赖，潜在的攻击者都有额外的机会破坏我们的系统，特别是如果我们没有定期扫描我们的依赖来寻找新的漏洞。

随着 Spring Boot 及其启动包的推出，范围已经大大缩小。现在，我们可以选择要在应用程序中包含哪些依赖项或包。

我们的一般建议是将你的依赖保持在最低限度。这将减少受漏洞影响的机会。

## **一切神奇地发生**

到目前为止我们看到的一切并不是我们能找到的唯一问题。当我们使用一个框架时，我们通常不知道框架在幕后做什么。我们甚至不知道它在内部使用了什么库，或者使用了什么语言来实现这个功能。到处都有魔法。如果你对框架和代码库都不熟悉，你就不可能理解甚至跟随代码！

我们甚至看到开发人员在代码评审中批评一段代码，同时利用一个框架注释，该注释在内部编译成他们抱怨的同一段代码！他们不知道一旦注释在编译时都被处理了，编译后的代码会是什么样子。

![](img/5a23b2745cd175dd44c0abcd9dbc1d02.png)

照片由[朱利叶斯·德罗斯特](https://unsplash.com/@juliusdrost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/playing-cards?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

## **性能影响**

这可能会变得更糟，一些注释只能在运行时处理，如果它们的保留策略已经被定义为[运行时](https://docs.oracle.com/javase/7/docs/api/java/lang/annotation/RetentionPolicy.html#RUNTIME)。在许多情况下，我们的代码必须通过一个方面或代理来执行。在许多情况下，这可能会影响性能，但是没有多少开发人员关心这个问题。很少看到开发人员在采用注释之前在内部仔细检查注释的功能。他们只关心目标，而不关心实现目标的手段。

例如，最近，我们注意到在我们的一个项目中，一些开发人员正在使用由 [Resiliency4j](https://resilience4j.readme.io/docs) 提供的`[@Retry](https://docs.spring.io/spring-batch/docs/current/reference/html/retry.html)`注释。问了团队之后，没有人知道那个注释在内部做什么。

快速看了一下，似乎这个注释是在运行时处理的。在这种情况下将会发生的是，每个用`[@Retry](https://docs.spring.io/spring-batch/docs/current/reference/html/retry.html)`标注的类都将通过一个名为`[RetryAspect](https://github.com/resilience4j/resilience4j/blob/master/resilience4j-spring/src/main/java/io/github/resilience4j/retry/configure/RetryAspect.java)`的方面进行处理。如果我们的应用程序每秒收到大量请求，而我们需要低延迟，这可能会影响性能。

像这样的事情的实际效果必须在你的系统中用[性能测试](https://en.wikipedia.org/wiki/Software_performance_testing)或者甚至是[微基准](https://en.wiktionary.org/wiki/microbenchmark)来测试。这将告诉你它是否会影响你的系统性能。

在这种情况下，我们强烈建议使用这个库，但是以编程方式引导。这样，一切都在一个地方，，我们可以很容易地阅读和理解它是如何工作的；不需要方面或者复杂的解决方案。

## **不必要的注释**

即使在编译时处理注释的情况下，如果我们可以用相似数量的代码实现相同的事情，那么将注释和它们自己的处理器一起使用也没有意义。这可能会降低你的编译时间，考虑到我们一天要编译多次，所以尽可能避免它们可能会更好。

我们知道使用注释是“酷”的，但是我们并没有因为酷而得到报酬。你要记住，每一个决定总会有后果，有些是坏的，有些是好的。关键是找到缺点更少的解决方案，能够满足您的要求，并且只使用您需要的东西。

与使用编译时处理的注释相关的一个例子可以是在 [Mockito](https://site.mockito.org/) 中使用`[@Mock](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mock.html)` [注释](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mock.html)。使用它们的真正好处是什么？我们可以在一行中初始化一个 mock，并将所有的 mock 注入到实现的构造函数中。这样，代码中的一切都是显式的。因此，它更容易阅读，最重要的是，我敢打赌你的单元测试会运行得更快。

此外，我们被迫使用 [MockitoExtension](https://www.javadoc.io/static/org.mockito/mockito-junit-jupiter/4.10.0/org/mockito/junit/jupiter/MockitoExtension.html) 进行单元测试。当我们运行单元测试时，所有这些开销都会增加。你不相信我们？让我们看一个例子。

我们将首先包括一个使用 [MockitoExtension](https://www.javadoc.io/static/org.mockito/mockito-junit-jupiter/4.10.0/org/mockito/junit/jupiter/MockitoExtension.html) 和`[@Mock](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mock.html)` [注释](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mock.html)的单元测试示例。该测试将从客户端 API 中的现有 IP 返回一个国家代码。

```
@ExtendWith(MockitoExtension.class)
class GeoLocalisationClientTest {
    @Mock
    private RestTemplate restTemplate;
    @Mock
    private APIConfiguration apiConfig;
    @Mock
    private RequestIdProvider requestIdProvider;
    @Mock
    private MetricsRecorder metricsRecorder;
    @InjectMocks
    private GeoLocalisationClient client;

    @Test
    void shouldReturnAValidCountryCode() {

        CountryData countryData = new CountryData("nl");
        Location location = new Location(countryData);
        GeoLocalisationResponse response = new GeoLocalistionResponse(location);
        IpInfo ipInfo = new IpInfo(location);
        when(apiConfig.getIpInfoPath()).thenReturn("ipinfo/{ipAddress}");
        when(restTemplate.exchange(eq("ipinfo/{ipAddress}"),
                eq(GET),
                any(),
                eq(GeoLocalisationResponse.class),
                eq("1.2.3.4")))
                .thenReturn(ResponseEntity.ok(response));
        String countryCode = client.getCountryCode("1.2.3.4");
        assertThat(countryCode).isEqualTo("NL");
    }
}
```

如你所见，我们使用`@Mock`注释初始化所有的模拟，然后使用`[@InjectMocks](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/InjectMocks.html)`实例化我们的客户端并注入之前定义的模拟。

如果我们在 IDE 中运行这个单元测试，每次执行大约需要 350-420 毫秒。

现在让我们试着删除这些注释，看看我们的测试会是什么样子:

```
class GeoLocalisationClientTest {
    private RestTemplate restTemplate = mock(RestTemplate.class);
    private APIConfiguration apiConfig = mock(APIConfiguration.class);
    private RequestIdProvider requestIdProvider = mock(RequestIdProvider.class);
    private GeoLocalisationClient client = new GeoLocalisationClient(restTemplate, apiConfig, requestIdProvider);

    @Test
    void shouldReturnAValidCountryCode() {
        CountryData countryData = new CountryData("nl");
        Location location = new Location(countryData);
        GeoLocalisationResponse response = new GeoLocalistionResponse(location);
        IpInfo ipInfo = new IpInfo(location);
        when(apiConfig.getIpInfoPath()).thenReturn("ipinfo/{ipAddress}");
        when(restTemplate.exchange(eq("ipinfo/{ipAddress}"),
                eq(GET),
                any(),
                eq(GeoLocalisationResponse.class),
                eq("1.2.3.4")))
                .thenReturn(ResponseEntity.ok(response));
        String countryCode = client.getCountryCode("1.2.3.4");
        assertThat(countryCode).isEqualTo("NL");
    }
}
```

你认为我们的测试看起来更复杂，更长，还是更难阅读？我们不这么认为。那么，这些注释的真正好处是什么呢？

对你们许多人来说，令人震惊的是同样的测试所花的时间。如果我们在没有注释和测试运行程序的情况下运行相同的测试，我们可以看到我们的测试大约在 35-45 毫秒内运行！这是一个巨大的进步。

您可以看到注释的处理在最好的情况下是如何影响我们的生产率的。在某些情况下，它甚至会影响我们应用程序的性能。所以请只在真正需要的时候使用它们。在做出任何影响你的代码库的决定之前，要三思而行，不要因为别人做了同样的事情就去做。

然而，我们例子中的问题并不止于此。这个例子来自我们最近几个月参与的一个项目中的真实单元测试。您会注意到，在我们的第一个测试中，已经为一个`MetricsRecorder`类实例化了一个 mock。问题是，这种依赖性在我们的组件中甚至不存在！那它为什么会在那里？

![](img/4706554857926527c995ae063ffce813.png)

图片由[埃德温·安德拉德在 Unsplash](https://unsplash.com/photos/4V1dC_eoCwg) 上拍摄

使用`@InjectMocks`的一个问题是，有额外的未使用的依赖关系并不重要。在这种情况下，可能发生的情况是，从我们的组件中移除了依赖关系，但是由于使用了模拟注释，所以无法注意到这一点。

如果我们在测试中使用普通的构造函数，这种情况会被立即标记出来，因为依赖关系会从组件的构造函数中移除，并且它甚至不会被编译！

因此，这些注释不仅是不必要的，没有增加任何实际价值，而且还会给我们带来一些问题。

## **缺乏可读性**

使用框架和注释的另一个问题是，遵循代码流可能会变得困难。使用注释迫使我们从一个地方跳到另一个地方，并在我们的代码库中进行搜索，以了解一切都是在哪里配置的。

当我们以编程方式配置和实例化所有东西时，我们永远不会有这个问题。即使是不熟悉我们代码库的人也可以很容易地理解这个流程。

## **调试变得困难**

框架和注释的另一个问题是调试变得困难。当出现问题时，我们必须深入研究框架的代码，并付出额外的努力来理解和调试代码，以解决我们的问题。

这意味着，随着开发人员花费更多时间调查问题，修复问题变得更加昂贵。这对任何组织来说都是非常重要的。

# 结论

本文讨论了使用(或过度使用)框架注释的一些好处和问题。我们试图对框架给我们带来的好处保持客观的看法，但同时，我们必须诚实地面对我们在职业生涯中遇到的所有问题。

在结束之前澄清一下，我们并不是说你不应该使用框架或注释。我们的意思是，你应该小心使用它们，只有当它们提供真正的价值，并且它们带来的问题比解决方案多的时候才应用它们！

如果你有兴趣阅读更多我们的 Java 文章，你可以在这里找到它们。

这就是我们今天的全部内容！我们很喜欢写这篇文章，我们希望你也像我们写这篇文章一样喜欢阅读它！

更多文章敬请关注。我们希望尽快见到你回来！

感谢阅读！