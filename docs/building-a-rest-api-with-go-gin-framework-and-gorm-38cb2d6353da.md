# 用 Go、Gin 框架和 GORM 构建 REST API

> 原文：<https://betterprogramming.pub/building-a-rest-api-with-go-gin-framework-and-gorm-38cb2d6353da>

## 加强你的后端项目

![](img/8326aedd9dd567e0703db747ff87a359.png)

我在寻找一个框架来启动一个后端项目，以学习和使用 GORM，这是一个用于 Go 的 ORM 库。和 Gin 引起了我的注意，我发现该文档非常好，其中的示例有助于构建 REST API。

我写这篇文章是作为我学习过程的一部分，所以我将与你分享如何使用 Gin HTTP Web 框架和 GORM 作为 ORM 来构建一个 REST API。

# **Gin 框架**

根据其[文档](https://gin-gonic.com/docs/):

> Gin 是一个用 Go (Golang)编写的 web 框架。它有一个类似 martini 的 API，由于有了 httprouter，它的性能提高了 40 倍。如果你需要性能和良好的生产力，你会喜欢杜松子酒。

## **先决条件**

*   安装完毕
*   Gin 框架
*   戈尔姆

```
restApi/ 
     grocery/ 
          groceryService.go 
     model/ 
       groceryModel.go 
       database.go 
     main.go 
     go.mod 
     go.sum 
     database.db
```

我们将在`grocery.go`开始定义我们的模型:

```
package model import ( 
"gorm.io/gorm" 
) type Grocery struct { gorm.Model 
Name string `json:"name"` 
Quantity int `json:"quantity"` }
```

当您向数据库添加数据时，在模型的字段上声明`gorm.Model`会自动将这些字段添加到模型中:

```
type Model struct { 
ID uint `gorm:"primaryKey"` 
CreatedAt time.Time 
UpdatedAt time.Time 
DeletedAt gorm.DeletedAt `gorm:"index"` 
}
```

`database.go`:

数据库函数将连接数据库。我使用的是 SQLite，但是 GORM 有 MySQL、PostgreSQL 和 SQLServer 的驱动程序。`db`变量将打开`database.db`文件，数据将存储在该文件中。

`db.AutoMigrate(&Grocery{})`将自动迁移我们的模式，并保持它最新。

`groceryService.go`:

`GetGroceries`是获取存储在数据库中的所有食品杂货的函数。我们声明了变量`groceries`，然后我们使用变量 db 来使用与* `gorm.DB`相关联的方法 Find，并将食品杂货的地址作为参数传递。

然后我们使用`c.JSON`以 JSON 的形式发送食品杂货中的状态和数据作为响应。

`GetGrocery`根据 id 获取杂货店。它从 URI 获取 id。我们使用的方法是，根据 id 获取杂货店。

如果数据库中不存在 id，将发送一个未找到状态和一条错误消息作为响应。如果存在，那么杂货数据将作为响应发送。

我们用字段 Name 和 Quantity 创建一个结构`NewGrocery`，并添加`binding: required`。

然后在`PostGrocery`中，我们声明一个类型为`NewGrocery`的变量杂货店。如果 post 请求中有一个字段缺失或有打印错误，则会发送一个包含错误的错误请求状态作为响应。

如果一切正确，像这样:`{“name”: “Apple”, “quantity”: 5}`。将创建另一个变量`newGrocery`，类型为杂货。

它的数据将与杂货变量相同。然后我们调用方法 Create，并传递`newGrocery`的地址。状态和数据将作为响应发送。

在`UpdateGrocery`中，首先，我们检查 URI 中通过它的 id 是否存在，如果不存在，将发送一个错误和未找到状态。

如果它确实存在，将创建一个类型为`GroceryUpdate`的变量来检查字段，如果一切都正确，那么我们使用方法 Updates，并发送杂货店更新的状态作为响应。

在`DeleteGrocery`中，如果 id 在数据库中不存在，将发送错误和未找到状态。

如果它确实存在，方法 Delete 将删除杂货店。状态，并将发送一条消息作为响应。

`main.go`:

在主函数中，我们调用了`model.Database()`来初始化数据库。

然后，我们用以下命令启动路由器:

```
router := gin.Default()
```

现在我们可以使用各自的 HTTP 方法，将它们的 URI 和函数作为参数。使用 GET、POST、PUT 和 DELETE 方法的模式是相同的:

```
 //method.(URI, function)
router.GET("/groceries", grocery.GetGroceries)
```

然后，我们使用方法 Run 并将端口作为参数传递。我们可以使用不带参数的 Run，默认情况下它服务于:8080。

感谢您花时间阅读这篇文章。

完整的代码是[这里是](https://github.com/carlosm27/apiwithgorm)。

# 参考

*   [杜松子酒](https://gin-gonic.com/docs/)
*   [戈尔姆](https://gorm.io/docs/)

```
**Want to Connect?**You can contact me through [Twitter](https://twitter.com/Carlos_marcv), [LinkedIn](https://www.linkedin.com/in/carlos-marcano-a2135a134/).*Originally published at* [*https://carlosmv.hashnode.dev*](https://carlosmv.hashnode.dev/building-a-rest-api-with-go-gin-framework-and-gorm)
```