# 了解协议缓冲区

> 原文：<https://betterprogramming.pub/understanding-protocol-buffers-43c5bced0d47>

## 对原蟾蜍的深入研究

![](img/77661c5b6b6b49652c7f6767dffa79e0.png)

在之前的[文章](https://medium.com/better-programming/understanding-grpc-60737b23e79e)中，我们讨论了与 gRPC 框架相关的各种概念。在那里，我简要地提到了协议缓冲区，这是 gRPC 中序列化结构化数据的默认方法。在本文中，我们将深入研究协议缓冲区的概念。

# 什么是协议缓冲区？

协议缓冲区是一种序列化数据的方法，这些数据可以通过网络传输或存储在文件中。JSON 和 XML 等其他格式也用于序列化数据。虽然这些平台已经证明了自己非常灵活和有效，但它们没有完全优化的一个地方是数据要以平台中立的方式在多个微服务之间传输的场景。

这就是促使谷歌在 2008 年创建 ProtoBuf 格式的挑战。从那以后，它在 Google 内部被广泛使用，并且成为 gRPC 框架的默认数据格式。最初，Protobuf 是为三种主要语言创建的——c++、Java 和 Python。多年来，许多语言如 Go、Ruby、JS、PHP、C#和 Objective-C 也开始支持 Protobufs。Protobuf 的当前版本被称为 proto3。

像 JSON 和 XML 一样，Protobufs 是语言和平台中立的。Protobuf 被优化为比 JSON 和 XML 更快，它去掉了许多通常由数据格式完成的职责，只关注尽可能快地序列化和反序列化数据的能力。另一个重要的优化是通过使传输的数据尽可能小，来考虑使用了多少网络带宽。

要序列化的数据的定义写在称为 proto 文件(`.proto`)的配置文件中。这些文件将包含被称为消息的配置。原型文件可以被编译以生成用户编程语言的代码。

让我们详细看看 Protobufs 的关键特性。

# 关键特征

## **二进制传输格式**

Protobuf 是一种二进制传输格式，这意味着数据是以二进制形式传输的。这比原始字符串更能提高传输速度，因为它占用的空间和带宽更少。由于数据被压缩，CPU 的使用也将减少。

唯一的缺点是 Protobuf 文件或数据不像 JSON 或 XML 那样易读

如果平台不像富客户端那样支持二进制消息，Protobuf 中有将二进制数据序列化为字符串的功能。

## **上下文和数据的分离**

在 JSON 和 XML 中，数据和上下文是分开的——而在 Protobuf 中，数据和上下文是分开的。考虑一个 JSON 例子。

```
{
  first_name: "Arun",
  last_name: "Kurian"
}
```

在这个例子中，传输的数据得到了一个具有两个属性的对象文本，`first_name`和`last_name`，值为`Arun`和`Kurian`。这是高度可读的，但这会占用更多的空间。这里，每个 JSON 消息每次都必须提供这两部分。随着我们数据的增长，传输时间会显著增加。

但是对于 Protobufs 来说，事情就不一样了。我们首先在配置文件中定义一条消息，如下所示:

```
{
  string first_name = 1;
  string last_name = 2;  
}
```

该配置文件包含上下文信息。数字只是字段的标识符。如果消息格式有点混乱，不要担心——我们稍后将详细研究它。通过使用这种配置，我们可以将编码数据作为“

```
124Arun226Kurian
```

在`124Arun`的情况下，`1`代表字段标识符，`2`代表数据类型(即字符串)，而`4`是文本的长度。我承认这比 JSON 更难阅读；然而，与 JSON 数据相比，这将占用很少的空间。

# 消息格式

正如我们之前看到的，数据是基于一种称为*消息的配置作为 Protobuf 传输的。*消息保存在`.proto`文件中。让我们看一个消息示例:

```
syntax = "proto3";message Person {
  uint64 id = 1;
  string email = 2;
  bool is_active = 3; enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  } message PhoneNumber {
    string number = 1;
    PhoneType type = 2;
   }

  repeated PhoneNumber phones = 4;}
```

从上面的例子中，我们可以看到消息是用消息关键字声明的，后跟用户定义的消息名。文字或组件在花括号中声明。每个文字字段可以分为四个部分。它们是:

## 田赛规则

在 Protobuf 的 proto2 版本中，有类似`required`、`optional`和`repeated`的规则被添加到字段类型或数据类型之前。这在 proto3 中得到了优化，只保留了`repeated`规则。如果一个字段表示一组相同类型的元素，那么这个字段就是重复的。如果字段不重复，则不应添加任何规则。

## **字段类型**

一个字段可以包含的数据类型分为三类。

第一种是标量数据类型，如字符串和数字。第二个是一个`enum`数据类型。在我们的例子中，这是`PhoneType`。最后的数据类型是嵌入的消息(就像我们例子中的`PhoneNumber`)。

像在 JSON 和 XML 中一样，当使用消息类型时，我们可以构建消息的层次结构来表示任何类型的数据。Protobuf 中可用的标量数据类型有`float`、`int32`、`int64`、`uint32`、`uint64`、`sint32`、`sint64`、`fixed32`、`fixed64`、`sfixed32`、`sfixed6`、`bool`、`string`和`bytes`。

## **字段名称**

当命名 Protobuf 中的字段时，有一些约定需要遵循，因为这些约定是 Protoc 编译器在为您选择的语言生成基于`.proto`文件的代码时假定的。第一个约定是字段名都应该是小写的。其次，如果字段名中有多个单词，它们应该用下划线隔开。

## **字段标签**

字段标记是字段的数字表示，这使我们能够在定义中拥有丰富的字段名称，而无需通过网络发送它们。

使用字段标签时，需要考虑一些事情。首先，字段标签在消息中必须是唯一的。其次，它们必须是整数。第三，如果一个字段要从已经使用的定义中删除，它的标签必须声明为`reserved`以防止它被重新定义。示例:`reserved 8;`

# 从原型文件生成代码

Protobuf 最重要的组件是协议编译器。协议的安装如下:

## **针对 Mac 用户**

```
brew tap homebrew/versions
brew install protobuf
```

## **针对 Ubuntu 用户**

```
sudo apt install protobuf-compiler
```

键入“,确保编译器是最新的

```
protoc --version
```

向不同语言解释 protoc 从`.proto`文件生成代码的方式确实很有挑战性，因为对于不同的生成代码，语法会有所不同。所以我将只为 JavaScript 生成代码。我建议每个人参考你最喜欢的语言的官方文档。

首先，让我们创建一个小的`.proto`文件。

## **src/person.proto**

```
syntax = "proto3";
message Person {
  uint64 id = 1;
  string email = 2;
}
```

让我们为这个`.proto`生成 JavaScript 代码。为此，请在终端中键入以下命令:

```
protoc --proto_path=src --js_out=js_out_folder src/person.proto
```

第一个参数— `proto_path` — 是保存`.proto`文件的地方。`js_out=js_out_folder`表示我们需要输出 JS 到用户自定义文件夹`js_out_folder`。最后一个参数是`.proto`文件的路径。

这将在`js_out_folde` r 中生成一个名为`person.js`的 JS 文件，生成的文件会相当大，所以我不在这里粘贴整个文件。整个文件可以在这个[链接](https://gist.github.com/amkurian/e59b2a8f8f0bde14263ae41ef4acae81)中找到。文件的重要部分是:

```
proto.Person = function(opt_data) {
  jspb.Message.initialize(this, opt_data, 0, -1, null, null);
};
goog.inherits(proto.Person, jspb.Message);proto.Person.prototype.getId = function() {
  return /** [@type](http://twitter.com/type) {number} */ (jspb.Message.getFieldWithDefault(this, 1, 0));
};proto.Person.prototype.setId = function(value) {
  return jspb.Message.setProto3IntField(this, 1, value);
};proto.Person.prototype.getEmail = function() {
  return /** [@type](http://twitter.com/type) {string} */ (jspb.Message.getFieldWithDefault(this, 2, ""));
};proto.Person.prototype.setEmail = function(value) {
  return jspb.Message.setProto3StringField(this, 2, value);
};
```

我们可以看到创建了一个类`Person`，并且为字段`id`和`email`定义了 getters 和 setters。

根据语言的不同，语法会有所不同。

# 结论

我们已经看到，协议缓冲区的设计比传统的数据传输格式(如 JSON 或 XML)更高效、更快。数据压缩可以提高速度，减少 CPU 的使用。

我不能说 Protobuf 是 JSON 的简单替代品，但它解决了 JSON 的许多问题，尤其是在使用微服务架构时。

一个代价是数据的可读性大大降低。与优点相比，这算不上什么大缺点。

JSON 和 Protobuf 的速度对比已经[可用](https://auth0.com/blog/beating-json-performance-with-protobuf/)。这是一项正在发展的技术，我建议每个人查看官方的[文档](https://developers.google.com/protocol-buffers)和[库](https://github.com/protocolbuffers/protobuf)。