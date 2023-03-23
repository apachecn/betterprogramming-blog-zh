# 无服务器 API Essentials:发布与修补

> 原文：<https://betterprogramming.pub/serverless-api-essentials-put-vs-patch-36235a36e88e>

## 知道何时用 PUT 或补丁进行更新可能很棘手。选择使用哪些无服务器资源是您的下一个障碍

![](img/2ca434eea827345550c0c5e50f247f09.png)

图片来自 Freepik 上的[故事集](https://www.freepik.com/free-vector/teacher-concept-illustration_8449776.htm#query=teacher&position=39&from_view=search)

我进行了大量的开发者访谈。我在采访中最喜欢的话题之一是 REST APIs。总的来说，我对 API 设计和 [API 开发](/are-you-really-api-first-or-do-you-just-think-it-24ffc47ec7ea)充满热情。所以很自然，这是我经常问的问题。

我们涵盖了基础知识，比如端点是如何构造的，以及如何平衡基本的 REST 构造和开发人员的经验。但是，当我要求解释 [HTTP 方法](https://doc.oroinc.com/api/http-methods)的区别以及何时使用一种方法而不是另一种方法时，比你想象的更容易出错。

大多数人能清楚地说出何时使用 POST、GET、PUT 和 DELETE。但当我问及帕奇时，我经常会有一种“车灯前的鹿”的表情。

有时人们告诉我他们以前从未听说过它；其他时候，他们告诉我他们知道这是一个更新操作，但不确定修补和上传之间的区别。

这两种操作之间最简单的区别是:

> PUT 用请求有效负载替换整个资源，但是 PATCH 对资源应用部分更新。

根据您使用 PUT 或 PATCH 的决定，当涉及到无服务器时，您的下游实现可能会发生巨大的变化。因此，让我们讨论一下什么时候您会使用其中的一个，并讨论无服务器资源的实现。

# 何时使用 PUT 操作

如前所述，PUT 将用请求负载替换整个资源。这意味着当您进行调用时，所有内容都将被覆盖，从而使该操作成为一般的[等幂](/serverless-api-essentials-idempotency-e753b7b49680)。

任何需要幂等且需要提供完整资源数据的操作都应该由 PUT 操作支持。例如，如果我们从我的参考项目 [Gopher Holes Unlimited](https://github.com/allenheltondev/gopher-holes-unlimited) 中获取 [update gopher hole](https://github.com/allenheltondev/gopher-holes-unlimited/blob/master/openapi.yaml#L670) 端点，您可以看到我们覆盖了整个 gopher hole 资源。

该资源具有有限的字段，并且没有一个字段是通过访问控制来限制的(稍后将详细介绍)。因此，我们有了一个完美的看跌期权用例。要求调用者提供 gopher hole 资源的所有细节，甚至是不变的值，并不是一个过多的负担。

当涉及到在无服务器系统中实现 PUT 时，您可以通过几种不同的方式来实现。如果您不需要执行验证(在模式验证之外),只需要替换实体，您可以通过 API 网关直接连接 DynamoDB 来替换资源。

下面是一个[开放 API 规范](https://www.openapis.org/)中的示例实现:

这个简单的操作不需要任何 Lambda 函数。API Gateway 会直接调用 DynamoDB，替换所请求资源的数据，假设资源已经存在。

如果资源不存在，也允许 PUT 操作[创建资源](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/PUT#responses)。它将资源的 id 设置为路径中提供的 id。但是因为我们讨论的是更新操作，如果资源不存在，这个端点将返回一个错误。

# 何时使用修补操作

如果一个资源的元素处于访问控制级别之下，或者它太大而无法让使用者在所有字段中传递，那么您正在寻找一个修补程序的最佳机会。

当我提到访问控制时，我指的是需要一定级别的授权才能查看或编辑的资源元素。例如，这些字段只有资源的创建者才能操作。在 Gopher Holes Unlimited 中，有一个`[PATCH /gophers/{gopherId}](https://github.com/allenheltondev/gopher-holes-unlimited/blob/master/openapi.yaml#L231)`端点，允许调用者通过请求模式只更新 Gopher 信息的一个子集。

在下游实现中，进行验证以确保只有添加 gopher 的人才能更改名称。API 的其他消费者可以更改其他字段，如他们看到地鼠的次数或颜色，但不能更改关键的标识信息。

由于内容的额外验证，补丁是这种情况下的首选途径。要在 Node.js Lambda 函数中实现通用补丁机制，请参考本要点中的代码[。](https://gist.github.com/allenheltondev/ccf585a1470e6be200f3ef970187e636)

与 PUT 操作相反，当谈到无服务器实现时，补丁将在 Dynamo 中执行一个`UpdateItem`调用。这个`[UpdateItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html)`调用将添加、更新或删除一个条目的特定属性，而不是像我们在 PUT 端点中使用的`[PutItem](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)`调用那样进行完全替换。

注意——当使用类似上面要点中的通用补丁机制时，您必须依赖 API 网关级别的模式验证。您应该将传入的模式定义为[，将潜在的更改限制在](https://gist.github.com/allenheltondev/ccf585a1470e6be200f3ef970187e636#file-openapi-yaml-L49)可变的字段。确保在使用泛型时不允许任何额外的属性，因为您可能会存储恶意用户传入的无限数据。

关于实现补丁端点，有两种观点:

*   像 POST 或 PUT 一样，在请求有效负载中提供所有更新的属性

```
{ 
  "location": { 
    "longitude": 24.554 
  }, 
  "comment": "This is a nasty gopher" 
}
```

*   以数组形式提供每个变化值的操作、属性路径和值

```
[
  {
    "op": "add",
    "path": "/comment",
    "value": "This is a nasty gopher"
  },
  {
    "op": "replace",
    "path": "/location/longitude",
    "value": 24.554
  },
  {
    "op": "remove",
    "path": "color"
  }
]
```

和大多数软件一样，这些实现都有一些折衷。第一种实现更为熟悉，并且在使用 API 时通常成本更低。但是，它不提供删除属性的功能。

第一个实现只是一个添加或更新操作。一种解决方法是删除作为空值传递到请求中的属性，但这可能不是 100%正确的。

另一个实现需要传入要更改的每个字段的 op、path 和值。这导致显著更灵活的设计，但是增加了消费者跟踪在执行数据输入时发生的操作的复杂性。

这两种实现在各自的场景中都是完全有效的。但是请确保在[执行您的初始 API 设计](/going-api-first-your-first-30-days-4c1ef6da320d)时做出一个有意识的决定。

# 摘要

资源更新是一次冒险。在决定使用哪种实现时，您有很多选项可以考虑。

API 设计和无服务器的伟大之处在于，您不必为整个应用程序坚持一个决定。也许您的端点的子集作为 PUT 操作运行是有意义的，而其他端点更适合于补丁。

在您的修补操作中，可能有些端点应该只允许更新，从不删除字段，而其他端点可能需要删除字段的能力。这完全取决于您的用例。

您的无服务器基础设施决策也各不相同。如果您的更新是一个没有副作用的简单 PUT，您可以从 API Gateway 直接进入 DynamoDB。如果在进行更改之前需要运行验证，Lambda 函数更适合您的需要。如果您有多个验证和由于变更而触发的副作用，那么[步骤功能工作流](/how-to-build-lightning-fast-apis-with-aws-step-functions-d1725624aaaa)可能是您的最佳选择。

请记住，看跌期权或补丁不是灵丹妙药。将大型端点分解成较小的端点可能更明智。

与其通过一个`PUT /gophers/{gopherId}`端点来更新 gopher 状态，不如实现一个`POST /gophers/{gopherId}/statuses`来分隔更新特定字段时需要运行的副作用或业务逻辑。

这就是 API 设计的美妙之处。这是原教旨主义和完美开发者体验之间的平衡。用最有意义的东西让你的消费者开心。

编码快乐！