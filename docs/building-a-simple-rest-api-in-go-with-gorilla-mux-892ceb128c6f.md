# 用 Gorilla/Mux 在 Go 中构建一个简单的 REST API

> 原文：<https://betterprogramming.pub/building-a-simple-rest-api-in-go-with-gorilla-mux-892ceb128c6f>

## 启动你的后端

![](img/c2f8ed750786c7a0102244873733336b.png)

照片由[罗曼·辛克维奇·🇺🇦](https://unsplash.com/@synkevych?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近我开始学习如何用 Gorilla/mux 构建 API，主要是因为我对后端开发感兴趣，并且我一直在学习。

我找到了 Gorilla/mux，老实说它的[文档](https://pkg.go.dev/github.com/gorilla/mux)非常好，解释得很好。

根据其文档，Gorilla/mux 是:

> Package gorilla/mux 实现了一个请求路由器和调度程序，用于将传入的请求匹配到它们各自的处理程序。
> 
> mux 这个名称代表“HTTP 请求多路复用器”。比如标准的 http。ServeMux，Mux。路由器将传入的请求与注册的路由列表进行匹配，并为匹配 URL 或其他条件的路由调用处理程序。主要特点是:
> 
> 它实现了 http。处理程序接口，因此它与标准的 http.ServeMux 兼容。可以基于 URL 主机、路径、路径前缀、方案、标头和查询值、http 方法或使用自定义匹配器来匹配请求。URL 主机、路径和查询值可以包含带有可选正则表达式的变量。注册的 URL 可以被构建，或者“反转”，这有助于维护对资源的引用。路由可以用作子路由:只有当父路由匹配时，才会测试嵌套路由。这对于定义共享相同条件(如主机、路径前缀或其他重复属性)的路由组非常有用。另外，这优化了请求匹配。

根据它的文档，我学会了构建一个简单的杂货 API，以获取特定的杂货及其数量、所有杂货及其数量，发布一个杂货项目并更新它。

这个项目的三个部分看起来像这样:

```
------groceriesAPI
       |---grocery.go
       |---handler.go
       |---main.go
```

在`grocery.go`中，我们定义 API 的模型，`handler.go`是管理请求的函数，`main.go`是注册我们的 URL 路径。

首先，我们安装 Gorilla/mux。

```
go get -u github.com/gorilla/mux
```

`grocery.go`的代码是:

```
package main

type Grocery struct {

    Name     string `json: "name"`
    Quantity int    `json: "quantity"`
}
```

我们的模型很简单，只有两个字段，食品名称和数量。

`main.go is`的代号:

在上面的代码中，我们将“r”定义为路由器后。在上面的例子中，方法`HandleFunc`有两个参数，第一个是 URL 路径，第二个是处理它的函数，当第一个匹配时，调用第二个。例如，如果请求“all 杂货”，函数`AllGroceries`将处理它并提供所请求的数据，在这种情况下，是数据库中的所有杂货(我们在这个项目中没有使用数据库)。

我们有五条路径，一条请求所有的食品杂货，一条按名称请求食品杂货，一条添加食品杂货，一条更新按名称请求的食品杂货，还有一条按名称删除食品杂货。它们都使用端口 10000，如果发生错误，将会触发信息错误并停止程序。

现在，我们将转移到`handler.go`来定义处理每个请求的函数:

我们定义了一个变量`groceries`，并给它分配了一个包含两个杂货信息的数组，因为我们没有使用数据库。当`AllGroceries`被调用时，它将返回包含所有杂货的数组，但以 JSON 的形式返回。

在这个函数中，我们使用`mux.Vars()`从路线中检索杂货店的名称。然后遍历切片，只返回请求的杂货。

在`GroceriesToBuy`中，我们接收 post 请求并将其分配给`reqBody`，然后我们用`Grocery`定义一个变量杂货作为类型。我们使用 JSON 包中的 unmarshal 方法解析 JSON 数据发布请求，并将其存储在杂货变量中。之后，我们将它添加到食品杂货中。

`DeleteGrocery`如果名称路径与切片中的一个杂货店匹配，将删除该杂货店。然后更新切片。

如果名称路径与切片中的一个杂货店匹配，最后一个函数将更新一个杂货店。它将接受 PUT 请求，对其进行解码并将其存储在`updateGrocery`变量中。然后它会把它附加到杂货上。

老实说，我很喜欢学习如何在 go 中创建 Rest API。下一步是添加一个类似 SQLite 或 PostgreSQL 的数据库。

完整的代码是[这里的](https://github.com/carlosm27/Go_projects/tree/main/groceriesApi)

## **资源**

 [## 复用器

### https://www.gorillatoolkit.org/pkg/mux 包 gorilla/mux 实现了一个请求路由器和调度器，用于匹配…

pkg.go.dev](https://pkg.go.dev/github.com/gorilla/mux)