# 使用 PostgreSQL、Gin 和 Gorm 编写成熟的 REST API

> 原文：<https://betterprogramming.pub/writing-a-fully-fledged-api-using-postgresql-gin-and-gorm-4d5ba73114da>

## 一步一步地构建一个速度惊人的 API

![](img/2dd9f84a3102e7fc0445c1201dc91ae4.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Chinmay Bhattar](https://unsplash.com/it/@geekgunda?utm_source=medium&utm_medium=referral) 拍摄的照片

无法掩盖的事实是，多年来我一直是 C#的坚定拥护者。十多年来，我一直在使用编程语言，近年来，语言本身及其外围框架都取得了长足的进步。

C#不太吸引人的一个方面是它极其冗长。即使是编写一个相对较小的项目，项目、文件和代码行的数量也会迅速增加。

为了使事情易于管理，特别是在大型项目中，多年来出现了许多模式和最佳实践。对于大多数现代的 C # web 项目，依赖注入是标准，数据库通过 ORM 访问，路由由一个复杂的框架处理。

这一切都与复杂性有关。为了管理这种复杂性，引入了像[洋葱架构](https://aevitas.medium.com/the-onion-architecture-in-5-minutes-537e9c31da04)这样的架构，使事情变得更加复杂。你最终得到一个项目，即使是最基本的改变，你也需要对上面提到的所有概念有一个坚实的理解。

我写代码已经超过十五年了。这些年来，有一个原则是我真正开始考虑的写好代码的基本概念之一:在你写的所有代码中，*无情地删除复杂性。*

我认为有一种语言真正体现了一致性和简单性，那就是 [Go](https://go.dev) 。

我在过去几个月里写的大部分新代码都在 Go 中，这是一股新鲜空气。在这篇文章中，我将分享一些我学到的东西，因为我们将构建一个非常基本的 API。

![](img/c65e36b2d260d08334d2c5bb73287a1f.png)

由 [Sebastian Svenson](https://unsplash.com/@sebastiansvenson?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 做好准备

重要的事情先来。你需要安装 [Go 开发工具](https://go.dev/)，还需要一个代码编辑器。在我写作的时候，我正在运行 Go 1.19，我正在使用带有 Go 扩展的 Visual Studio 代码作为我的编辑器。

您可以从命令行直接使用 Go 做大多数事情，因此 IDE 是完全可选的。如果你愿意，你甚至可以使用记事本。

准备就绪后，通过创建一个新文件夹并运行以下命令来验证您已经正确设置了所有内容:

```
$ go mod init aevitas.dev/go-api
go: creating new go.mod: module aevitas.dev/go-api
```

您应该会在新文件夹中看到一个包含以下内容的`go.mod`文件:

```
module aevitas.dev/go-api

go 1.19
```

如果您愿意，您可以更改包的名称，即`aevitas.dev/go-api`——但是请记住，您需要在以后对所有的`import`语句应用这个更改！

# 主包

一个 Go 应用程序的入口点是`package main`——这个包应该包含一个名为`main()`的函数，这个函数将在应用程序启动时被调用。

因为我们不想自己编写 API 的所有代码，而是使用包来处理请求路由和获取环境变量，所以我们必须熟悉 Go 的包管理器。

不像 NuGet 或 NPM 这样的包管理器，Go 包管理器可以从你在`go get`抛出的任何 URL 中抓取一个包，只要目的 URL 包含一个有效的包。

首先，我们需要从环境变量中获取数据库连接字符串。您可以直接设置环境变量，或者使用一个`.env`文件来存储它们。我们将使用后者。

在您的项目文件夹中，运行`go get github.com/joho/godotenv` —这将在您的项目中安装`godotenv`包。

一旦就位，在与`go.mod`相同的目录中创建一个名为`main.go`的文件，并添加以下代码:

```
package main

import (
 "log"
 "os"

 "github.com/joho/godotenv"
)

func main() {
 err := godotenv.Load()

 if err != nil {
  log.Fatal(err)
 }

 dsn := os.Getenv("DB_DSN")
}
```

您可能会得到一个警告，说`dsn`未被使用。没错。我们稍后将回到这个文件并修复它。如果真的困扰你，就在开头加上`//`注释掉这一行。

# 书籍模型

我们将处理——令人惊讶地——书籍作为我们 API 的主题。在我们这样做之前，我们需要在我们的领域内定义一本书的内容。

这只是我们需要为我们的书创建一个模型的一种奇特的说法。在 Go 中，任何可能被外部使用的代码都应该放在`pkg`文件夹中(反过来，任何*从不*被外部使用的代码都应该放在`internal`文件夹中)。

因为我们正在构建一个 API，所以我们确实希望外部用户使用我们的模型代码。因此，在您的项目的根目录中，创建一个包含一个文件的`pkg`文件夹:`pkg/book.go`

在`book.go`文件中，添加以下代码:

```
package pkg

type Book struct {
 Id     uint64  `json:"id"`
 ISBN   string  `json:"isbn"`
 Title  string  `json:"title"`
 Author string  `json:"author"`
 Price  float64 `json:"price"`
}
```

注意包名是如何从`main`变成`pkg`的——这向编译器表明代码驻留在不同的包中。每个包都被视为一个独立的单元，所以为了使用这段代码，我们必须将其定义为`pkg.Book`

因为我们的模型最终将被序列化为 JSON，所以我们在字段的类型之后提供了序列化属性的名称:`json:"id"` —序列化程序将识别这一点并相应地命名属性。

目前关于图书模型的内容已经足够了。让我们添加一些功能。

![](img/e2d47f2325b720e1479d1b23dea01efc.png)

[Sole D'Alessandro G.](https://unsplash.com/@s___d___g?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 服务器

在本节开始之前，我想说有许多方法可以解决这个问题，这只是其中之一。我发现在 Go 中访问数据库的基本问题是，对数据库的访问必须通过程序。

在本指南中，我让`Server`类型对此负责。我不确定这是不是最好的方法，但在我见过的方法中，它对我来说最有意义。

不管怎样，我们开始吧。在您的项目文件夹中，创建`./api`文件夹，并添加一个`server.go`以便路径`./api/server.go`有效。

我们的服务器由两部分组成:一个数据库指针和一个路由器。为了保持它们有组织，我们在一个`struct`中定义它们——一个可以包含这两个元素的数据结构。代码的基本前提是这样的:

```
package api

type Server struct {
 DB  *gorm.DB
 Gin *gin.Engine
}
```

当然，Gorm 和 Gin 都是在外部包中定义的。我们需要抓住他们:

```
$ go get gorm.io/gorm
$ go get gorm.io/driver/postgres
$ go get github.com/gin-gonic/gin
```

请注意，在本教程中，我将使用本地 PostgreSQL 服务器。如果你没有，用 SQLite 代替也很好——只是用
`go get gorm.io/driver/sqlite`代替。

接下来，我们将编写一些函数来帮助我们设置服务器实例——初始化数据库连接、设置路由器等。我们还将添加一个`Ready()`函数来指示服务器是否准备好了:

```
func (s *Server) InitDb(dsn string) *Server {
 db, err := gorm.Open(postgres.Open(dsn))

 if err != nil {
  log.Fatal(err)
 }

 s.DB = db

 return s
}

func (s *Server) InitGin() *Server {
 g := gin.Default()

 s.Gin = g

 return s
}

func (s *Server) Ready() bool {
 return s.DB != nil && s.Gin != nil
}
```

插入这段代码后，您可能会看到许多红色的曲线，因为编译器无法解析某些引用。在命令行中运行`go mod tidy`来缓解这个问题——它将确保所有的包都能正确解析。

最后，如果您正在使用 SQLite，用一个`sqlite.Open`调用替换`postgres.Open`调用，并且只使用一个文件名而不是传入`dsn`——结果行看起来类似于:

```
db, err := gorm.Open(postgres.Open("books.db"))
```

拥有一个我们无法启动的服务器没有什么意义，所以在文件的底部，添加:

```
func (s *Server) Start(ep string) error {
 if !s.Ready() {
  return errors.New("server isn't ready - make sure to init db and gin")
 }

 if err := http.ListenAndServe(ep, s.Gin.Handler()); err != nil {
  log.Fatal(err)
 }

 return nil
}
```

我们将所有这些函数定义为`Server`类型的方法。这意味着我们不能直接调用它们，而是必须创建一个服务器实例，然后调用这些方法。我们会在`main.go`文件中解决这个问题。

# 主要重访

回到`main.go`，我们现在有了一个服务器类型，我们可以实例化并运行它来服务我们的 API。在`dsn`定义行周围，添加以下内容:

```
 srv := &api.Server{}

 dsn := os.Getenv("DB_DSN")

 srv.InitDb(dsn)
 srv.InitGin()

 srv.RegisterRoutes()

 srv.Start(":8050")
```

这里的第一行创建了一个`Server`类型的新实例，没有指定它的任何字段。后面的`InitDb`和`InitGin`调用是我们刚刚实现的，会设置我们的数据库和路由器。

我们最终的`main.go`文件应该是这样的:

```
package main

import (
 "log"
 "os"

 "aevitas.dev/go-books/api"
 "github.com/joho/godotenv"
)

func main() {
 err := godotenv.Load()

 if err != nil {
  log.Fatal(err)
 }

 srv := &api.Server{}

 dsn := os.Getenv("DB_DSN")

 srv.InitDb(dsn)
 srv.InitGin()

 srv.RegisterRoutes()

 srv.Start(":8050") // Or grab this from the env, too!
}
```

你们当中善于观察的人会注意到两件事:

1.  我们实际上还没有一个 API 来服务
2.  我们没有在`server.go`中实现`RegisterRoutes`

两者都是正确的。让我们添加一些处理程序，这样我们就可以开始提供一些内容。

![](img/f2e09397757c617464e0c748be625b19.png)

[张艺](https://unsplash.com/@kencheungphoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 经手人

与服务器一样，有很多很多方法可以做到这一点。出于本文的目的，我再次决定保持简单，直接在处理程序中实现逻辑。

在`api`文件夹中创建一个名为`add_book.go`的文件，并添加以下代码:

```
package api

import (
 "log"
 "net/http"

 "aevitas.dev/go-books/pkg"
 "github.com/gin-gonic/gin"
 "github.com/oklog/ulid"
)

func (s *Server) HandleAddBook(ctx *gin.Context) {
 var book pkg.Book

 err := ctx.BindJSON(&book)

 if err != nil {
  ctx.AbortWithError(http.StatusBadRequest, err)
  return
 }

 book.Id = ulid.Now()

 r := s.DB.Create(&book)

 if r.Error != nil {
  log.Fatal(r.Error)
 }

 s.DB.Save(&book)

 ctx.JSON(http.StatusOK, &book)
}
```

确保抓住`ulid`包，然后运行`go mod tidy`。

`ctx`参数包含 Gin 上下文，或者更确切地说是 HTTP 上下文。这个上下文包含我们从用户那里收到的请求，以及我们将为他们提供的最终响应。

首要任务是定义我们之前定义的模型的一个`book`变量— `pkg.Book`接下来，我们将尝试将请求内容反序列化到这个变量上。如果一切顺利，我们将得到一本有标题、ISBN、作者，只要你说得出名字的有效的书。

如果没有，我们将通过调用`ctx.AbortWithError`来缩短请求，并指定一个错误的请求，以及我们从序列化程序收到的错误。

请注意，虽然这确实缩短了响应管道，但代码仍将继续执行——因此出现了`return`

因为这个处理程序被声明为`Server`的一个方法，所以我们可以访问`s.DB`指针来访问数据库。我们将创建并保存这本书，如果一切顺利，将这本书作为 JSON 对象返回给调用者。

既然我们可以创造书籍，我们也应该能够检索它们。添加一个包含以下(非常相似)代码的`get_book.go`处理程序:

```
package api

import (
 "net/http"

 "aevitas.dev/go-books/pkg"
 "github.com/gin-gonic/gin"
)

func (s *Server) HandleGetByISBN(ctx *gin.Context) {
 var book pkg.Book

 isbn := ctx.Param("isbn")

 ret := s.DB.First(&book, "isbn = ?", isbn)

 if ret.RowsAffected == 0 {
  ctx.AbortWithStatus(http.StatusNotFound)
  return
 }

 if ret.Error != nil {
  ctx.AbortWithError(http.StatusBadRequest, ret.Error)
  return
 }

 ctx.JSON(http.StatusOK, book)
}
```

正如您所看到的，两者之间的主要区别在于，后者从请求参数中获取 ISBN(因为请求将是 GET 而不是 POST，因此没有人)并执行简单的 DB 查询。

现在所缺少的(除了测试！)是`RegisterRoutes`功能。

# 路线

Go 附带了一个非常可靠的 HTTP 服务器，包含在`http`包中。我们将使用它来服务我们的 API，同时使用 Gin 来处理路由。杜松子酒比我在这篇文章中展示的要强大得多。

在`api`包中，创建一个名为`routes.go`的文件，并添加以下代码:

```
package api

func (s *Server) RegisterRoutes() {
 s.Gin.GET("/books/:isbn", s.HandleGetByISBN)
 s.Gin.POST("/books", s.HandleAddBook)
}
```

与单独的处理程序类似，我们将这些函数声明为`Server`的方法——即使它们在不同的文件中。这只对同一个包中的文件是可能的，这也是我更喜欢的将功能捆绑在一起的方式。

比方说，你也可以用详细的作者信息来扩展这个 API，你可以把我们上面写的处理程序移到一个`books`包中，暴露它自己的`RegisterBookRoutes`函数或类似的东西，对`authors`做同样的事情——这样你就可以把你的 API 整齐地打包到功能的[垂直片](https://jimmybogard.com/vertical-slice-architecture/)中。

# 跑步

仅此而已！现在只需添加一个`.env`文件，并用类似下面的代码定义`DB_DSN`:

`host=localhost user=foo password=bar dbname=books`

你就准备好了。通过运行`go run .`来运行您的应用程序，您应该能够在端口 8050 上使用[本地主机上的 API。](http://localhost:8050)

# 结论

我们已经在 Go 中构建了一个非常简单的 API 来访问数据库以存储和检索书籍，并且已经将一些基本结构应用到我们的项目中，以便在将来对其进行扩展。

据我所知，不存在像其他语言那样的“最佳实践”或标准的单一结构。这在很大程度上取决于你个人的需求和偏好，最终取决于你能做出什么样的工作。也许这就是为什么我认为这种语言是一股新鲜空气。

本文附带的最终代码可以在[这里](https://github.com/aevitas/go-books-api)找到。

感谢您的阅读，希望本指南对您有用！