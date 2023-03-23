# Go 中服务器端 HTTP APIs 概述

> 原文：<https://betterprogramming.pub/overview-of-server-side-http-apis-in-go-44f052737e4b>

## 探索 net/http 包

![](img/34e5a1fb88ed490118d6bd7e98ec0056.png)

[布兰登·莫温克尔](https://unsplash.com/@bmowinkel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/trains?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

欢迎来到另一个版本的[](https://medium.com/@abhishek1987/just-enough-go-blog-series-c1cd62b04beb)**——关于 [Go 编程语言](https://golang.org/)的系列文章，涵盖了一些最常用的 [Go 标准库包](https://golang.org/pkg) : `encoding/json`、`io`、`net/http`、`sync`等。**

**在本文中，我们将探索为 HTTP 服务提供服务器端和客户端 API 的`[net/http](https://golang.org/pkg/net/http/)`包。这一部分将提供重要服务器组件的概述(客户端 API 将在另一篇文章中介绍)**

**所有的代码示例都可以在 GitHub 上找到。**

**让我们从基本的构建模块开始:`ServeMux`和`Server`**

# **多路复用器**

**简单来说，`ServeMux`是一个 HTTP 请求复用器。它负责将请求中的 URL 匹配到适当的处理程序并执行它。你可以通过调用`http.NewServeMux`来创建一个。接下来要做的是使用`Handle`和`HandleFunc`方法将 URL 和它们各自的处理程序实现附加到一个`ServeMux`实例。**

**我们来看看如何使用`[Handle](https://golang.org/pkg/net/http/#ServeMux.Handle)` [方法](https://golang.org/pkg/net/http/#ServeMux.Handle)。它接受一个`String`和一个`http.Handler`:**

```
func (mux *ServeMux) Handle(pattern string, handler Handler)
```

**`[http.Handler](https://golang.org/pkg/net/http/#Handler)` [是与`ServeHTTP`方法的接口](https://golang.org/pkg/net/http/#Handler)(`Handle`方法中的第二个参数):**

```
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

**我们可以简单地使用一个`struct`来提供实现。例如:**

```
func (h home) ServeHTTP(rw http.ResponseWriter, r *http.Request) {
	rw.Write([]byte("Welcome to the Just Enough Go! blog series!"))
}
```

**然后将其连接到多路复用器，如下所示:**

```
mux := http.NewServeMux()
mux.Handle("/", home{})
```

**让我们为我们的`ServeMux` ( `mux`)添加另一个处理程序。这一次，我们将使用签名如下的`[HandleFunc](https://golang.org/pkg/net/http/#ServeMux.HandleFunc)`变体:**

```
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```

**与`Handle`方法不同，`HandleFunc`接受函数形式的处理程序实现(以及调用它的路径)。你可以这样使用它:**

```
mux.HandleFunc("/posts", func(rw http.ResponseWriter, req *http.Request) {
	rw.Write([]byte("Visit http://bit.ly/just-enough-go to get started"))
})
```

# **服务器(HTTP 服务器)**

**现在我们有了一个多路复用器，如果用户导航到我们服务的根目录，它可以做出响应；即`/`以及`/posts`。让我们用一个`Server`把它们绑在一起。创建一个`Server`的新实例很容易:**

```
server := http.Server{Addr: ":8080", Handler: mux}
```

**我们可以为 HTTP 服务器定义许多参数，但是让我们来看看几个重要的参数，即`Addr`和`Handler`(上面突出显示)——`Addr`是服务器监听的地址，例如`http://localhost:8080`和`Handler`实际上是一个`http.Handler`实例。**

**`Handler`位很有趣，因为我们刚刚看到了`ServeMux`中的`Handle`方法也接受一个`http.Handler`。那么，我们是否在这里传递了与在`ServeMux`中传递`Handle`方法相同的实例呢？如果是的话，再做一次又有什么意义呢？**

**如果您只有想要处理的路线或路径，您可以传递一个`http.Handler`的实例(例如本例中的`home{}`),并完全跳过`ServeMux`。否则，在大多数情况下，您可以/应该传递一个`ServeMux`的实例，这样您就可以处理多个路由/路径(例如`/home`、`/items`等)。)这是可能的，因为它实现了`http.Handler`。在内部，它通过基于`http.Request`中的路径(URL)分派或路由到适当的处理程序来工作。**

**它根据`http.Handler`接口的要求定义了一个`ServeHTTP`方法:**

```
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) 
```

***注意:* `*Handler*` *可以是*`*nil*`*——这个场景将在本文后面讨论。***

**太好了！到目前为止，我们有一个带有两个处理程序的`ServeMux`，我们将`Server`与多路复用器相关联，并定义了它将监听的位置。最后，你只需要使用`[ListenAndServe](https://golang.org/pkg/net/http/#Server.ListenAndServe)`方法`start`它:**

```
server.ListenAndServe()
```

**就是这样。下面是合并后的代码(相当小！):**

```
package mainimport (
	"log"
	"net/http"
)func main() {
	mux := http.NewServeMux() mux.Handle("/", home{}) mux.HandleFunc("/posts", func(rw http.ResponseWriter, req *http.Request) {
		rw.Write([]byte("Visit http://bit.ly/just-enough-go to get started")) }) server := http.Server{Addr: ":8080", Handler: mux}
	log.Fatal(server.ListenAndServe())
}type home struct{}func (h home) ServeHTTP(rw http.ResponseWriter, r *http.Request) {
	rw.Write([]byte("Welcome to the \"Just Enough Go\" blog series!!"))
}
```

**要尝试这种方法:**

*   **只需将代码保存在文件中(如`go-http-1.go`)**
*   **运行它— `go run go-http-1.go`**
*   **访问端点— `curl http://localhost:8080/`和`curl [http://localhost:8080/posts](http://localhost:8080/posts)`**

## **默认多路复用器**

**为了使事情更简单，有一个现成的多路复用器`DefaultServeMux`。你不需要使用显式的`ServeMux`。为此，`ServeMux`中可用的`Handle`和`HandleFunc`方法也在`net/http`包中作为全局函数公开——您可以以同样的方式使用它们！**

```
http.Handle("/users",myHandler{})http.HandleFunc("/items",func(rw http.ResponseWriter, req *http.Request){
    //handler logic
})
```

**要启动 HTTP 服务器，可以使用`http.ListenAndServe`函数，就像使用`Server`实例一样。**

```
func ListenAndServe(addr string, handler Handler) error
```

**如果您已经使用了`http.Handle`和/或`http.HandleFunc`来指定各个路由的处理程序实现，那么`handler`参数可以是`nil`。**

# **充当处理程序**

**到目前为止，我们已经看到了如何使用 struct 来实现`http.Handler`接口并在`HandleFunc`中使用它。您可能还希望使用独立函数而不声明结构。`net/http`包为此定义了一个函数类型:`[http.HandlerFunc](https://golang.org/pkg/net/http/#HandlerFunc)`**

```
type HandlerFunc func(ResponseWriter, *Request)
```

**`HandlerFunc`允许您使用普通函数作为 HTTP 处理程序。例如:**

```
func welcome(rw http.ResponseWriter, req *http.Request) {
	rw.Write([]byte("Welcome to Just Enough Go"))
}
```

**`welcome`是具有所需签名的独立函数。您可以在接受如下`http.Handler`的`Handle`方法中使用它:**

```
http.ListenAndServe(":8080", http.HandlerFunc(welcome))
```

***注意:* `*HandlerFunc(f)*` *是调用* `*f*`函数的处理程序**

**代码如下所示:**

```
package mainimport "net/http"func main() {
	http.Handle("/welcome", http.HandlerFunc(welcome))
	http.ListenAndServe(":8080", nil)
}func welcome(rw http.ResponseWriter, req *http.Request) {
	rw.Write([]byte("Welcome to Just Enough Go"))
}
```

**要尝试这种方法:**

*   **只需将代码保存在一个文件中(如`go-http-2.go`)并**
*   **运行它— `go run go-http-2.go`**
*   **访问端点— `curl [http://localhost:8080/welcome](http://localhost:8080/welcome)`**

**这就是这篇博客的全部内容，我们在这里介绍了由`net/http`包提供的服务器端 HTTP APIs 的基本构造。**

**我真的希望你喜欢并从中学到了一些东西。如果你做了，请喜欢并跟随！**