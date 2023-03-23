# 什么是 Protobuf，为什么要使用它

> 原文：<https://betterprogramming.pub/what-is-protobuf-and-why-you-should-use-it-14d52646f2a7>

## JSON vs 协议缓冲区。哪个更好？

![](img/45116eb96d11cf04a56529e4d5fcc96f.png)

卡罗琳娜·格拉博斯卡在[的照片](https://www.pexels.com/photo/notebook-mail-business-paper-5706001/)

如果您在任何 web 服务中编写过大量代码，那么您很可能听说过术语 JSON。

这是一种非常方便的数据格式，几乎随处可见。

然而，协议缓冲区这个术语近年来越来越受欢迎。许多人声称它在功能和灵活性方面超过了 JSON。

对我们许多人来说，协议缓冲区听起来令人生畏，也许一开始会令人困惑，但是深入研究之后，你会发现它非常方便。

今天就来说说协议缓冲吧！

# 什么是协议缓冲区？

![](img/c98535f88424eb4257021dbc9e70b2c3.png)

由[安德里亚·皮亚卡迪奥](https://www.pexels.com/@olly)在[像素](https://www.pexels.com/photo/pensive-ethnic-man-listening-to-answer-in-paper-cup-phone-3760607/)上拍摄

它从一个简单的问题开始。

想象一下，在 Python 程序中有一个对象，如何将它发送给另一台计算机上运行的 JavaScript 应用程序？

各种计算机系统中的不同语言可以不同地实现一个对象。这类似于不同的母语人士试图沟通。

因此，我们需要一种标准的、平台中立的数据格式。简而言之，共同语言。

两个应用程序同意相同的格式和编码类型。发送方以预定的格式序列化数据，而接收方相应地反序列化和解析数据。

JSON 和协议缓冲就是这样！

这是一种标准的、平台无关的数据格式，简化了应用程序之间的数据传输。

# 为什么是协议缓冲区？

如果 JSON 做得这么好，为什么还要协议缓冲？

JSON 虽然方便，但也有几个缺点，

1.  这是一种无模式的数据格式
2.  这是一种基于文本的编码数据格式，会产生更大的数据量
3.  它没有在模式级别进行任何验证

**无模式数据格式**。数据可以以任何形式发送，使用任何键-值对。由接收者来验证数据。简而言之，对数据的发送方式没有限制。

**基于文本的编码数据格式**。数据在发送出去之前被转换成文本。例如，尽管本质上是相似的，但下面的数据在编码成字符串时消耗的字节数是不同的。

```
 json1 = {
   "my_age_this_year": 4
}

json2 = {
   "age": 4
}//json1 ~= 23 bytes
//json2 ~= 10 bytes
```

**缺乏验证**。由于 JSON 是无模式的，所以数据验证只能在代码级别完成。

这就是为什么协议缓冲区来救援！

# 关键特征

协议缓冲区具有各种特性。在这篇文章中，我将重点介绍在您的日常应用中很重要的四个基本要素。

*   原型文件
*   原始编译程序
*   Protobuf 对象的序列化
*   二进制编码

## 原型文件

![](img/9970c22aece795d4be04aa572601d9e9.png)

照片由 [Pavel Danilyuk](https://www.pexels.com/@pavel-danilyuk) 拍摄于 [Pexels](https://www.pexels.com/photo/close-up-photo-of-people-shaking-hands-8112172/)

为了促进数据传输，应用程序需要同意标准的模式和格式，这就是`.proto`文件。

被称为`message`的数据/对象被定义并存储在`.proto`文件中。然后文件被放在客户端和服务器端。

例如，为了在客户机和服务器之间发送一个用户对象，我们以如下格式在一个`.proto`文件中定义了一个`User message`

```
syntax = "proto3";

package user;

message User {
	string name = 1;
	int32 age = 2;
	int32 height = 3;
	repeated Pet pets = 4;
}

message Pet {
	string name = 1;
	string sound = 2;
} 
```

`.proto`文件独立于您使用的平台和语言。

`User`消息指定了四个键值对。每个都包含

*   字段类型- `string` / `int32` /...
*   键名- `name` / `age` /...
*   字段编号- `1` / `2` /...

字段类型定义值类型，而术语`repeated`指定字段是相似类型的数组。

字段编号作为二进制编码中的唯一标签,这也是 Protobuf 较小的原因，我们将在后面详细讨论。

客户机和服务器都利用预定的`message`类型来序列化和去序列化数据，给 ProtoBuf 一个明确的模式。

## 协议

![](img/ad521a6c9aee63b6d9d60838540f6a11.png)

照片由[马体·米罗什尼琴科](https://www.pexels.com/@tima-miroshnichenko)在[像素](https://www.pexels.com/photo/man-person-people-industry-5846247/)上拍摄

当您创建第一个`.proto`文件时，随之而来的问题是，如何在您的应用程序中应用它？

这就是协议编译器(又名 Protocol)发挥作用的地方！

用户在一个`.proto`文件中定义消息，协议编译器将它编译成你正在使用的任何语言的类

通过下面的命令，协议编译器在`$DST_DIR`中生成一个`user_pb2.py`文件。

```
protoc -I=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/user.proto
```

*   $SRC_DIR:源代码所在的目录
*   $DST_DIR:生成的`_pb2.py`文件所在的目录
*   $SRC_DIR:原始`user.proto`文件所在的目录
*   —python_out:用于指定所用语言的选项

```
....
_USER = _descriptor.Descriptor(
name='User',
full_name='user.User',
filename=None,
file=DESCRIPTOR,
containing_type=None,
create_key=_descriptor._internal_create_key,
....
```

`user_pb2.py`文件的内容几乎不可读，但这无关紧要。您所需要关心的就是在您的应用程序中导入和应用这些类。

## Protobuf 对象的序列化

![](img/e466f399b4931a4b6508127295cf459f.png)

照片由 [cottonbro](https://www.pexels.com/@cottonbro) 在[像素](https://www.pexels.com/photo/photo-of-person-taking-down-notes-7319070/)上拍摄

让我们尝试使用我们的`user_pb2.py`文件中生成的类。

```
from pb.proto import user_pb2user = user_pb2.User()
user.name = "Jason"
user.age = 20
user.height = 180pet1 = user.pets.add()
pet1.name = "Dog"
pet1.sound = "Bark"# Attribute error
user.friend = "Peter"# Type error
user.age = "twenty"protoString = user.SerializeToString()parsedUserFromStr = user_pb2.User()
parsedUserFromStr.ParseFromString(protoString)print(protoString)
# b'\n\x05Jason\x10\x14\x18\xb4\x01"\x0b\n\x03Dog\x12\x04Bark'print(parsedUserFromStr)
# name: "Jason"
# age: 20
# height: 180
# pets {
#   name: "Dog"
#   sound: "Bark"
# }
```

注意，数据赋值**不仅仅是将任意字段**添加到典型的 Python 对象中。

如果你分配了一个没有在`.proto`文件中声明的字段或者一个与定义的值类型不兼容的值，将会产生一个错误。

通过生成的类的内置方法，您可以将数据序列化为一个字符串，并将其发送到指定的应用程序。

值得强调的一点是序列化的字符串是二进制的**，而不是文本**。`str`只是作为一个方便的容器来传输数据。

## 二进制编码

![](img/17c495fdb718750a6008df9172c17c0e.png)

Miguel A. Padrinan 在 [Pexels](https://www.pexels.com/photo/bike-chain-forming-1-and-0-1061133/) 拍摄的照片

这就是为什么协议缓冲区比 JSON 对象更紧凑的原因。

让我们来看一个典型的 JSON 对象。

```
{
	"name": "Jason",
	"money": 300
}# jsonString = '{"name": "Jason", "money": 300}'
```

发送方必须向客户端提供密钥和值。

JSON 使用基于文本的编码，这意味着一个对象在发送出去之前被转换成一个文本字符串，数据大小随着键中字符数的增加而增加。

另一方面，协议缓冲区的工作方式不同。

```
message Person {
	string name = 1;
	int money = 2;
}
```

**它用一个唯一的标签号**代替了钥匙。上面显示的类似对象将在协议缓冲区中编码如下。

```
125Jason20300
```

`1`代表字段号，`2`是[线类型](https://developers.google.com/protocol-buffers/docs/encoding#structure)(值类型)`5`是指字符串的长度。注意，只有线类型`2`需要输入数据长度。

同样，字符串中的第二个`2`是指字段编号，`0`是导线类型，`300`是指数据。

通过用一个唯一的字段号替换关键字，减少了总的数据量。

这意味着`.proto`文件中字段的顺序并不重要。接收方根据字段编号解码数据。例如，接收方将`Jason`分配给字段编号`1`，而不考虑`.proto`文件中的字段顺序。

但是，更改字段编号是灾难性的。如果发送方将`name`的字段号改为`2`，数据接收方将尝试将`Jason`赋值给变量`money`而不是`name`。

此外，由于 JSON 使用基于文本的编码，一个整数`300`携带三个字符，因此消耗三个字节。

```
00000011 00000000 00000000
```

另一方面，协议缓冲区使用二进制编码，将`300`编码到一个`varint`中，而不是消耗 2 个字节。

```
00000001 00101100
```

当然，这是编码字节串的简化版本，与实际版本没有任何相似之处。但是希望这能给你一个为什么协议缓冲区比 JSON 字符串小的要点。

# 优势

![](img/09b9b6fa656c06c304f638716bae255c.png)

由[在](https://www.pexels.com/@cottonbro)[像素](https://www.pexels.com/photo/woman-in-white-and-black-striped-long-sleeve-shirt-and-red-knit-cap-4715329/)上拍摄的照片

在结束本文之前，让我们强调一下协议缓冲区的一些主要优点。

*   尺寸较小
*   向后兼容性
*   (计划或理论的)纲要
*   确认

## 尺寸较小

如前所述，与 JSON 中基于文本的编码相比，Protocol Buffer 使用二进制编码，并且它用一个唯一的字段号来替换密钥。

因此，与 JSON 相比，它消耗的字节数要少得多。

## 向后兼容性

通过利用唯一的字段号，协议缓冲区提供了极好的向后兼容性。

用户可以在发送方/接收方方便地添加或删除字段，而不用担心兼容性问题。当接收者解码数据时，它将

*   如果接收方没有发送的字段编号，则忽略
*   如果所需的字段编号在传输的数据中不可用，则分配默认值

## (计划或理论的)纲要

协议缓冲区为发送方和接收方提供了明确的模式。它允许开发人员准确地知道从消息中可以期待什么。

## 数据有效性

协议缓冲区在编码和解码过程中验证给定数据的值类型，确保数据传输过程中的数据完整性。

# 结论

![](img/c8362b2386a6a690ef907e20b23bfc44.png)

照片由[赖爷苏比扬托](https://www.pexels.com/@ketut-subiyanto)在[像素](https://www.pexels.com/photo/crop-coffee-house-worker-serving-cup-of-freshly-brewed-espresso-4353573/)上拍摄

关于协议缓冲区就是这样！

尽管本文省略了大量的细节和技术问题，但我希望我已经向您简要介绍了什么是协议缓冲区以及为什么每个人都在使用它！

就这样，我们今天到此为止。下次见，再见！

# 参考

*   [Arun Mathew Kurian 的《了解协议缓冲区》](/understanding-protocol-buffers-43c5bced0d47)
*   [Yash Suresh Chandra 的 Protobuf 编码内部视图](https://medium.com/@yashschandra/an-inner-view-to-protobuf-encoding-e668f37847d5)
*   [Matthias Doring 的 Python 基本 Protobuf 指南](https://www.datascienceblog.net/post/programming/essential-protobuf-guide-python/)