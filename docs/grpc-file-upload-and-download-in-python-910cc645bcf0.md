# 用 Python 上传和下载 gRPC 文件

> 原文：<https://betterprogramming.pub/grpc-file-upload-and-download-in-python-910cc645bcf0>

## 通过流式终端传输文件

![](img/bec94d4f511a22ad9c58dfa3915df426.png)

约翰·汤纳在 [Unsplash](https://unsplash.com/s/photos/connect?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

通过阅读这篇文章，您将学习如何设置自己的 gRPC 客户端和服务器，以便用 Python 上传/下载文件。供您参考，gRPC 被称为远程过程调用，是一个现代的开放源码，用于将设备、移动应用程序和浏览器连接到后端服务。

其核心具有以下特性:

*   `simple service definition` —通过协议缓冲区定义，一个强大的二进制序列化工具
*   `scalable` —扩展到每秒百万次 RPC
*   `multi-platform advantage` —跨不同语言和平台工作
*   `streaming support` —支持客户端和服务器之间的双向流传输

本教程涵盖以下概念和实现:

*   安装 Python 模块和 gRPC 工具
*   通过协议缓冲区定义服务
*   生成 gRPC 代码
*   建立一个 gRPC 服务器用于文件上传和下载
*   构建一个 gRPC 客户端从服务器上传和下载文件
*   更新流媒体服务
*   更新 gRPC 服务器以上传/下载文件
*   更新 gRPC 客户端以上传/下载文件

让我们继续下一节的设置和安装

# 设置

强烈建议您在继续安装之前设置一个虚拟环境。

## gRPC

在您的终端上运行以下命令来安装`grpcio`:

```
pip install grpcio
```

## gRPC 工具

除此之外，我们需要安装协议缓冲编译器附带的 gRPC 工具来生成服务器和客户端代码。您可以按如下方式安装它:

```
pip install grpcio-tools
```

# 定义服务

在您的工作目录中创建一个名为`protos`的新文件夹。然后，创建一个名为`hello.proto`的新文件。该文件充当整个项目的服务定义(适用于客户机和服务器)。

我们将定义数据序列化的结构。

*   `message` —表示包含一系列名称-值对的信息的逻辑记录。每个项目通常被称为字段。您必须定义字段类型、字段名称和字段编号。字段编号用作唯一标识符，不应更改。它从 1 开始，1 到 15 范围内的数字用一个字节进行编码。另一方面，16 到 2047 范围内的数字需要两个字节来编码。

看看下面的例子:

```
message HelloRequest {
  string name = 1;
  int32 age = 2;
}message StringResponse {
  string message = 1;
}
```

`HelloRequest`消息包含一个字符串和 int32 变量。因为没有限制，所以您可以将它用于请求或响应。在这种情况下，我们将使用它作为请求，这意味着传入的请求应该有一个 string 类型的`name`变量和一个 int32 类型的`age`变量。

另一方面，StringResponse 消息只包含一个消息变量。因此，我们的 gRPC 服务将接受两个输入变量(string，int32)并返回一个变量(string)作为响应。

*   `service` —代表服务定义。每个服务可以有多个 rpc，它们有自己的请求和响应定义。

下面的代码片段演示了一个简单的服务定义，它利用了来自

```
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (StringResponse) {}
}
```

`Greeter`服务包含一个接受`HelloRequest`消息并返回`StringResponse`消息的`SayHello` rpc。

> 协议缓冲区定义是通用的，与您使用的平台和编程语言无关。有关协议缓冲区的更多信息，请查看[语言指南(proto3)](https://developers.google.com/protocol-buffers/docs/proto3) 。

看看下面的原型文件作为参考:

# 生成 gRPC 代码

下一步是生成相应的 Python 代码，供客户机和服务器使用。假设您的工作目录如下:

```
root (working directory)
|
|--protos
|  |-hello.proto
|--client.py (client code, will be created in the later section)
|--server.py (server code, will be created in the later section)
```

从您的工作目录中调用以下命令:

```
python -m grpc_tools.protoc -I=. --python_out=. --grpc_python_out=. ./protos/hello.proto
```

它将在 protos 文件夹中生成以下文件:

*   `hello_pb2.py`
*   `hello_pb2_grpc.py`

> 文件的前缀取决于*的名称。原型文件。每当对原型文件进行修改时，都需要重新生成 gRPC 代码。

# gRPC 服务器

在您的工作目录中创建一个名为`server.py`的新文件。

## 导入

打开它并添加以下导入语句:

```
from concurrent import futures
import logging
import os
import grpc
from protos import hello_pb2, hello_pb2_grpc
```

## 类别定义

然后，用名为`SayHello`的函数定义`Greeter`类:

```
class Greeter(hello_pb2_grpc.GreeterServicer):
    def SayHello(self, request, context):
        return hello_pb2.StringResponse(message=f'Hello, {request.name}! Your age is {request.age}')
```

在函数内部，您可以实现所需的逻辑并将`StringResponse`消息返回给客户端。

> 函数名应该与我们之前在`proto`文件中定义的相匹配。

## 主要功能

之后，定义一个名为 serve 的新函数，并在其中添加以下代码:

```
def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=4))
    hello_pb2_grpc.add_GreeterServicer_to_server(Greeter(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()
```

它将使用端口 50051 上的 4 个工作线程运行服务器。

在文件底部添加以下代码作为最后一笔:

```
if __name__ == '__main__':
    logging.basicConfig()
    serve()
```

您可以在以下网址找到完整的服务器代码:

在您的工作目录中运行以下命令:

```
python server.py
```

# gRPC 客户端

完成服务器代码后，创建一个名为`client.py`的新 Python 文件。

## 导入

在文件顶部添加以下导入语句:

```
import logging
import os
import grpc
from protos import hello_pb2, hello_pb2_grpc
```

## 主要功能

继续添加一个名为`run`的新函数，代码如下:

```
def run():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = hello_pb2_grpc.GreeterStub(channel)
        response = stub.SayHello(hello_pb2.HelloRequest(name='John Doe', age=30))
        print("Greeter client received: " + response.message)
```

它将在端口 50051 连接到 gRPC 服务器，并使用以下输入调用 SayHello rpc:

*   `name` —无名氏
*   `age` — 30

在`client.py`脚本的末尾添加以下代码:

```
if __name__ == '__main__':
    logging.basicConfig()
    run()
```

请看下面的代码片段作为参考:

确保您当前正在运行`server.py`。打开一个新终端并运行以下命令:

```
python client.py
```

您应该会看到以下输出:

```
Greeter client received: Hello, John Doe! Your age is 30
```

# 更新流媒体服务

对于文件响应，最好的方法是通过流返回字节。您可以按如下方式定义消息:

```
message FileResponse {
  bytes chunk_data = 1;
}
```

至于文件请求，在使用它作为消息的定制字段类型之前，首先创建一个`MetaData`消息是一个好主意。

```
message MetaData {
  string filename = 1;
  string extension = 2;
}
```

这允许您传递图像/音频/视频/文件数据以及其他重要信息。由于流式传输将在文件结束之前被间隔调用，我们可以避免通过`oneof`关键字发送相同的信息。

> `oneof`除了`oneof`中的所有字段共享内存之外，字段与常规字段类似，并且最多可以同时设置一个字段。

```
message UploadFileRequest {
  oneof request {
    MetaData metadata = 1;
    bytes chunk_data = 2;
  }
}
```

在这种情况下，我们可以先发送`Metadata`消息，然后将文件传输到 gRPC 服务器。

我们需要用两个新的 rpc 来更新欢迎服务:

*   上传文件
*   下载文件

```
service Greeter {
 rpc SayHello (HelloRequest) returns (StringResponse) {}
 rpc UploadFile(stream UploadFileRequest) returns (StringResponse) {}
 rpc DownloadFile(MetaData) returns (stream FileResponse) {}
}
```

> 在流式功能的请求或响应消息前添加`stream`关键字。

您可以在以下要点中找到完整的`hello.proto`:

记住运行以下命令来重新生成 gRPC 代码:

```
python -m grpc_tools.protoc -I=. --python_out=. --grpc_python_out=. ./protos/hello.proto
```

# 更新 gRPC 服务器

在`Greeter`服务下用两个新功能修改`server.py`文件:

*   `UploadFile`
*   `DownloadFile`

与`SayHello`函数不同，`UploadFile`的输入参数是一个迭代器，因为我们已经将它指定为一个流请求。

循环遍历迭代器以获取请求字段。由于我们使用了`oneof`关键字，迭代器中的每个请求可以是`MetaData`或`bytes`。我们可以很容易地使用条件检查来确定内容并相应地处理它。

在这种情况下，我们将把`chunk_data`存储到一个`bytearray`变量中，并保存在流的末尾。然后，我们向客户端返回一条`StringResponse`消息。

同时，`DownloadFile`函数将把一个文件流回客户机。最简单的实现是逐块读取文件，并将其作为`FileResponse`消息返回。

> 出于演示目的，我将 chunk_size 设置为 1024。根据您的使用情况，您可以将其设置为更高的值。

具有流功能的`server.py`的完整代码如下:

使用以下命令停止服务器并重新运行它:

```
python server.py
```

# 更新 gRPC 客户端

让我们更新`client.py`文件来调用以下函数:

*   上传文件
*   下载文件

请注意，`UploadFile` rpc 接受一个迭代器，因为它是一个流请求。因此，创建一个名为`read_iterfile`的新迭代器来逐块读取文件:

> 出于演示目的，我将`chunk_size`设置为 1024。根据您的用例修改它。

然后，可以如下调用`rpc`:

```
def run():
    with grpc.insecure_channel('localhost:50051') as channel:
        ... response = stub.UploadFile(read_iterfile('test.txt'))
        print("Greeter client received: " + response.message)
```

> 用文件的路径替换`test.txt`。

另一方面，`DownloadFile` rpc 返回一个流响应。我们可以很容易地对它进行迭代，并逐块保存文件:

> 根据您的用例替换`filename`和`extension`变量的值。

`client.py`的完整代码位于以下要点:

运行以下命令，测试通过 gRPC 上传和下载文件的情况:

```
python client.py
```

# 结论

让我们回顾一下你今天所学的内容。

本文首先简要介绍了 gRPC，然后继续介绍设置和安装。

然后，讨论了通过协议缓冲区的消息和服务定义。它还强调了生成供客户机和服务器应用程序使用的 gRPC 代码的步骤。

它还解释了基本的客户机和服务器代码实现。随后，它提供了代码来更新现有的代码，以便通过流媒体上传和下载文件。

感谢你阅读这篇文章。请随意查看我的其他文章。祝你有美好的一天！

# 参考

1.  [gRPC 文档—快速入门](https://grpc.io/docs/languages/python/quickstart/)
2.  [proto3 —语言指南](https://developers.google.com/protocol-buffers/docs/proto3)