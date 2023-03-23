# Vapor 4 入门—模型关系

> 原文：<https://betterprogramming.pub/getting-started-with-vapor-4-model-relationships-e6e73d6c2412>

## 扩展待办事项应用程序，了解这一以 Swift 编写的服务器优先工具的更多高级功能

![](img/e65d8681e5146783b12af4739dd82a5d.png)

# 介绍

在上一篇文章中，我们介绍了 Vapor 并开始创建一个 TODO 应用程序。

[](/getting-started-with-vapor-4-part-1-3d731e64fb45) [## Vapor 4 入门—在 Swift 中构建一个基本的 Todo 应用程序

### 了解这一以 Swift 编写的服务器优先工具的基础知识(不那么基础)

better 编程. pub](/getting-started-with-vapor-4-part-1-3d731e64fb45) 

就目前而言，这款应用没什么用。它只是让你创建一个列表，但你还不能添加任何提醒。我们稍后会解决这个问题。

到本文结束时，我们将有一个工作的 web 应用程序来存储我们的待办事项列表。在这个过程中，我们将继续了解 Vapor 及其工作原理。你可以在我的回购协议中找到完整的实现:

[https://github.com/fermoya/vapor-tutorial](https://github.com/fermoya/vapor-tutorial)

# 高级路由

到目前为止，我们直接在`routes.swift`中定义所有端点，如下所示:

```
app.get("todo-lists") { ... }
app.post("todo-lists") { ... }
...
```

一旦我们推出新型号，情况就会变得非常混乱。想象一个真实的 web 应用程序，数据库中保存了多少不同的实体:用户、集合、设置、集合等。我们需要一个更好的策略。

此外，想想你知道的任何 REST APIs。您很可能已经注意到部分路径引用了一个 API 版本。比如说，`https://<organization>.atlassian.net/api/3/<path>`或者`https://api.appstoreconnect.apple.com/v1/<path>`。这在我们的应用程序中会是什么样子？

```
app.get("v1", "todo-lists") { ... }
app.post("v1", "todo-lists") { ... }
...
```

这看起来不太容易维护。幸运的是，Vapor 允许您“分组”路线和“注册”控制器。

让我们付诸行动吧。打开`routes.swift`，删除`routes(_:)`的内容，改为粘贴以下代码:

```
let v1Routes = app.grouped("v1")let todoListsRoutes = v1Routes.grouped("todo-lists")
try todoListsRoutes.register(collection: TodoListController())
```

这里发生了什么事？

*   我们将所有端点分组到`v1`路径下。这意味着针对`v1Routes`定义的每个端点路径都将在路径前面加上`v1`。例如，`/v1/todo-lists`
*   我们再次对包含`todo-lists`的一组路径进行分组。这很好，因为我们所有的 CRUD 操作都将`todo-lists`定义为它们的路径
*   最后，`TodoListController`被定义为负责子路径`/v1/todo-lists`的`class`

让我们看看`TodoListController`会是什么样子:

```
import Vaporfinal class TodoListController: RouteCollection {
  func boot(routes: RoutesBuilder) throws {
    // routing here
  }
}
```

一个`Controller`仅仅是一个`RouteCollection`，当启动时，它将我们希望允许的每个操作路由到一个特定的方法。让我们实现这个方法:

让我们来分解一下:

*   当`get`请求`/v1/todo-lists`时，调用`getAllEntities(req:)`
*   根据对`/v1/todo-lists`的`post`请求，调用`postEntity(req:)`
*   注意这两个方法是如何保存之前在`routes.swift`中的代码的

这很整洁，不是吗？比以前干净多了。现在，我们可以继续分组子路径，继续构建我们的应用程序。

注意:不要忘记现在你的请求应该以`v1`为前缀。就是`localhost:8080/todo-lists`不再起作用，而是，`localhost:8080/v1/todo-lists`。

# 参数和查询项

在继续前进并继续开发`TodoApp`之前，让我们先来看看如何改进我们的`GET operations`。首先，有没有办法检索某个特定的`TodoList`？在 REST 中，这将通过从`GET /todo-lists/<id>`获取来完成。也就是说，路径需要参数化。

此外，是否可以不检索`all(_:)`列表，而只检索一定数量的列表？也就是说，我们可以根据返回结果的数量来过滤列表吗？

## 路由参数

Vapor 让我们通过传递一个由`:`前置的字符串来参数化到一个端点的路径。例如:

```
app.get("users", ":userID") { ... }
```

将解析为`/users/<userID>`并将创建一个可从`request`对象中检索的`userID`。类似地，这可以在我们当前的路由结构中完成，我们只需要在`func routes(boot:)`中定义一个新的路由，并为它提供一个实现:

注意我们是如何通过调用`req.parameters.get(_:as:)`来获取参数的。如果一个`TodoList`不存在，将返回`HTTP 404`。

让我们测试这个新的端点。首先，让我们修改`POST /todo-lists`来返回创建的列表:

```
private func postEntity(req: Request) throws -> EventLoopFuture<Response> {
  let todoList = try req.content.decode(TodoList.self)
  return todoList.save(on: req.db)
    .map { todoList }
    .encodeResponse(status: .created, for: req)
}
```

最后，我们可以测试一下:

```
$ curl -X POST localhost:8080/v1/todo-lists \
    -H "Content-Type:application/json"
    --data "{ \"name\": \"Test my new endpoint\" }" | jq
{
  "id": "CF880521-A913-4977-8D84-4CC70EC2977D",
  "name": "Test my new endpoint"
}
$ curl localhost:8080/v1/todo-lists/8359807A-FCE3-48AA-8AC6-EFF619913900 | jq
{
  "id": "CF880521-A913-4977-8D84-4CC70EC2977D",
  "name": "Test my new endpoint"
}
```

## 询问

之前，`GET /todo-lists`会返回所有可用的`TodoList`。然而，这在将来会非常低效。想象一下，我们最终拥有成千上万的列表。

我们希望支持这个端点的分页，为此我们需要一种方法来访问请求中的`query-items` send。幸运的是，`Request`带有`subscript`方法，允许您检索预期的`query-item`。转到`func getAllEntities(req:)`，我们将其重命名为`getEntities`，因为我们不再返回每个实例。然后，将其修改如下:

注意我们如何在这里限制结果的`range(_:)`，然后检索匹配的`all(_:)`。让我们通过创建 20 个列表来尝试一下:

```
for i in $(seq 0 20); do
  curl localhost:8080/v1/todo-lists \
  -X POST \
  -H "Content-Type:application/json" \
  --data "{ \"name\": \"TODO List $i\" }"
done
```

并获取列表 10 至 14:

```
$ curl "localhost:8080/v1/todo-lists?offset=10&limit=5" | jq
[
  {
    "id": "77CD7685-1105-4E81-8A35-F775CEDDA28F",
    "name": "TODO List 10"
  },
  {...},
  {...},
  {...},
  {
    "id": "77CD7685-1105-4E81-8A35-F775CEDDA28F",
    "name": "TODO List 14"
  }
]
```

如果您想知道，越界(在我们的例子中是`offset=21`)将会变成一个空数组。

# 模型关系

## 定义待办事项

至此，你应该有了一个可以存储和返回`TodoList`的 web app。我们在实际列表中添加一些`Todo`怎么样？让我们从思考我们想要达到的目标开始。一个`TodoList`可以被视为一个包含`Todo` s 的文件夹；

```
├── my-todo-list
│   ├── todo-1
│   ├── todo-2
│   └── ...
```

在这里，您将看到以下内容:

*   没有`TodoList`就没有`Todo`
*   一个`TodoList`可以有任意数量的`Todo`

这就是所谓的`one-to-many`关系。为了告诉`Vapor`，或者实际上告诉 ORM `Fluent`，这两个模型是相关的，我们需要使用两个新的包装器:

*   `@Parent`将把一个`Todo`和一个`TodoList`联系起来
*   `@Children`将执行`TodoList`和`Todo`之间的等效操作

考虑到这些，我们可以将`Todo`定义为:

并更新`TodoList`以反映两个模型之间已建立的关系:

```
@Children(for: \.$list)
var todos: [Todo]
```

请注意，`list`是在`Todo`中定义的属性，它保存了对`TodoList`的引用。

接下来，我们需要在数据库中创建一个新表。如果您还记得第 1 部分，这是通过`Migration`完成的:

注意字段`description`是如何被定义为`String?`的，因此，它没有被标记为`.required`。对于`TodoList`，我们不需要任何额外的迁移。

最后，添加迁移到`configure.swift`:

```
app.migrations.add(CreateTodoListMigration(), to: .sqlite)
app.migrations.add(CreateTodoMigration(), to: .sqlite)
```

## 创建新的待办事项

我们的新模型已经准备好了，数据库也创建好了，但是我们还没有开始路由。我们希望定义看起来像`/todo-lists/<id>/todos`的新端点。如果您还记得，我们已经在`TodoListsController`中定义了一个端点`/todo-lists/<id>`和一组路由`singleListRoutes`。我们可以在此基础上继续建设。

```
// TodoListController
func boot(routes: RoutesBuilder) throws {
  ...
  let singleListRoutes = routes.grouped(":id")
  singleListRoutes.get(use: getEntity)
  let todosRoutes = singleListRoutes.grouped("todos")
  try todosRoutes.register(collection: TodoController())
}
```

而类似于`TodoListController`，我们可以将`TodoController`定义为:

让我们从实现`postEntity`开始:

```
guard let listID = req.parameters.get("id", as: UUID.self) else {
  throw Abort(.notFound)
}let todo = try req.content.decode(Todo.self)
todo.$list.id = listIDreturn todo.save(on: req.db)
  .map { todo }
  .encodeResponse(status: .created, for: req)
```

并尝试一下:

```
$ curl localhost:8080/v1/todo-lists \
    -X POST \
    -H "Content-Type:application/json" \
    --data "{ \"name\": \"Foo\" }"{"id":"9F5D21D3-11F3-434D-907E-2DA083404C3F","name":"Foo"}$ curl -X POST \
    "localhost:8080/v1/todo-lists/9F5D21D3-11F3-434D-907E-2DA083404C3F/todos" \
    -H "Content-Type:application/json" \
    --data "{ \"title\": \"My first TODO\" }"{"error":true,"reason":"Value of type 'TodoList' required for key 'list'."}**%**
```

注意错误:键`list`需要类型`TodoList`的值。不幸的是，Vapor 不会让你解码一个`Todo`，除非你通过一个`list`。有两种方法可以解决这个问题:

*   备选方案 1。删除`guard/else`、`todo.$list.id = listID`并在请求体中传递`listID`:

```
POST /v1/todo-lists/9F5D21D3-11F3-434D-907E-2DA083404C3F/todos
{
  "title": ...
  "list": {
    "id": "9F5D21D3-11F3-434D-907E-2DA083404C3F"
  }
}
```

*   选项 2。创建一个`struct`来解码信息(`title`、`description`)，从头开始创建一个新的`todo`，填充其字段并保存:

选项 2 比选项 1 更麻烦，所以我们选择选项 1。

现在，如果我们使用添加的字段再次重试相同的操作:

```
$ curl -X POST \
    "localhost:8080/v1/todo-lists/9F5D21D3-11F3-434D-907E-2DA083404C3F/todos" \
    -H "Content-Type:application/json" \
    --data "{ \"title\": \"My first TODO\", \"list\": { \"id\": \"9F5D21D3-11F3-434D-907E-2DA083404C3F\" } }"{"id":"1840A97A-631A-4B35-9381-BD2FC27A3FA5","title":"My First TODO","list":{"id":"9F5D21D3-11F3-434D-907E-2DA083404C3F"},"description":null}**%**
```

我们的`Todo`创造出来了！让我们通过获取父节点`TodoList`来检查一下:

```
curl -X GET "localhost:8080/v1/todo-lists/9F5D21D3-11F3-434D-907E-2DA083404C3F" | jq{
  "id": "9F5D21D3-11F3-434D-907E-2DA083404C3F",
  "name":"Foo"
}
```

哎呦！这份名单中似乎没有`Todo`…或者有吗？让我们检查一下`GET /v1/todo-lists/<id>`的实施情况:

```
let uuid = req.parameters.get("id", as: UUID.self)
return TodoList.find(uuid, on: req.db)
  .unwrap(orError: Abort(.notFound))
  .encodeResponse(for: req)
```

注意我们是如何试图`find` a `TodoList`的，但是我们没有加载/获取任何子节点！实现需要稍微改变:

```
let uuid = req.parameters.get("id", as: UUID.self)
return TodoList.find(uuid, on: req.db)
  .unwrap(orError: Abort(.notFound))
  .flatMap { list in
    list.$todos.load(on: req.db).map { list }
  }
  .encodeResponse(for: req)
```

如果我们现在再试一次:

```
curl -X GET "localhost:8080/v1/todo-lists/9F5D21D3-11F3-434D-907E-2DA083404C3F" | jq{
  "id": "9F5D21D3-11F3-434D-907E-2DA083404C3F",
  "name":"Foo",
  "todos": [
    {
      "id": "1840A97A-631A-4B35-9381-BD2FC27A3FA5",
      "title": "My First TODO",
      "list": {
        "id": "9F5D21D3-11F3-434D-907E-2DA083404C3F"
      },
      "description": null
    }
  ]
}
```

同样，`/v1/todo-lists`也需要更新。这一次我们可以使用`with`来创建连接查询:

```
return TodoList.query(on: req.db)
    .range(offset..<(limit + offset))
    .with(\.$todos)
    .all()
    .encodeResponse(for: req)
```

## 提取所有待办事项

我们在一个`TodoList`中获取所有的`Todo`怎么样？为此，我们需要通过一个`listID`来`query`所有的 todos 和`filter`。让我们这样做:

```
private func getEntities(req: Request) throws -> EventLoopFuture<Response> {
  guard let listID = req.parameters.get("id", as: UUID.self) else {
    throw Abort(.notFound)
  }
  return Todo.query(on: req.db)
    .filter(\.$list.$id == listID)
    .all()
    .encodeResponse(for: req)
}
```

# 结论

在本文中，我们在 TodoApp 的基础上构建了新的特性，并学习了构建 Vapor 项目的良好实践。我们还探索了模型关系的想法，以成功地将一个`Todo`关联到一个`TodoList`。

在本文结束时，您应该有一个功能性的 TodoApp。在最后一篇文章中，我将解释 Vapor 的更多高级特性，以将图像添加到`TodoList`中。

感谢阅读！

敬请关注。