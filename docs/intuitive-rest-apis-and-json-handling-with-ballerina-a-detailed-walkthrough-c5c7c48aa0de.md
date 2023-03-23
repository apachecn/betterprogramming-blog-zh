# 借助芭蕾舞演员编程语言实现直观的 REST APIs 和 JSON 处理

> 原文：<https://betterprogramming.pub/intuitive-rest-apis-and-json-handling-with-ballerina-a-detailed-walkthrough-c5c7c48aa0de>

## 详细的演练

![](img/fe843c4a1b507bd5cf93a691fc517ba8.png)

[**芭蕾舞演员**](https://ballerina.io/?utm_source=me&utm_medium=link&utm_campaign=me_link_IntuitiveRESTAPIsblog_220525) 编程语言的利基在于，它被设计成允许开发人员轻松使用、组合和创建网络服务来构建真实世界的云原生应用。换句话说，它是一种编程语言，为专注于为网络交互创建新 API、新集成和新逻辑的开发人员提供了正确的工具集和抽象级别。最新的天鹅湖版本进一步简化了这些方面。

使用 Ballerina 可以永远改变你开发和解决 API 问题的方式。我打算通过一个简单而有趣的例子向您展示如何用 Ballerina 使用和创建 REST APIs。

# REST 和 JSON

REST APIs 已经成为公司在 web 上构建 web 服务和启动开发人员平台的事实上的标准，因为它们易于构建和使用。REST 使用标准的 CRUD HTTP 动词(GET、POST、PUT、DELETE)并利用以数据资源为中心的 HTTP 约定(HTTP URIs)。例如，拥有 api.exampleshop.com/orders*资源*的零售商可以表现得与拥有 api.examplerestaurant.com/reviews 资源*资源* 的餐馆类似。两者都需要对该资源进行 CRUD 操作，并且更喜欢缓存查询。虽然 Java、Python、Ruby、Perl、C、C++、Javascript 和许多其他语言都可以用来构建 REST 端点，并且都有很好的工作效果，但是它们并不是专门为处理网络资源而构建的。它们没有提供内置于语言中的正确的工具或抽象集，而这些工具或抽象集可以使 API 开发人员的生活变得容易得多。

另一方面，有人可能会说 GraphQL 修复了用户在 RESTful 架构中发现的许多问题，并且它已经风靡全球。但是 GraphQL 也有自己的一系列挑战。REST vs GraphQL 是一个值得专门撰写一篇文章的主题，并且已经有很多相关的文章。(是的，芭蕾舞演员也广泛支持 GraphQL。)

JSON 表示基于 JavaScript 对象语法的结构化数据，是人类可读的，并且是 web 上最广泛使用的数据交换格式。JSON 以字符串的形式存在，当您想要通过网络传输数据时，这很有用。当您想使用点/括号符号访问其中的数据时，必须将 JSON 字符串解析(或反序列化)为某种对象或语言中的映射表示。这也称为数据绑定。

# 诺贝尔奖获得者 API

重要的事情先来。让我们来讨论 API 及其数据，我将利用它们来解释芭蕾舞演员语言的一些显著特征。

Nobelprize.org[向开发者免费提供关于诺贝尔奖和诺贝尔奖获得者的公开数据。让我们探究这些数据，了解一下获奖者及其成就。我将使用](https://www.nobelprize.org/about/developer-zone-2/) [**API 版本 1**](https://nobelprize.readme.io/v1.0) ，它以 JSON 或 CSV 的形式返回数据，我将使用**l**[**aureate**](https://nobelprize.readme.io/docs/laureate)方法，它允许任何人查找并列出所有诺贝尔奖获得者(个人和组织)。

您可以使用 cURL 从 laureate 端点检索数据。在终端中键入以下命令:

```
curl [https://api.nobelprize.org/v1/laureate.json](https://api.nobelprize.org/v1/laureate.json)
```

您将能够获取一个获奖者的 JSON 数组:

```
{
 “laureates”: [
  {"id": "1", .. },
  {"id": "2", .. },
  {"id": "3", .. },
   ..
  {"id": "1009", .. }
 ]
}
```

单个获奖者由以下 JSON 结构表示:

一个 laureate JSON 对象带有几个基本字段以及几个嵌套的数组和对象。

# 芭蕾舞演员对 REST APIs 和 JSON 的一流支持

*关于如何建立 nobel_prize 项目以运行代码的指导，*阅读这篇[帖子](https://medium.com/@duckster/setting-up-the-nobel-prize-ballerina-project-3237387907eb)。

## 消费一个 API

您必须首先创建一个客户端对象`nobelPrizes`，以便在 Ballerina 程序中使用 HTTP 服务，如下所示。客户端具有表示与远程系统的出站交互的远程方法。提取的有效载荷被分配给变量`laureateResults`(我将在下一节讨论`json`数据类型)。`[check](https://ballerina.io/learn/language-basics/#check-expression)`表达式提供了一种简洁地处理芭蕾舞演员错误的机制。

## **内置 JSON**

Ballerina 配备了一个网络友好型系统，具有强大的功能来处理网络上的数据，因为 JSON 是 Ballerina 中的“通用语言”——Ballerina 中的数据类型与 JSON 非常接近。这允许来自网络的 JSON 有效负载立即进入语言，并且在没有转换或反序列化的情况下被操作。例如，您可以在 Ballerina 中从 JSON 转换为`[json](https://ballerina.io/learn/distinctive-language-features/data/#json-type)`类型，或者转换为内置类型的用户定义子类型，如`[map](https://ballerina.io/learn/language-basics/#maps)`或`[record](https://ballerina.io/learn/language-basics/#records)`。之后，您可以处理这些数据，并将其转换回 JSON。点击阅读更多关于 Ballerina 如何支持各种类型的网络数据[的信息。](https://ballerina.io/learn/distinctive-language-features/data/)

您可以通过两种方式将这些数据引入芭蕾舞演员计划:

1.  使用`json`类型:

**注意**:允许通过点符号对`json`类型的变量进行字段访问，即使我们不知道编译时的字段是什么，但是您需要通过`ensureType`函数安全地将值转换为类型。`ensureType`以与类型转换表达式相同的方式将值转换为类型，但如果转换无法完成，则返回错误。该错误可以显式处理，或者通过使用`[check](https://ballerina.io/learn/language-basics/#check-expression)`表达式来处理。更多信息见[处理错误](https://ballerina.io/learn/language-basics/#error-handling)。

2.使用`record`类型:

注意:一个`[record](https://ballerina.io/learn/language-basics/#records)`是一个特定类型字段的集合。使用记录类型，您可以控制您的键是什么。通常，记录类型与类型定义相结合，默认情况下，Ballerina 中声明的记录类型是 [open](https://ballerina.io/learn/distinctive-language-features/data/#open-records) 。这意味着您可以向其中添加比指定字段更多的字段。芭蕾舞演员也允许[封闭记录](https://ballerina.io/learn/distinctive-language-features/data/#control-openness)，它只能有一组固定的字段。

在这种情况下，我们有四种记录类型:

1.  `**LaureateResults**` —保存获奖者的根数组。
2.  `**Laureate**`——保存获奖者信息。
3.  `**Prize**`——保存有关奖项详情的信息(尽管这种情况很少见，但一个获奖者一生中可能会获得不止一个诺贝尔奖。已经有四位这样的获奖者了！)
4.  `**Affiliation**`——保存获奖者所属组织或教育机构的信息，这些组织或机构支持他们的努力，使他们获得诺贝尔奖。

如果您检查 JSON 数据层次结构中代表一个获奖者的字段，您会看到我在记录中也使用了相同的字段名称。这允许一个桂冠诗人被自动映射到一个`**Laureate**` 记录。

您将看到一些字段以`?`为后缀。这些是[可选字段](https://ballerina.io/learn/distinctive-language-features/data/#optional-fields)，这意味着它们有时可能会丢失。例如，如果他们是一个组织，一些获奖者可能没有`surname`，如果他们仍然活着，可能没有`diedCountry`，如果他们独立工作，可能没有`affiliation`。芭蕾舞演员的类型系统是灵活的，就像一种图式语言。其中一种方法是使用可选字段。

你可能也注意到了`(Affiliation|json[0])[] affiliations?`。这表明从属关系是一个可选字段。此外，返回类型将是`Affiliation[]`和`json[0]`的并集(这意味着从属关系可以保存记录类型从属关系的数组或 JSON 数组)。这样做是为了将附属关系数据转换成一个`Affiliation`记录数组，或者根据 JSON 有效负载包含它的方式将其留空:

或者

当 API 被调用时，整个 JSON 有效负载被分配给一个`LaureateResults`记录，获奖者的数组将被映射到`laureates`数组。您可以通过上面代码所示的`[foreach](https://ballerina.io/learn/language-basics/#foreach-statement)`语句访问各个获奖者。

`foreach Laureate {id, firstname, …rest}`中的`[spread operator](https://ballerina.io/learn/distinctive-language-features/advanced-general-purpose-language-features/#spread-operator-x)` `…rest`允许通过展开`Laureate`类型的成员进行析构，其中`rest`是包含`Laureate`未指定成员的列表或映射(例如`surname`、`born`、`died`等)。).这相当于用逗号分隔它们。

由于`surname`字段是可选的，我们必须首先检查它是否可用，如果不可用，就分配一个空字符串，就像这样:`string surname = rest.surname ?: “”;`

在本文的其余部分，我将继续使用记录的方法，因为它更简单，更适合我们的用例。

## **用查询表达式进行 JSON 操作**

让我们假设您想要将同一组获奖者返回给另一个程序，但是具有有限的字段集。假设您想要删除字段`bornCountryCode`、`bornCity`、`died`、`diedCountry`、`diedCountryCode`和`diedCity`，并添加一个名为`isAlive`的新字段来指示获奖者是否仍然活着。

下面是允许您这样做的代码:

*为了突出该语言的灵活性和动态性，以及它对 JSON 操作的内置支持，我使用了相同的记录类型，但是您也可以创建一个不同的记录类型，它可以是一个封闭的记录，来处理来自* `**Laureate**` *记录类型的必需字段。*

## 笔记

我已经在`laureates`数组上使用了一个[查询表达式](https://ballerina.io/why-ballerina/data-oriented/#query-expressions)来修改其中的每一个桂冠记录，以包含选定的字段，并将它们添加到一个名为`formattedLaureates`的新的`Laureate`数组中。在处理数据时，查询表达式非常有用，它包含一组类似于 SQL 的子句来处理数据。它们以`from`子句开始，可以执行各种操作，比如过滤、连接、排序、限制和投影。`from`子句用于定义输入迭代器源(在本例中为`laureates`),用于处理数据。

`let`子句允许您定义只能在查询表达式范围内使用的变量。我已经定义了这样一个变量`isAlive`，它基于一个`Laureate`记录的`died`字段的日期字符串值来保存 true 或 false。

最后，使用`select`子句，我们可以定义哪些字段应该出现在`formattedLaureates`数组中的每个`Laureate`记录中，包括新的`isAlive`字段。记得我提到过记录是默认打开的吗？由于开放记录所提供灵活性，我们能够向`Laureate`记录引入一个新的字段。如果这是一个封闭的记录，我们就不可能做到这一点。

# 创建一个 REST API

除了网络感知类型系统，Ballerina 还提供了用于网络服务的基本语法抽象。如果你想创建一个服务，你可以定义服务类型来产生服务，一个服务只用三四行芭蕾舞代码就可以写出来。

服务对象被附加到监听器，比如 HTTP(甚至 GraphQL 或 gRPC)，并接收网络输入，比如 JSON 有效负载。资源函数由 HTTP 方法(例如 GET、POST、PUT)和一个名词(例如 laureate、order 或 review)命名。下面是一个关于如何为获奖者数据创建自己的 REST API 的基本示例:

# 将所有这些放在您自己的 REST API 中

现在，您已经对访问外部 API、操作它返回的数据以及使用 Ballerina 创建 API 有了大致的了解，让我们看看如何通过您自己的 REST API 来公开获奖者数据。

让我们定义 API 的一些功能需求。

仅提供来自原始诺贝尔奖获得者 API 的一组有限的数据字段。

用户应该能够查看所有获奖者:

```
curl http://localhost:9090/nobelprize/laureates
```

用户应能通过以下一个或多个查询参数过滤结果:`name`、`year`、`category`、`gender`、`country`或/和`isAlive`状态。以下是一些卷曲的例子:

```
curl http://localhost:9090/nobelprize/laureates?name=Marie%20Curiecurl http://localhost:9090/nobelprize/laureates?gender=femalecurl http://localhost:9090/nobelprize/laureates?gender=female&isAlive=truecurl http://localhost:9090/nobelprize/laureates?country=Pakistan
```

用户应该能够通过他/她的 id 查看获奖者:`<API_URL>/laureate/id`

```
curl http://localhost:9090/nobelprize/laureate/100
```

用户应该能够通过 HTTP POST 提供一个 JSON 字符串来添加一个获奖者。这里有一个例子:

```
curl -X POST -H "Content-Type: application/json" \
-d '{
"id": "",
"firstname": "Dakshitha",
"surname": "Ratnayake",
"bornCountry": "Sri Lanka",
"gender": "female",
"prizes": [
 {
  "year": "2042",
  "category": "Physics",
  "share": "1",
  "motivation": "\"for inventing time travel\"",
  "affiliations": [
   {
    "name": "University of Colombo",
    "city": "Colombo",
    "country": "Sri Lanka"
    }
   ]
  }
],
"isAlive": true
}
' \
http://localhost:9090/nobelprize/laureate/
```

## 构建数据和服务

*在一个文件 laureate_service.bal 中，我添加了一些之前讨论过的导入语句和记录定义。(您可以删除 main.bal 文件或注释掉您目前使用的代码，以避免导入错误和 main 函数到控制台的输出。)*

除了已经介绍的内容之外，还添加了全局变量`laureates`和`TEST_ID`以及函数`initializeLaureates`和`getLaureateById`，后面是服务定义。

## 笔记

在这篇文章中，我不会将获奖者保存在数据库中，而是使用`laureates`数组来保存所有获奖者，而不是每次我们需要获取获奖者的信息时都调用诺贝尔奖 API。阵列将通过`initializeLaureates`功能进行初始化。

`initializeLaureates`将从诺贝尔奖 API 中获取获奖者，通过删除不需要的字段并添加`isAlive`字段来格式化每个`Laureate`，并返回格式化后的获奖者。

`TEST_ID`将在用户希望通过 POST 方法添加新用户时使用，每次添加都会增加 1。

`getLaureateById`函数将字符串参数与`laureates`数组中的一个`Laureate`的 id 进行匹配，并返回一个`Laureate`或一个`error`。

## 返回所有获奖者，并提供基于查询参数过滤结果的选项

下面是诺贝尔奖 API 中的第一个资源函数:`get laureates`。这转化为一个 GET 方法来获取一组获奖者，或者是一个错误，带有包含输入参数以过滤结果的选项。

# 笔记

*   `name`、`year`、`category`、`gender`、`country`、`isLiving`均为可选输入参数。如果这些参数都不存在，将返回获奖者的完整列表。
*   过滤发生在查询表达式中。将对`laureates`中的每个`Laureate`进行检查，以查看在`where`子句中是否满足由查询参数给出的标准(如果存在的话)。
*   创建了几个字符串变量来访问可选变量/字段。如果查询参数`name`不存在，则将“”赋值给`searchTerm`。如果`laureate.surname?`丢失，变量`surname`也将被分配一个空字符串。我还将让用户搜索整个名称来匹配他们提供的名称，因此我也将构造`fullName`变量。表达式`var isAlive = laureate[“isAlive”]`允许我们通过括号符号访问`isAlive`字段，并将其分配给`isAlive`，其类型为`var`。一个单独的`let`子句可以用来在查询表达式的范围内定义多个用逗号分隔的变量。

`where`子句允许您根据条件进行过滤。您可以定义任何返回布尔值的条件表达式。

*   首先，它检查名称是否存在，如果存在，它使用正则表达式将全名与提供的名称进行匹配。
*   接下来，它检查是否提供了年份。由于年份在`prizes`数组中(在`Laureate`记录中)，我们需要遍历数组并遍历每个`Prize`来找到匹配的年份。我们本可以用`prizes[0].year`来进入这一年，但请记住，有些获奖者不止一次获奖。
*   数组的 lang 库定义了一个名为`[filter](https://lib.ballerina.io/ballerina/lang.array/0.0.0/functions#filter)`的函数，它接受一个函数来对数组执行特定的过滤操作。表达式`laureate.prizes.filter(prize => prize.year == year).length() > 0)`允许我们这样做。它是一个[匿名函数](https://ballerina.io/learn/language-basics/#functional-programming)表达式，带有一个可以推断参数类型的表达式函数体。上面的表达式相当于:`laureate.prizes.filter(function (Prize prize) returns boolean { return prize.year == year;})`
*   它基本上接受数组`laureate.prizes`并创建一个包含`Prize`成员的新数组，其中`prize.year`等于`year`。如果这个数组是空的，那就意味着那一年没有奖励。如果数组的长度是> 0，那么至少有一个匹配的奖励。也遵循相同的方法来检查类别。
*   检查性别、国家和 isAlive 状态非常简单，可以看出它是匹配的还是没有作为查询参数提供。

如果条件返回 true，给定迭代中的`Laureate`将被选择并添加到`results`数组中。

如果`results`数组为空，将返回一个`error`。否则，将返回整个数组或其子集。

## 按 ID 返回获奖者

nobelprize API 中的第二个资源函数是`get laureate/string id]`。如果路径参数`id`匹配，这将转化为获取单个`Laureate`的 GET 方法，否则将获取一个`error`。

## 添加获奖者

nobelprize API 中最后一个资源函数是`post laureate`。这转化为一个 POST 方法来添加一个`Laureate`，它的数据应该在请求体中提供。

## 笔记

`newLaureate`是将作为 JSON 对象包含在请求有效负载中的`Laureate`。如前所述，Ballerina 允许您将 JSON 直接解析成语言。

即将到来的获奖者将不包含 id。因此，`TEST_ID`将被指定为 id。对于下一个输入的`Laureate`，`TEST_ID`将增加 1。

`newLaureate`将通过`[push](https://lib.ballerina.io/ballerina/lang.array/0.0.0/functions#push)`函数添加到`laureates`数组中。

如果成功添加了获奖者，将返回`newLaureate`以及分配的 id，如果出现任何错误，将返回`error`。

## 测试 API

有了 nobel _ priories 包中的 laureate_service.bal 文件，您可以通过从 nobel _ priories 目录运行`bal run`命令来运行 API。您可以使用这些 [curl 命令](https://github.com/dakshitha/ballerina-samples/blob/main/nobel_prizes/curl-commands.txt)在外部测试 API。

要在内部测试 HTTP 服务，或者单独测试包，可以使用 [Ballerina 测试框架](https://ballerina.io/learn/test-ballerina-code/test-services-and-clients/#test-services)。测试 Ballerina 服务包括使用客户端向服务发送特定请求，并使用断言函数验证响应。目的是确保服务和客户端在发送和接收预期的请求和格式错误的请求时，行为符合预期。更多信息请参见[测试服务和客户端](https://ballerina.io/learn/test-ballerina-code/test-services-and-clients/#test-services)。

您可以在 [laureate_service.bal](https://github.com/dakshitha/ballerina-samples/blob/main/nobel_prizes/laureate_service.bal) 中查看服务的完整代码

# 结论

这让我们走到了尽头，但不是这样。这个 REST API 实现绝不是模仿一个生产就绪的设置——我没有涉及[持久性](https://ballerina.io/learn/build-a-data-service-in-ballerina/)、[安全性](https://medium.com/ballerina-techblog/microservices-security-with-ballerina-e9d430f05373)、[日志](https://dzone.com/articles/how-to-configure-customize-and-utilize-ballerina-logs)、 [API 文档](https://ballerina.io/learn/ballerina-openapi-support/)和[云部署](https://ballerina.io/learn/run-ballerina-programs-in-the-cloud/code-to-cloud-deployment/)，所有这些都可以用 Ballerina 优雅地实现。该实现的目的是向您展示该语言强大而简单的模型，以无缝地创建 API 并在网络上处理数据，重点是该语言在操作数据时提供的灵活性。芭蕾舞演员非常适合你的下一个 API 项目。现在你知道为什么了！

感谢阅读。我希望这有所帮助。如果你有任何问题，请随时回复。

# 资源

*   你可以在芭蕾舞演员[这里](https://ballerina.io/learn/distinctive-language-features/network-interaction/)阅读更多关于网络互动的内容。
*   您也可以查看这个[服务实现](https://gist.github.com/sameerajayasoma/786d22ed727cc34b174c4e7bcc596690)来理解不同地使用记录类型，特别是对于不同的输入和输出格式。
*   [阅读](https://blog.jclark.com/2022/05/why-ballerina-is-language.html)为什么芭蕾舞演员是一种语言，而不是一个框架或库。
*   访问 [ballerina.io](http://ballerina.io) 了解更多关于 ballerina 的信息。

```
**Want to connect?** Follow me on [Twitter](https://twitter.com/techieducky).
```