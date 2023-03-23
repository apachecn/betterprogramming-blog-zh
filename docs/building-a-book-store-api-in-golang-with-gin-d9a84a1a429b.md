# 用 Gin 在 Golang 构建书店 API

> 原文：<https://betterprogramming.pub/building-a-book-store-api-in-golang-with-gin-d9a84a1a429b>

## 创建应用程序

![](img/2532a28023527443b714dce800fcc82f.png)

# 介绍

Go 社区认为我们不需要框架来开发 web 服务。我同意这一点。当你在不使用任何框架的情况下工作时，你了解了开发的来龙去脉。但是一旦你学会了事情是如何运作的，你必须生活在一个社区中，不要重复发明轮子。

我以前在 Go 中创建了 POC，我必须处理 HTTP 头、序列化/反序列化、错误处理、数据库连接等等。但是现在我决定加入 Gin 社区，因为它是软件开发社区中被广泛接受的社区之一。

虽然我写这篇文章是作为一篇独立的文章，并在这里保持事情简单。但是我想在这些例子的基础上继续讨论认证、授权、数据库(包括 Postgres、ORM)、swagger 和 GraphQL。当我创建它们时，将链接的职位。

# 为什么是杜松子酒

![](img/e8446076d464b121b378a9b467a82a09.png)

你可能想用杜松子酒的原因有很多。如果你问我，我是杜松子酒明智的默认大风扇。

我喜欢 Gin 的另一点是它是一个完整的框架。你不需要单独的多路复用器和单独的中间件库等等。最重要的是，已经有很多常见的东西，你不必重新发明。它确实提高了我们的生产力。虽然我没有在生产中使用它，但我已经开始感受到它了。

# 杜松子酒世界你好

```
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.New()

	router.GET("/ping", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"message": "pong",
		})
	})

	router.Run()
}
```

让我们稍微熟悉一下杜松子酒。

```
router := gin.New()
```

这就创建了一个`Engine`的实例。`gin.Engine`是杜松子酒的核心。它还充当路由器，这就是为什么我们将引擎实例放入一个名为`router`的变量中。

```
router.GET("/ping", func(ctx *gin.Context) {
```

这将路由`/ping`绑定到一个处理程序。在上面的例子中，我创建了一个匿名函数，但是它也可以是一个单独的函数。这里要注意的是这个函数的参数。`*gin.Context`。`Context`是除发动机之外的又一重要构造。`Context`有将近 100 个方法。新手应该把大部分时间花在理解这个`Context`结构和它们的方法上。

现在让我们看看下面几行:

```
ctx.JSON(http.StatusFound, gin.H{
			"message": "pong",
		})
```

`*gin.Context`方法之一是`JSON`。该方法用于向客户端发送 JSON 响应。这意味着它自动将响应的`Content-Type`设置为`application/json`。JSON 方法接受一个 HTTP 状态代码和一个响应映射。`gin.H`是`map[string]interface{}`的别名。所以基本上我们可以创建一个对象，它可以有字符串键和我们想要的任何值。

接下来是:

```
router.Run()
```

引擎。Run 简单地将我们的路由器和路由处理器一起绑定到 http.Server，默认端口是`8080`，但是如果您愿意，您可以在这里传递另一个地址。

# 书店 API

我之前已经在书店上做过一次 POC，当时想在 MongoDB 和 Go 之间做一个连接的原型。但这次我的目标是让 Postgres 和 GraphQL 合并。

首先，我希望您建立一个这样的目录结构:

```
$ tree
.
├── db
│   └── db.go
├── go.mod
├── go.sum
├── handlers
│   └── books.go
├── main.go
└── models
    └── book.go
```

让我们开始填写这些文件。

# db/db.go

```
package db

import "github.com/santosh/gingo/models"

// Books slice to seed book data.
var Books = []models.Book{
	{ISBN: "9781612680194", Title: "Rich Dad Poor Dad", Author: "Robert Kiyosaki"},
	{ISBN: "9781781257654", Title: "The Daily Stotic", Author: "Ryan Holiday"},
	{ISBN: "9780593419052", Title: "A Mind for Numbers", Author: "Barbara Oklay"},
}
```

