# 使用 Golang 中的 Mongo-Go-Driver 进行单元测试代码

> 原文：<https://betterprogramming.pub/unit-testing-code-using-the-mongo-go-driver-in-golang-7166d1aa72c0>

## 这很难——但值得吗？

![](img/8b05fa6e420d30d5762ccf6b0b3a1cc9.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2450236) 的 [Gerd Altmann](https://pixabay.com/users/geralt-9301/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2450236)

一个周末，当我有一些空闲时间时，我决定重写我的宠物学习/实验项目的部分内容。你知道，你的项目从一些随机的想法开始，以便学习一些新的东西，然后不断产生新的想法，增加新的学习途径，从不相信应用程序已经准备好投入生产。

在折腾和(重新)移动代码块时，我决定用官方的 [mongo-go-driver](https://github.com/mongodb/mongo-go-driver) 替换废弃的 [mgo.v2](https://gopkg.in/mgo.v2) 驱动程序，并增加一个单元测试层。我选择使用[evidence](https://github.com/stretchr/testify)库而不是 [Golang mock](https://github.com/golang/mock) 进行单元测试和嘲讽，因为 evidence 有更多可以理解的错误消息，比 Golang mock 稍微成熟一些。

第一个错误是很久以前犯的:没有使用依赖注入。这导致 mgo.v2 驱动程序无处不在(处理程序、中间件、模型等)。).看看相关问题的数量，我的小意大利面怪物项目看起来很悲惨。为了能够对代码进行单元测试，我不得不使用依赖注入重写一半的程序。这是一项耗时但简单的任务。哦，好吧——你迟早要偿还技术债务。

真正的问题出现在我开始写单元测试的时候。似乎官方的 mongo-go-driver 没有使用接口，他们也不打算在短期内实现它们(截至 2019 年 10 月 14 日)。因此，有必要创建一个定制的数据库抽象层来实现必要的方法:

正如您在示例中看到的，有四个接口涵盖基本功能，如`insert`、`get,`和`delete`。这应该让您对当更高级的驱动程序特性被添加到这一层时，代码变得有多臃肿有了基本的概念。

下一件事是将存储库从数据库实现中分离出来，以便更容易地在不同的域之间进行分离。让我们假设我们的示例域是`users`，我们希望拥有能够`insert`、`get`和`delete`用户的功能。这个用户库叫做`UserDatabase`:

现在，我们如何对这个用户存储库进行单元测试？在编写单元测试时，我们首先需要的是一个模拟接口。您可以使用官方文档中的[示例自己编写，也可以使用](https://github.com/stretchr/testify#mock-package)[mocking](https://github.com/vektra/mockery)包生成一个。下面是生成代码的示例:

一旦我们模拟了结构，我们就可以继续编写实际的测试。一个常见的用法示例是:

如果我们运行我们的测试，我们会看到他们通过了。但是如果我们检查代码覆盖率，我们会很快注意到它没有检测到我们的功能正在被测试。

经过一些调查和研究后，似乎`go test`认为这就像你在测试一个被模仿的功能，而不是真正的功能。为了解决这个问题，我们需要定义一个模拟的数据库接口，并使用该模拟创建一个用户存储库接口。以下是`FindOne`功能的单元测试示例:

现在，如果我们运行`go tool cover -func=cover.out`命令，我们将看到我们的功能被 100%覆盖。

# 结论和最终想法

您必须切换到不同数据库的可能性非常小。如果这样的事情发生了，仍然存在这样一个问题:您是否足够幸运，以这样一种方式实现了一个数据库抽象层，足以改变一个文件来满足新的需求。我认为重写几个文件而不是一个文件不会是你最大的问题，因为你会忙于考虑如何迁移所有的数据。因此，除非您在一个项目中使用几个不同的数据库实现，否则这种强制数据库抽象层对于代码的单元测试没有任何额外的好处。正如罗伯托·沃尔曼曾经说过的:

> 面向对象版本的“意大利面条代码”当然是“千层面代码”(层次太多)。

最后一件事:测试这个功能的推荐方法是通过集成测试，而不是单元测试。当然，集成测试在代码覆盖率中并不总是可见的，特别是如果你为它们编写一个单独的项目，但是这种牺牲最终会给你带来更好的结果。你应该总是编写集成测试来覆盖真实的案例，因为单元测试根本做不到。这里有一个真实的例子:

![](img/21834a5f0f18f50bfbbbe60e10ed351f.png)

# 来源

[](https://github.com/stretchr/testify) [## 伸展/作证

### Go code (golang)一组包，提供了许多工具来证明您的代码将按照您的意图运行…

github.com](https://github.com/stretchr/testify)  [## vektra/嘲弄

### mockss 提供了为 golang 接口轻松生成 mock 的能力。它消除了所需的样板代码…

github.com](https://github.com/vektra/mockery) [](https://github.com/golang/mock) [## golang/mock

### GoMock 是一个模仿 Go 编程语言的框架。它与 Go 的内置测试包集成得很好…

github.com](https://github.com/golang/mock) [](https://github.com/mongodb/mongo-go-driver) [## MongoDB/mongo-driver

### MongoDB 支持的 Go 驱动程序。Go 1.10 或更高。我们的目标是支持最新版本的 go。MongoDB…

github.com](https://github.com/mongodb/mongo-go-driver)  [## 指挥掩护

### Cover 是一个用于分析由“go test -coverprofile=cover.out”生成的覆盖率概要文件的程序。封面也是…

godoc.org](https://godoc.org/golang.org/x/tools/cmd/cover)  [## 单元测试的艺术

### 一本几年前就应该写好的重要的书。迈克尔羽毛，对象导师单元测试，做得好…

www.manning.com](https://www.manning.com/books/the-art-of-unit-testing) [](https://github.com/b-pagis/medium-mongo-go-driver) [## b-pagis/中型 mongo-go-driver

### 看看要达到 100%的覆盖率需要多少麻烦，包括一些属于外部库的部分。这个…

github.com](https://github.com/b-pagis/medium-mongo-go-driver)