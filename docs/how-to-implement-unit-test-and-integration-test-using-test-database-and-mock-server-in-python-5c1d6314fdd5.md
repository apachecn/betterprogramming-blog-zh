# 用测试数据库和模拟服务器在 Python 中实现单元测试和集成测试

> 原文：<https://betterprogramming.pub/how-to-implement-unit-test-and-integration-test-using-test-database-and-mock-server-in-python-5c1d6314fdd5>

## 软件测试简要指南

![](img/6fa4c80c39eb94576ba9af20c917eb31.png)

晒衣夹([来源](https://www.flickr.com/photos/toprakucar/16394952041/))

“应用程序在上周之前一直正常工作？！？!"，“希望我们不会破坏现有服务中的任何东西？”，“在分期中，我们应该涵盖边缘案例，让我们写下一些场景！”，而且“我们只添加了一些日志来跟踪服务更好的人，我不知道发生了什么？?"。

我很确定你对它们非常熟悉，而且如果你不得不与开发人员一起工作(？？)他们通常不重视测试和良好的编码原则。如果我冒犯了你，很抱歉——但是，我从事软件工作已经快 7 年了，毫无疑问，以这种方式进步的开发人员会留下非常糟糕的应用程序。此外，这个问题甚至会导致人们离职，因为它会把申请带到无法处理的地步。今天，我想提一下应用程序中的测试，它是开发的保证。

在接下来的文章中，将简要介绍软件测试的定义及其细节。然后，接下来是一个示例，指导如何实现单元测试和集成测试，包括一个测试数据库，其中包含 docker 来测试存储库操作，以及一个假客户端来测试应用程序中的外部 API。

# 什么是软件测试？

正如维基百科所说[1]；

> 软件测试是通过确认和验证来检查被测软件的工件和行为的行为。

在这篇博文中，我不会提到由验证测试和用户测试组成的应用程序的业务或客户端。通过简要描述理论和分享实践，将只提到内部服务的验证。

根据我的理解，测试的定义是“应用程序在我的本地机器上工作，你可以克隆存储库，然后你可以在你的本地机器上运行它”这句话的保证，使用相同的环境特性。为了在实现新功能或修复/修改现有功能之前验证应用程序，测试应该自动化并在合并之前通过，测试套件应该包括单元、集成和功能测试，断言应该大量编写。

除了验证软件测试的好处之外，它还鼓励开发者以最简单的方式设计一个合适的应用程序结构。为设计糟糕的应用程序实现单元或集成测试对开发人员来说是个挑战，甚至开发人员可能会放弃为这些糟糕的应用程序添加测试。

# 什么是单元测试？

Kent Beck 在他的书*测试驱动开发* [2]中将单元测试定义为:

> 单元测试测试独立于程序其余部分的单个单元(模块、函数、类)。

因为这些单元测试只验证单个的单元，所以只有需要的单元/类应该在测试类中初始化。为了忽略外部服务或外部技术依赖，如数据源，我们可以使用模拟来模仿它们的行为。单元测试时间应该很短，因为它们不依赖于任何外部资源或服务。

# 什么是集成测试？

Martin Fowler [4]提到了集成测试的定义:

> 顾名思义，集成测试的目的是测试许多单独开发的模块是否如预期的那样一起工作。它是通过激活许多模块并对所有模块运行更高级别的测试来执行的，以确保它们一起运行。这些模块可以是单个可执行程序的一部分，也可以是独立的。

与单元测试相比，集成测试的主要区别在于所有内部服务都是初始化的。对于外部数据源，例如数据库，可以实现与生产数据库具有相同表名和列名的测试数据库来测试外部层。集成测试的最大好处之一是应用程序的最外层，控制器，可以用给定的请求参数进行测试。

在开始这个例子之前，我想给出一些关于代码的简单细节。这是一个获取和插入音乐家的 rest API。API 的功能如下:

*   叫一个音乐家的名字
*   获取所有音乐家
*   音乐家的加入

在该应用中，使用两个外部源:音乐家数据库和音乐家客户服务来获取信息。实现了一个 dockerized `test-database`来访问音乐家数据库，并为音乐家客户端服务实现了一个使用 Flask 的假端点来进行集成测试。还有一个`ConcertService`设计得很糟糕，用来展示如何捕捉内部服务并模仿它。

你可以从[库](https://github.com/ktoprakucar/unit-and-integration-test-implementation)中获得整个应用程序。

# 密码

## 应用

在进入测试之前，将会正确地解释服务，以理解领域以及执行和验证什么测试。

![](img/0b1573b7cb6321e36e57e16715fbcf66.png)

[信号源](https://unsplash.com/photos/0vB20AT_39o)

所有服务和外部数据源都在`MusicianApplication`类中实现，以管理依赖注入。FastAPI 用于生成应用程序。

我没有使用任何配置服务来简化应用程序。您还可以使用该类运行应用程序。

正如我上面提到的，应用程序有 3 个端点/行为。在这个类中，唯一的依赖是`MusicianService`。`fetch_musician_by_name()` 和`fetch_all_musicians()` 方法返回一个*音乐家*对象或音乐家列表。

音乐家对象包含:

*   `name`
*   `surname`
*   `age`
*   `instrument`

`MusicianService` class 是调用和管理所有外部资源的类。

在`get_musician_by_name` 法中；首先，对名字进行验证，然后存储库返回音乐家(如果存在的话)。

`external_musician_client`检索所有音乐家的名字，然后使用这些名字从`get_all_musicians`方法的储存库中获取音乐家。

在最后一个方法*保存*中，音乐家通过给定的`name`、`surname`、`age`和`instrument`信息使用存储库被插入。

`MusicianRepository`使用`postgres_client`管理数据库客户端上的读取和插入操作。它使用映射器和访问数据库来处理数据对象和值对象之间的转换。

`PostgresClient`是唯一依赖于使用 pandas dataframe 的数据库的类。`PostgresClientConfig`用于配置数据库设置。

`ExternalMusicianClient`用于获取储存库中使用的音乐家姓名。`ExternalMusicianClientConfig`包含 HTTP 请求的`url`和`port`信息。

为了解释*打补丁*(将在测试部分解释)，执行`ConcertService`。它没有任何逻辑，直接调用`OrganizationService`和`TicketService`创建一个由`band`和`ticket_price`组成的字典。`OrganizationService`使用`choose_band`方法返回一个静态值，`TicketService`根据乐队名称的长度定义票价。

## 试验

在解释完服务之后，我们可以转移到应用程序的单元测试和集成测试部分。我没有为存储库和客户端实现单元测试，因为集成测试已经实现了，这有点过度工程化。

![](img/c5ba4bdafd39f4f91c93f92c5ed58a55.png)

[来源](https://unsplash.com/photos/i22gbC3gFm4)

在测试中，`unittest` [5]包用于断言。每个测试有 3 个部分。*给定的*是将用于测试的信息或服务部分。*当*是要测试的阶段。*那么*就是使用断言特性的验证部分。

对于单元测试；我们将从嘲讽和补丁功能中获益。`Assertion`用于比较预期结果和实际结果。`Mock`被用来初始化服务以控制它们的行为。`Patch` 用于捕捉内部服务的方法来改变它们的输出。

我想从`MapperTest`开始，因为它是最简单的一个，无需任何模仿或修补就能验证输出。在第一次测试中，`test_should_convert_dataframe_to_musician`做了简要说明。

*   一代音乐家`dataframe`
*   `dataframe`是对音乐家的皈依
*   音乐家对象属性的验证

在最后一个测试`test_should_convert_musician_to_dataframe`中，它执行相反的操作。

`MusicianRepositoryTest`比`MapperTest`复杂一点，因为它使用了模拟。如上所述，因为只测试了单个单元，所以客户端被`Mock()`方法模仿。

在我们模仿客户端类之后，我们可以定义被模仿类的任何方法的返回值。为`retrieve_musician`方法定义了一个预定义的音乐家对象。`MusicianRepository`使用模拟对象生成对象，以执行和验证行为。最后，我们能够验证输出的属性。在第二个测试中，相同的逻辑被应用于存储库的另一个方法。

`MusicianValidationServiceTest`不同于其他测试，因为它捕捉并验证异常，而不管输出的断言。

在`MusicianValidationService`中，如果名称不仅仅包含字母值，它会引发一个“名称无效”的异常消息。

第一个测试`test_should_raise_an_exception_when_name_is_invalid`，使用`with self.assertRaises(Exception) as context`调用验证，然后捕获并验证异常。

在第二个测试中，它没有引发任何异常，因为名称是有效的。

在进入集成测试之前，我想提一下`ConcertServiceTest`来解释补丁注释。

如上所述，`ConcertService` 没有任何逻辑，内部服务也没有作为构造函数的参数使用(糟糕的设计)。在第一次测试中，`organize_concert()`方法工作正常并返回默认结果。

在`test_should_organize_concert_with_mocked_values`中，补丁注释用于捕获内部服务的方法，并定义这些方法的输出值。每个模仿的方法都应该添加到方法参数中，以定义输出值(注释和模仿对象的顺序是相反的，如您所见:`define_ticket_price:choose_band` `-> mock_choose_band:mock_define_ticket_price`)。

在返回值中，`choose_band()` 方法的输出被定义为“Abba”。

对于`define_ticket_price()`，默认情况下它应该返回 20，但是因为我们将返回类型改为 100，所以它返回 100。之后，比较预期结果和实际结果。

对于集成测试；一个文档化的数据库和一个使用 Flask 的假客户端被用来管理外部资源。

`AbstractIntegrationTestClass`被实现来组织这两个资源，这个类在所有集成测试中都被实现。

这个抽象类有两个主要方法，`setup()`和`tear_down()`来初始化`test-database`和`test-client`。`testcontainers`包装是给码头工人用的。

在`docker-compose.yml`中，定义了`test-database`的属性，模式、表格和记录的初始脚本被写入`init.sql`文件。`MockServer`【8】用于添加伪端点。对于给定的`url`和`port`值，可以通过初始化 Flask 服务返回期望的响应。

提供了一个`test-client`和`test-database`来调用端点，并从数据库中获取信息用于集成测试。

在`ExternalMusicianClientIntegrationTest`中，调用一个初始化的模拟服务器来验证响应。在配置文件中设置`Url`和`port`值，并创建`ExternalMusicianClient`。

调用`get_all_musician_names()`并验证响应。

`PostgresClientIntegrationTest` 使用`test-database`验证数据库客户端上的读写操作。

第一个测试通过名字读取音乐家，第二个测试通过`test-database`中的名字列表读取音乐家。最后一个测试将音乐家记录插入到`test-database`中，并验证插入。

为了创建一个数据库连接，测试使用`AbstractIntegrationTestClass` 的`create_connection()`方法，然后获取最近插入的记录。

`MusicianServiceIntegrationTest` 是测试端点之前的最新集成测试。

正如我在客户端和数据库存储库的集成测试中提到的，两个服务都用配置文件初始化，然后使用这些服务初始化`MusicianService` 。

因为它具有与上面演示的相同的执行和验证，所以我不会重新解释它们。

测试端点的最终集成测试`*MusicianRestApiIntegrationTest*`，调用每个端点并验证响应。

既然是 HTTP 调用，我们需要使用`*url*`和`*parameters*`。在第一个测试中，它使用参数`kurt`发出一个`get`请求，并使用`json()`提取响应，然后验证结果。

在第二个测试中，由于参数无效，断言了一个异常。由于名为`paul`的音乐家不存在，第三个测试验证了空结果。`fetch-all-musicians`和`save-musician`端点分别在第 4 次和第 5 次测试中测试。

# 运行测试

为了运行测试，您可以同时使用终端和 IDE。我将共享终端的命令。`pytest`包可以用来运行测试。

要运行所有测试:

```
pytest test
```

就个人而言，我不喜欢将单元测试和集成测试分离到不同的文件夹中。因此，我们可以使用`regex`单独运行这些测试套件。这在 CI/CD 操作中也是有用的

若要仅运行单元测试:

```
pytest test/ -k 'test and not integration'
```

若要仅运行集成测试:

```
pytest test/ -k 'integration'
```

# 新闻报道

为了测量应用程序测试的代码覆盖率，我们可以使用`coverage` [9]包。

首先，`coverage_file`被初始化:

```
export COVERAGE_FILE=.coverage
```

然后，将执行测试来测量代码覆盖率:

```
python -m coverage run -m pytest test
```

最后，指出所有服务的代码覆盖率和覆盖率细节:

```
python -m coverage report -m
```

# 最后的话

简而言之，我想谈谈单元测试和集成测试是如何在应用程序中开发的。通过参考我的帖子，您可以使用`test-database`和`mock-servers`为存储库和客户端轻松地从头开始实现测试。

关于进行单元测试的一些建议:

*   在部署新功能之前，至少要实现单元测试和集成测试
*   当应用程序中的逻辑没有改变时，避免改变断言。如果您打算更改验证，请重新考虑您在应用程序中所做的事情
*   避免注释掉测试。这些被忽视的测试可能会在未来导致非常大的灾难性事件
*   尝试在最外层的服务层实现所有边缘情况/可能的用户场景
*   通过为简单的 get 和 set 方法编写单元测试来避免过度工程化

# 参考

1.  [https://en.wikipedia.org/wiki/Software_testing](https://en.wikipedia.org/wiki/Software_testing)
2.  [https://www . Amazon . com/DP/0321146530/ref = cm _ SW _ r _ tw _ DP _ 6 e 8 dcvqjp 9 wv5 fwhzr 13](https://www.amazon.com/dp/0321146530/ref=cm_sw_r_tw_dp_6E8DCVQJP9WV5FWHZR13)
3.  [https://martinfowler.com/bliki/UnitTest.html](https://martinfowler.com/bliki/UnitTest.html)
4.  [https://martinfowler.com/bliki/IntegrationTest.html](https://martinfowler.com/bliki/IntegrationTest.html)
5.  [https://docs.python.org/3/library/unittest.html](https://docs.python.org/3/library/unittest.html)
6.  [https://docs . Docker . com/get-started/overview/#:~:text = Docker % 20 is % 20 an % 20 open % 20 platform，ways % 20 you % 20 manage % 20 your % 20 applications](https://docs.docker.com/get-started/overview/#:~:text=Docker%20is%20an%20open%20platform,ways%20you%20manage%20your%20applications)。
7.  [https://medium . com/swlh/test containers-in-python-testing-docker-dependent-python-apps-BD 34935 f55 b 5](https://medium.com/swlh/testcontainers-in-python-testing-docker-dependent-python-apps-bd34935f55b5)
8.  [https://gist . github . com/eru vanos/f6f 62 EDB 368 a 20 AAA 880 e 12976620 db 8](https://gist.github.com/eruvanos/f6f62edb368a20aaa880e12976620db8)
9.  【https://coverage.readthedocs.io/en/6.3.2/ 

```
**Want to Connect?**LinkedIn: [https://www.linkedin.com/in/ktoprakucar/](https://www.linkedin.com/in/ktoprakucar/)GitHub: [https://github.com/ktoprakucar](https://github.com/ktoprakucar)
```