我决定在这篇文章中使用内存中的数据库，而不是现在就进入设置数据库的复杂性。在这个文件中，我在`db.Books` slice 中植入了一些书籍。

如果`models.Book`使，你很好奇，下一个文件是那只。

# models/book.go

```
package models// Book represents data about a book.
type Book struct {
 ISBN   string  `json:"isbn"`
 Title  string  `json:"title"`
 Author string  `json:"author"`
}
```

这里没有什么奇特的，我们现在只有 3 个领域。它们都是带有 struct 标签的字符串。

让我们先看看我们的 main.go，然后再看 handlers.go。

# main.go

```
package mainimport (
 "github.com/gin-gonic/gin"
 "github.com/santosh/gingo/handlers"
)func setupRouter() *gin.Engine {
    router := gin.Default()
 router.GET("/books", handlers.GetBooks)
 router.GET("/books/:isbn", handlers.GetBookByISBN)
 // router.DELETE("/books/:isbn", handlers.DeleteBookByISBN)
 // router.PUT("/books/:isbn", handlers.UpdateBookByISBN)
 router.POST("/books", handlers.PostBook)return router
}func main() {
    router := setupRouter()
 router.Run(":8080")
}
```

几乎类似于我们上面看到的 hello world 示例。但是这次我们用了`gin.Default()`而不是`gin.New()`。`Default`带有我们大多数人想要的默认设置。比如日志中间件。

坦白说，我还没怎么用过 Gin 的中间件。但是创建你的中间件非常简单。我会在帖子底部放一些链接供你探索。但是现在，让我们看看我们的处理程序。

# handlers/books.go

```
package handlersimport (
 "net/http""github.com/gin-gonic/gin"
 "github.com/santosh/gingo/db"
 "github.com/santosh/gingo/models"
)// GetBooks responds with the list of all books as JSON.
func GetBooks(c *gin.Context) {
 c.JSON(http.StatusOK, db.Books)
}// PostBook takes a book JSON and store in DB.
func PostBook(c *gin.Context) {
 var newBook models.Book// Call BindJSON to bind the received JSON to
 // newBook.
 if err := c.BindJSON(&newBook); err != nil {
  return
 }// Add the new book to the slice.
 db.Books = append(db.Books, newBook)
 c.JSON(http.StatusCreated, newBook)
}// GetBookByISBN locates the book whose ISBN value matches the isbn
func GetBookByISBN(c *gin.Context) {
 isbn := c.Param("isbn")// Loop over the list of books, look for
 // an book whose ISBN value matches the parameter.
 for _, a := range db.Books {
  if a.ISBN == isbn {
   c.JSON(http.StatusOK, a)
   return
  }
 }
 c.JSON(http.StatusNotFound, gin.H{"message": "book not found"})
}// func DeleteBookByISBN(c *gin.Context) {}// func UpdateBookByISBN(c *gin.Context) {}
```

真正的内容在这个处理程序文件中。这可能需要一些解释。

`handlers.GetBooks`，绑定到`GET /books`转储整个书切片。

绑定到`GET /books/:isbn`的`handlers.GetBookByISBN`也做同样的事情，但是它也接受`isbn`作为 URL 参数。该处理程序扫描整个切片并返回匹配的图书。扫描大切片可能不是最佳解决方案，但是不要忘记，在我们继续开发这个书店的同时，我们将实现一个真正的数据库。

这里最有意思的是`handlers.PostBook`，绑定了`POST /books`。`c.BindJSON`是一个神奇的方法，它从请求中获取 JSON，并将其存储到之前创建的`newBook` struct 中。后来

# 试验

我们现在需要一点改变。我们需要从 main.go 中删除这些内容:

```
@@ -1,17 +1,9 @@
 package main

-import (
-       "github.com/gin-gonic/gin"
-       "github.com/santosh/gingo/handlers"
-)
+import "github.com/santosh/gingo/routes"

 func main() {
-       router := gin.Default()
-       router.GET("/books", handlers.GetBooks)
-       router.GET("/books/:isbn", handlers.GetBookByISBN)
-       // router.DELETE("/books/:isbn", handlers.DeleteBookByISBN)
-       // router.PUT("/books/:isbn", handlers.UpdateBookByISBN)
-       router.POST("/books", handlers.PostBook)
+       router := routes.SetupRouter()

        router.Run(":8080")
 }
```

