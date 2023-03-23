# 在 Go 中有效编写单元测试的 5.5 技巧

> 原文：<https://betterprogramming.pub/5-and-a-half-techniques-for-effectively-writing-unit-tests-in-go-1b87b94abd21>

## 这些技术帮助我在两年的时间里在生产中没有任何错误

![](img/9aff3a97a13db9c9424cd8bf86b7f2f2.png)

[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

单元测试一直是我的事情——有点像爱好。曾经有一段时间我对它念念不忘。

我所有的项目必须有至少 90%的单元测试覆盖率。您可能会猜测对代码库进行重大更改需要多少时间。但是，另一方面，得到一个带有一些与业务逻辑相关的 bug 的报告是很少见的。

大多数情况下，我只有与其他(微)服务或数据库的集成问题相关的错误。此外，添加新的业务不变量也很容易。之前已经有涵盖所有病例的测试了。

重要的是确保这些测试最终是绿色的。我甚至经常不检查完整的运行(微)服务——有绿色的新旧单元测试就足够了。

有一次，在做一个私人项目时，我不得不编写单元测试来覆盖一些模块。可能里面有 100 多个不同的 Go 结构，谁知道有多少函数。我花了整整一个周末。

一个星期天的深夜，在我睡觉前，我设了一个闹钟叫醒我，因为我第二天要出差。我几乎没有睡觉。当你做梦的时候，这是一种奇怪的睡眠方式，但是你不知何故意识到你自己和你的环境。

你的大脑整晚都很活跃。整个晚上，我都在梦想为我的闹钟编写单元测试。猜猜在每个单元测试执行中发生了什么？闹钟一直在响。就这样，整整一夜。

哦，对了，我差点忘了说。两年来，我们在生产中没有任何错误。应用程序仍然获取所有数据。它每周一发送所有的电子邮件。我甚至不知道我的 Gitlab 密码。

# 我们将涵盖的内容

为了节省阅读整篇文章的时间，您可以直接查看以下主题:

1.  单元测试和模拟(一般情况下)
2.  生成模拟
3.  界面的局部模拟
4.  功能模拟
5.  套件和断言
6.  好处 1:模仿 HTTP 服务器
7.  额外收获 2:模仿 SQL 数据库

# 单元测试和模拟(一般情况下)

正如我们在[的文章](https://martinfowler.com/bliki/UnitTest.html)和[马丁·福勒](https://twitter.com/martinfowler)中看到的，我们可以区分两种类型的单元测试:

1.  *社交单元测试*是我们测试一个依赖于其他对象的单元的测试。如果要测试`UserController`，就用与数据库通信的`UserRepository`来测试。
2.  *独立单元测试*是我们测试一个完全独立的单元的测试。在这里，您将测试`UserController`，它与受控的、被模仿的`UserRepository`交互，为此您可以提供它在没有数据库的情况下究竟如何运行。

在一个项目中使用这两种方法都是合理的，我总是使用这两种方法。

如果我编写社交单元测试，过程很简单——使用我模块中已经使用的任何东西，一起测试逻辑。但是，当谈到围棋中的嘲讽时，这不是一个标准的过程。

[Go 不支持继承，支持合成](https://golangbot.com/inheritance/)。这意味着一个结构不扩展第二个结构，而是包含它。因此，Go 不支持结构层次上的多态性，而是使用[接口](https://golangbot.com/polymorphism/)。

所以，一旦你的结构直接依赖于一个结构的实例，或者某个函数期望一个特定的结构作为参数，那么祝你好运模仿那个结构。

在下面的代码示例中，我们有一个带有`UserDBRepository`和`AdminController`的简单案例。`AdminController`直接依赖于`UserDBRepository`的实例，它代表负责与数据库“对话”的存储库的实现。

AdminController 依赖于 UserDbRepository 结构的实例的情况

如果我们想为`AdminController`编写单元测试，看看它是否会创建正确的 JSON 响应，我们有两种可能性:

1.  提供一个`UserDBRepository` 的新实例，以及一个到`AdminController` 的数据库连接，希望它是你需要传递的唯一依赖项。
2.  不要提供任何东西，一旦开始运行测试，就只期待一个零指针异常。

为了避免这种情况，并且能够正确地测试我们的单元，我们需要确保我们的代码遵守以下原则:

1.  [编程到界面](https://medium.com/javarevisited/oop-good-practices-coding-to-the-interface-baea84fd60d3)
2.  [依存倒置原则](https://stackify.com/dependency-inversion-principle/)

一旦我们应用了这两个原则，我们的重构代码就会得到如下所示的形状，其中实际的`AdminController`依赖于接口`UserRepository`，而不指定它是数据库的存储库还是其他。

AdminController 依赖于 UserRepository 接口实例的情况

所以，现在当我们有了一个起点，让我们看看我们如何能最有效地嘲笑。

# 生成模拟

有许多用于生成模拟的库，如果你愿意，你也可以创建自己的生成器。我喜欢来自 [Vektra](https://github.com/vektra) 的[嘲弄](https://github.com/vektra/mockery)包装。它提供了由来自 [Stretchr，Inc](https://github.com/stretchr) 的[evidence](https://github.com/stretchr/testify)包支持的模拟，这已经是一个足够好的继续使用它的理由了。

带有 AdminController 的简单用户历史界面

让我们用`UserRepository`**`AdminController`回到前面的例子。每当有人向`/users`端点发送请求时，`AdminController`期望`UserRepository`接口通过它们的`Lastname`来过滤`Users`。**

**严格来说，`AdminController`并不关心`UserRepository`如何找到结果。根据它是否获得了`Users`或`Error`的切片，只需将适当的响应从 Gin 包附加到`Context`即可。**

**主 Go 文件**

**在本例中，我使用了来自 [Gin-Gonic](https://github.com/gin-gonic) 的 [Gin](https://github.com/gin-gonic/gin) 包进行布线，但是我们想使用哪个包来布线并不重要。我们将首先初始化`UserRepository`的实际实现，将其传递给`AdminController`，并在运行我们的服务器之前定义端点。**

**此时，我们的文件夹结构可能如下所示:**

```
**user-service
| cmd
  | main.go
| pkg
  | user
    | user.go
    | admin_controller.go
    | admin_controller_test.go**
```

**现在，在文件夹`user`中，我们可以执行 mock 命令来生成 mock 对象。**

```
**$ mockery --all --case=underscore**
```

**它检查包中的所有接口(您可以进一步修改这个选项)，并创建一个新的文件夹`mocks`，在其中放置所有生成的文件。**

```
**user-service
| cmd
  | main.go
| pkg
  | user 
    | mocks 
      | user_repository.go
    | user.go
    | admin_controller.go
    | admin_controller_test.go**
```

**生成文件的内容如下例所示:**

**带有模拟结构的生成文件示例**

**当我在一个项目中工作时，我喜欢把所有的命令都写在项目中的某个地方。有时，它可以是一只`Makefile`或一只`bash script`。但是在这里，我们可以在`user`文件夹中添加额外的`generate.go`文件，并将以下代码放入其中:**

**generate.go 文件的内容。**

```
**user-service
| cmd
  | main.go
| pkg
  | user 
    | mocks 
      | user_repository.go
    | user.go
    | admin_controller.go
    | admin_controller_test.go
    | generate.go**
```

**该文件包含一个特殊的[注释](https://blog.golang.org/generate)，从`//go:generate.`开始，它包含一个用于执行其后代码的标志，一旦您在项目根文件夹中运行下面的命令，它将生成所有文件:**

```
**$ go generate ./...**
```

**最后，这两种方法给出了相同的结果——生成一个带有模仿对象的文件。因此，编写单独的单元测试不再是一个问题:**

**生成模拟的单元测试示例**

# **界面的局部模拟**

**有时，不需要模拟接口中的所有方法。或者，这个包不属于我们，所以我们不能生成文件。在我们的库中创建和保存文件也是没有意义的。**

**但是，有时候，在我们需要的时候，接口可以有很多方法。对于这种情况，我们仍然可以使用一个带有`UserRepository`的例子。`AdminController`只使用存储库中的一个函数，称为`FilterByLastname`。**

**这意味着我们不需要任何其他方法来测试`AdminController`。为此，让我们提供一些名为`MockedUserRepository`的结构，如下例所示:**

**手动创建的模拟对象。**

**`MockedUserRepository`实现接口`UserRepository`。在我们将`UserRepository`接口嵌入`MockedUserRepository`时，我们确保了这一点。**

**我们的模拟对象期望在它的*中包含一些`UserRepository`接口的实例。*如果没有定义该实例，默认情况下，它将是`nill`。除此之外，它还有一个字段，这是一种函数。**

**该功能与`FilterByLastname`具有相同的签名。`FilterByLastname`方法附加在被模仿的结构上，它只是代理一个对私有字段的调用。现在，如果我们以下面的方式重写我们的测试，它可能看起来更直观:**

**在单元测试中使用手动创建的模拟。**

**当我们通过使用 [AWS SDK](https://github.com/aws/aws-sdk-go) 来测试我们的代码与 [AWS](https://aws.amazon.com/console/) 服务(如 SQS)的集成时，这种技术可能是有益的。在这种情况下，我们的`SQSReceiver`依赖于`SQSAPI`接口，它有…嗯，很多功能:**

**一个简单的 SQSReceiver 示例。**

**这里我们可以使用相同的技术并提供我们自己的模拟结构:**

**使用手动创建的模拟来模拟与 SQS 队列的通信。**

**一般来说，我不测试负责与数据库或外部服务建立连接的基础设施对象。为此，我在[测试金字塔](https://martinfowler.com/articles/practical-test-pyramid.html)的更高层次上编写测试。不过，如果真的需要测试这样的代码，这种方法对我很有帮助。**

# **功能模拟**

**在核心 Go 代码或任何其他包中，有许多有用的函数。我们可以在代码中直接使用这些函数，就像下面的`ConfigurationRepository`一样。**

**这个结构负责读取`config.yml`文件并返回应用程序中到处使用的配置。`ConfigurationRepository`从 core Go 包 [IOutil](https://pkg.go.dev/io/ioutil) 中调用方法 [ReadFile](https://pkg.go.dev/io/ioutil#ReadFile) :**

**配置存储库的示例**

**在像这样的代码中，如果我们想要测试`GetConfiguration`，每个测试执行都不可避免地依赖于`config.yml`文件的存在。**

**我们再次依赖于技术细节，比如从文件中读取。当类似这样的事情发生时，我想为这段代码提供一个单元测试，过去我使用了两种变体。**

## **变体 1:简单类型别名**

**第一个变化是为我们想要模仿的方法类型提供一个[类型别名](https://yourbasic.org/golang/type-alias/)。新类型表示我们希望在代码中使用的函数签名。`ConfigurationRepository`应该依赖于这个新类型`FileReaderFunc`而不是我们想要模仿的方法:**

**简单类型别名。**

**在这种情况下，在初始化我们的应用程序时，我们会在创建`ConfigurationRepository`时将 Go 核心包中的实际方法作为参数传递:**

**配置存储库的初始化。**

**最后，我们可以编写一个单元测试，如下面的代码示例所示。在这里，我们定义了一个新的 reader 函数，它返回我们在每种情况下控制的结果。**

**使用简单类型别名的单元测试示例。**

## **变体 2:接口的复杂类型别名**

**第二个变体使用了相同的思想，但是在`ConfigurationRepository`中使用了一个作为依赖项的接口。它不依赖于函数类型，而是依赖于一个接口`FileReader`，该接口的方法与我们想要模仿的方法`ReadFile`具有相同的签名。**

**重构的 ConfigurationRepository，所以它依赖于接口中的函数。**

**此时，我们应该再次添加同一个类型别名`FileReaderFunc`，但是这一次我们应该为该类型附加一个函数。是的，我们需要在方法中添加一个方法——我无法表达我有多喜欢 Go 中的这一部分。**

**新的类型别名和方法。**

**从这一点来看，`FileReaderFunc`类型实现了`FileReader`接口。它拥有的唯一方法是代理对该类型实例的调用，即原始方法。当我们想要初始化应用程序时，它只带来最小的变化:**

**主应用程序文件。**

**并且，它不会对单元测试带来任何改变:**

**单元测试的例子。**

**我更喜欢第二种变化，因为我更倾向于接口和结构而不是独立的函数。但是，这两种解决方案都是好的。**

# **套件和断言**

**我需要再次提到[作证](https://github.com/stretchr/testify)包的伟大之处。除了嘲讽，这个库还提供了对[套件](https://www.testmonitor.com/blog/test-case-test-suite-test-run-whats-the-difference)和[断言](https://docs.microsoft.com/en-us/visualstudio/test/unit-test-basics?view=vs-2019#write-your-tests)的支持。**

**每当测试的目的是检查一个没有任何依赖关系(或者至少没有被模仿的依赖关系)的简单函数或 u struct 时，我就对简单的单元测试使用断言。我发现这对未来的代码读者了解测试用例的思想很有帮助，也更明确。**

**我唯一一次使用 pure Go 原生代码进行测试是在我的库不依赖于其他包的时候，所以要保持它“干净”否则，如果没有这类软件包的帮助，要覆盖所有的检查就太累了。**

**断言的例子**

**我在需要测试复杂结构时使用的套件，至少有一个模拟的依赖项。这允许我定义一个代码，它应该在整个套件启动之前、每次测试之前、每次测试之后等等被执行。**

**在每个套件运行之前，在大多数情况下，我初始化变量，这些变量在整个过程中是静态的。**

**例如，如果`Context`或`Request`没有保存任何特定于测试用例的数据，我在套件启动之前定义它们。如果测试可以改变它们的状态，我会在每次测试前用所有模仿的对象和主结构初始化它们。**

**最后，在每次测试之后，有时我应该编写代码来关闭一些通道，销毁一些变量，或者断言发送到 struct 函数的调用次数。**

**套件示例**

# **好处 1:模仿 HTTP 服务器**

**说到模仿 HTTP 服务器，我不认为这属于单元测试。尽管如此，有时有人可能有一个依赖于一些 HTTP 请求的代码结构，本节给出了在这些情况下的一些想法。**

**让我们假设我们有一些`UserAPIRepository`，它通过与外部 API 而不是数据库通信来发送和获取数据。这个结构可能看起来像这样:**

**UserAPIRepository 的示例**

**自然，我们也可以用模仿函数来处理这个问题，但是让我们继续玩这个游戏。为了对`UserAPIRepository`进行单元测试，我们可以使用 core Go [HTTPtest](https://pkg.go.dev/net/http/httptest) 包中的[服务器](https://pkg.go.dev/net/http/httptest#Server)的一个实例。**

**这个包为我们提供了一个简单的小型服务器，在本地的一些端口上工作，我们可以快速适应我们的测试用例并向它发送请求:**

**使用模拟的 HTTP 服务器进行单元测试**

# **额外收获 2:模仿 SQL 数据库**

**同样，与 HTTP 请求一样，我并不特别渴望编写测试 SQL 查询的单元测试。我总是问自己，如果我测试一个库或者一个模仿工具。**

**尽管如此，当我想检查一些 SQL 查询时，它可能被包装在一些结构中，就像这里的`UserDBRepository`:**

**UserDBRepository 示例。**

**当我决定为这类存储库编写单元测试时，我喜欢使用来自[数据狗](https://github.com/DATA-DOG)的包 [Sqlmock](https://github.com/DATA-DOG/go-sqlmock) 。它足够简单并且有很好的文档:**

**使用 SQLmock 进行单元测试**

**当模拟实际的 SQL 查询太累人时，另一种方法可以是用一个小的 SQLite 文件包含测试数据。它应该与我们的常规 SQL 数据库具有相同的表结构。**

**当然，这也不是一个理想的解决方案，因为我们在不同的数据库引擎上测试我们的查询，我们可能应该依靠 [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) 来避免双重集成。**

**在本例中，我创建了一个临时文件，并在每次测试执行之前将数据从 SQLite 文件复制到其中。它比较慢，但是像这样，我不能破坏我的测试数据。**

**连接到临时 SQLite 文件**

**最后，单元测试现在看起来简单多了:**

**UserDBRepository 的单元测试**

# **结论**

**用 Go 编写单元测试仍然比用其他语言更具挑战性，至少对我来说是这样。它需要准备代码，以便能够支持测试策略。**

**这在某种程度上是一个令人愉快的部分——它比任何其他语言都更有助于我在编写代码时形成我的架构方法。它永远不会枯燥，而且它给人一种持续的快乐感，即使是在一千次单元测试之后。**

**你对 Go 中的单元测试和嘲讽有什么体验？**