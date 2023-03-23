# 验证协议缓冲区中的零枚举值

> 原文：<https://betterprogramming.pub/validate-zero-enum-value-in-protocol-buffer-dbed0d9ecac4>

## *如何验证 Protobuf 消息不包含零值枚举字段？原来 Protobuf 并不直接支持这个功能！我们需要研究如何实现包* `*protojson*` *。*

![](img/ec3ea68bdbfbdd69699f5d9ffb910b50.png)

照片由 [Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

越来越多的公司采用带有 [Protobuf](https://developers.google.com/protocol-buffers) 的 gRPC 进行内部服务之间的通信。它的好处是高性能，支持多种编程语言，并得到谷歌的支持，周围有一个巨大的生态系统。

为了与前端和外部服务通信，Protobuf 可以被封送到 JSON 格式。浏览器只理解 JSON 格式，我们不能指望其他公司直接从我们这里消费 Protobuf。*(当然可以，如果你够大*[](https://github.com/googleapis/googleapis)**！)**

# *让我们谈谈 enum 以及我们最近在使用它时遇到的一些问题。*

**样本代码写在 Go 中。**

*从 Protobuf [样式指南](https://developers.google.com/protocol-buffers/docs/style#enums)中，零值枚举应该有后缀`UNSPECIFIED`。这是因为 enum 被实现为一个`uint32`，并且值`0`也被认为是未指定的。对于消息或空字符串，它类似于`nil`。将 Protobuf 编码为 JSON 时，会忽略`nil`消息、`UNSPECIFIED`枚举或空字符串。*

*我们一直在遵循这个惯例，直到有一天，我们没有。*

*当发送外部 webhook 消息时，我们决定不使用`0`作为`UNSPECIFIED`。一个原因是，我们使用`EmitUnpopulated: true`来确保在向外部发送 webhook 消息时，所有字段都包含在 JSON 表示中。如果我们忘记将 enum 字段设置为 0，我们不希望`UNSPECIFIED`值出现在 webhook 消息中。单元测试不能捕捉所有的错误；我们工程师知道这一点。*

*这导致了很多麻烦，所以我们不得不恢复并再次将值`0`设为`UNSPECIFIED`。一个问题是，它迫使到处使用`EmitUnpopulated: true`！有些地方，我们不想发射所有未填充的场。比如调用一些第三方 API。一些消息混杂在`UNSPECIFIED`枚举和非`UNSPECIFIED`枚举之间；没有办法发送正确的格式。用`EmitUnpopulated: true`，第三方 API 不懂`UNSPECIFIED`；使用`EmitUnpopulated: false`并省略一些非`UNSPECIFIED`枚举的必填字段。当然，它们都可以被重构掉，但在开始时强制使用`UNSPECIFIED`应该更简单。*

# *那么，为什么不验证 webhook 消息中的所有 enum 字段都没有设置为 0 呢？你可能会问。*

*原来在 Protobuf 3 中没有简单方法可以做到这一点！*

*在 Protobuf 2 中，有一个`[required](https://developers.google.com/protocol-buffers/docs/proto#specifying-rules)` [选项](https://developers.google.com/protocol-buffers/docs/proto#specifying-rules)来防止一个字段被取消设置。这个选项在 Protobuf 3 中被移除了，因为它阻止了移除字段的重构。如果我们忘记更新每个服务来删除不再使用的`required`字段，特别是在一个有多个团队一起工作的公司中，消息会被无意地丢弃。最好不要提前要求。( [*更有*](https://developers.google.com/protocol-buffers/docs/proto#specifying-rules) )*

*Protobuf 3 中有`[jsonpb.JSONPBMarshaler](https://pkg.go.dev/github.com/golang/protobuf/jsonpb#JSONPBMarshaler)` [接口](https://pkg.go.dev/github.com/golang/protobuf/jsonpb#JSONPBMarshaler)。我们可以简单地为所有枚举实现接口，以便在看到零值时返回错误。不过还是那句话，被撤了！作为一个协议，我们应该尽可能地减少定制。否则，定制将不得不在不同的团队中以不同的语言实现和维护！*

# *那么，如何验证枚举字段中的零值呢？*

*我们必须到达反射包。`[protoreflect.Message](https://pkg.go.dev/google.golang.org/protobuf/reflect/protoreflect#Message)`接口有`Range()`方法来迭代每个填充的字段。我们可以使用该方法来验证没有带有零的枚举字段…哦，等等。它只迭代*填充的字段*。所以它不会检测 enum 中的零值！*

*但是函数`[protojson.Marshal()](https://pkg.go.dev/google.golang.org/protobuf/encoding/protojson#Marshal)`仍然可以通过`[EmitUnpopulated](https://pkg.go.dev/google.golang.org/protobuf/encoding/protojson#MarshalOptions)` [选项](https://pkg.go.dev/google.golang.org/protobuf/encoding/protojson#MarshalOptions)发出未填充的字段。它是如何实现的？深入到`[encoding/protojson](https://pkg.go.dev/encoding/json)`，有一段代码片段用于迭代*未填充的字段* ( [*来源*](https://github.com/protocolbuffers/protobuf-go/blob/master/encoding/protojson/encode.go#L174-L197) )。我们去偷吧:*

*上面的代码所做的是通过循环遍历`protoreflect.Message.Descriptor().Fields()`来迭代额外的字段。跳过`oneof`字段内的字段。未填充的单数`message`字段在被发送到输入函数之前被设置为`invalid`(在生成的 JSON 中将它视为`null`)。*

*尽管如此，还有一些代码要写，比如实现一个遍历所有不同 Protobuf 类型的方法:消息、数组(重复)、动态[结构](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#google.protobuf.Struct)，当然还有枚举。但是是可以解决的。我现在可以休息了。*

*感谢阅读！*

```
***Want to Connect?**Also published at [my blog](https://olvrng.github.io/w/proto.enum/). Follow me on [Twitter](https://twitter.com/olvrng) for more.*
```