并把它放到一个新文件中。

## routes/roures.go

```
package routesimport (
 "github.com/gin-gonic/gin"
 "github.com/santosh/gingo/handlers"
)func SetupRouter() *gin.Engine {
 router := gin.Default()
 router.GET("/books", handlers.GetBooks)
 router.GET("/books/:isbn", handlers.GetBookByISBN)
 // router.DELETE("/books/:isbn", handlers.DeleteBookByISBN)
 // router.PUT("/books/:isbn", handlers.UpdateBookByISBN)
 router.POST("/books", handlers.PostBook)return router
}
```

我有对你有意义的改变。我们这样做是因为我们需要从测试中启动服务器。

接下来，我们在处理程序中创建一个`books_test.go`。

## handlers/books_test.go

```
package handlers_testimport (
 "bytes"
 "encoding/json"
 "net/http"
 "net/http/httptest"
 "testing""github.com/santosh/gingo/models"
 "github.com/santosh/gingo/routes"
 "github.com/stretchr/testify/assert"
)func TestBooksRoute(t *testing.T) {
 router := routes.SetupRouter()w := httptest.NewRecorder()
 req, _ := http.NewRequest("GET", "/books", nil)
 router.ServeHTTP(w, req)assert.Equal(t, 200, w.Code)
 assert.Contains(t, w.Body.String(), "9781612680194")
 assert.Contains(t, w.Body.String(), "9781781257654")
 assert.Contains(t, w.Body.String(), "9780593419052")
}func TestBooksbyISBNRoute(t *testing.T) {
 router := routes.SetupRouter()w := httptest.NewRecorder()
 req, _ := http.NewRequest("GET", "/books/9781612680194", nil)
 router.ServeHTTP(w, req)assert.Equal(t, 200, w.Code)
 assert.Contains(t, w.Body.String(), "Rich Dad Poor Dad")
}func TestPostBookRoute(t *testing.T) {
 router := routes.SetupRouter()book := models.Book{
  ISBN: "1234567891012",
  Author: "Santosh Kumar",
  Title: "Hello World",
 }body, _ := json.Marshal(book)w := httptest.NewRecorder()
 req, _ := http.NewRequest("POST", "/books", bytes.NewReader(body))
 router.ServeHTTP(w, req)assert.Equal(t, 201, w.Code)
 assert.Contains(t, w.Body.String(), "Hello World")
}
```

同样，这也是不言自明的。我认为上面的代码不需要任何解释。我们正在测试特定字符串的响应代码和响应主体。

让我们也运行一下测试，看看效果如何:

```
go test ./... -cover
?       github.com/santosh/gingo        [no test files]
?       github.com/santosh/gingo/db     [no test files]
ok      github.com/santosh/gingo/handlers       (cached)        coverage: 83.3% of statements
?       github.com/santosh/gingo/models [no test files]
?       github.com/santosh/gingo/routes [no test files]
```

# 锻炼

是的，让我们通过增加一些互动性来使这篇博文更有趣。我有一些任务给你，你需要自己解决。请试穿一下。它们是:

1.  实现`DeleteBookByISBN`和`UpdateBookByISBN`处理程序并启用它们。
2.  为上面提到的处理程序编写测试。
3.  我们的测试非常简单。我们的负责人也是。我们不做任何错误处理。将错误处理添加到处理程序中，并编写测试来验证它们。

# 结论

我们已经看到了在 Gin 中创建 hello world 应用程序是多么简单。但这一旅程并未就此结束。下次我会带更多教程回来。在那之前，再见。

# 资源

*   https://sosedoff.com/2014/12/21/gin-middleware.html
*   [https://santoshk . dev/posts/2020/sending-post-request-in-go-in-a-body/](https://santoshk.dev/posts/2020/sending-post-request-in-go-with-a-body/)

*最初发布于*[*https://santoshk . dev*](https://santoshk.dev/posts/2022/building-a-bookstore-api-in-golang-with-gin/)