# 模型分离:用可编码数据解析，用核心数据持久化

> 原文：<https://betterprogramming.pub/model-separation-parsing-with-codable-and-persisting-with-core-data-3803c0614176>

## 将核心数据中的*域模型*与*持久性模型*分开，以获得更好的可测试性

![](img/f9734433e97cb428a5b74c7e84788cc5.png)

迈克·肯尼利在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

距离我上一篇关于核心数据的文章已经有一段时间了。而且，正如经常发生的那样，我解决问题的方法也发生了变化(希望是更好的变化)。所以，我决定写下我改变了什么，为什么。

# 将领域模型与持久性模型分离开来

在过去两年中，我所做的主要改变是将*领域模型*与*持久性模型*分开。这一选择背后有几个原因。

首先，这有助于更好地分离关注点。*领域模型*由 API 定义，用 JSON 表示。这意味着我们可以只使用准系统结构来表示 API 返回的实体。相反，*持久模型*严重依赖于所选择的特定持久存储。在我们的例子中，选择的持久存储是核心数据，出于本文的目的，我们可以将其视为 SQLite 数据库之上的一个 [ORM](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping) 层。当然，我们希望确保在代表同一个实体的*域模型*和*持久性模型*之间有 1:1 的关系。

此外，这种分离允许我们在某种程度上独立地改变每种表示。例如，我们可以改变核心数据中持久化的实体的细节，而不必触及相应的*域模型*表示。这可能是有用的，例如，如果我们想为我们的*持久性模型*使用除 JSON 中的原始类型(`bool`、`number`、`string`)之外的特殊类型。然而，另一方面，由于需要遵循一些 API 更新，模型表示通常会发生变化。

模型分离的第二个重要优势是更好的可测试性。保持*域模型*与*持久性模型*的分离允许我们独立于持久性逻辑测试解析逻辑。

让我们看看如何将模型分离应用到我之前的[帖子](https://andrea-prearo.github.io/ios/mobile%20app%20development/swift/core%20data/codable/2018/05/07/Working-with-Codable-and-Core-Data.html)中的`[User](https://gist.github.com/andrea-prearo/5d9aa5c59ed80ef454d121825f4e17d0#file-coredatacodable-9-swift)` [类](https://gist.github.com/andrea-prearo/5d9aa5c59ed80ef454d121825f4e17d0#file-coredatacodable-9-swift)。

# 域模型处理:用户和代码

由于*域模型*将仅用于表示 API 实体，我们可以将其实现为一个简单的`Codable struct`:

# 持久性模型处理:UserManagedObject 和核心数据

因为我们将使用核心数据，所以*持久性模型*需要从`NSManagedObject`继承:

现在，由于只有几个有效的角色，我们可以通过将它们封装到一个`UserRole` enum 中来更容易地处理它们:

注意:使用核心数据特定功能可能有不同的方法来实现这一点。但我个人更喜欢用更程序化的方法来转变价值观。

# 跨域和持久性模型的转换

主要有两种情况需要我们跨*域*和*持久化*模型进行转换:

*   **从 API 中检索数据并将其持久化到核心数据中**:在这种情况下，我们将在解析 JSON 响应内容时创建*域*对象，然后将它们转换成相应的*持久化*对象(`User` - > `UserManagedObject`)
*   **上传核心数据更新到后端**:如果我们已经更新了*持久性*对象，我们可能需要通过 API(通常通过`PUT`调用)将这些更新传递到后端。这需要将一个或多个现有的*持久性*对象转换成它们对应的*域*对象，这些对象可以由 API ( `UserManagedObject` - > `User`)处理。

因为我总是发现通过一个接口来表达需求是很有用的，所以我决定创建一些协议来实现这个目的。

# 从域转换到持久性模型:ManagedObjectConvertible

为了表达能够从*域模型*转换到`*persistence*` *模型*的需求，我们可以依赖于`ManagedObjectConvertible`协议:

现在，要将一个`User`对象转换成它对应的`UserManagedObject`对象，我们只需要遵守`ManagedObjectConvertible`协议:

上面的代码只是创建适当类型的核心数据实体，并从当前的`User`对象中分配它的属性值。

关于`context` ( `NSManagedObjectContext`)参数有一个简单的说明:由于该方法正在创建一个新的核心数据对象，我们在调用它时使用的`context`应该是能够写入核心数据的适当上下文。

特别是，它必须支持从后台线程写入核心数据的主上下文。

# 从持久性到域模型的转换:ModelConvertible

与我们在前面的转换中看到的类似，为了表达能够从*持久性模型*转换到*域模型*的需求，我们可以依赖`ModelConvertible`协议:

要将一个`UserManagedObject`对象转换成其对应的`User`对象，我们只需要遵守`ModelConvertible`协议:

# 解析 JSON 响应并将用户存储在核心数据中

既然我们已经将*域模型*与*持久模型*完全分离，那么让我们看看如何重构现有的`[UserController](https://gist.github.com/andrea-prearo/c5bfc26cb927f0e7ad8c46e1cd7bda7b#file-coredatacodable-2-swift)`(它负责从 API 中检索用户的数据，并将其持久化到核心数据中)。所需的更改相当简单:

如前所述，我们只需要确保在调用`toManagedObject`方法时使用合适的上下文(支持从后台线程写入核心数据主上下文的上下文)。

# 结论

在这篇文章中，我展示了我最近在处理核心数据时将*域模型*与*持久性模型*分离的方法。这应该允许更好地分离关注点并增强可测试性。

更新后的代码可在 [GitHub](https://github.com/andrea-prearo/SwiftExamples/tree/master/CoreDataCodable) 上获得。

正如这篇文章中简要提到的，处理核心数据可能很棘手。尤其是当试图利用后台线程或队列与之交互时。在下一篇文章中，我将描述一些我发现对处理核心数据粗糙边缘有用的方法。