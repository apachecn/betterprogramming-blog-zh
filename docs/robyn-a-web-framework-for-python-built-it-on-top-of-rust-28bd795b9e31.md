# 你好，Robyn:带 Rust 运行时的 Python Web 框架

> 原文：<https://betterprogramming.pub/robyn-a-web-framework-for-python-built-it-on-top-of-rust-28bd795b9e31>

## 使用 Robyn web 框架构建一个简单的服务器

![](img/725a74e53e454608f9021b322e5f9efc.png)

> “Robyn 是一个快速、高性能的 Python web 框架，具有 Rust 运行时。它被设计为提供接近本地 Rust 的吞吐量，同时受益于用 Python 编写的代码。Robyn 可与 Flask、FastAPI、Django 等其他产品相媲美，也是一种 web 服务器选择。Robyn 的主要优势之一是，它不需要外部 web 服务器来进行生产，从而使其更加高效和简化。”
> 
> 更多关于罗宾的信息，请点击这里。

# 特征

*   正在积极开发中！
*   写在 Rust，btw xD
*   多线程运行时
*   可扩张的
*   一个简单的 API
*   同步和异步功能支持
*   动态 URL 路由
*   多核扩展
*   WebSockets！
*   中间件
*   热重装
*   社区第一，真正的自由/开源软件！

# 设置

```
py -m venv venv cd venv/Scripts activate
```

# 装置

```
pip install robyn
```

# 你好世界

让我们编写一个返回“Hello World”的简单端点我们创建一个名为`[app.py](http://app.py)`的文件。

我们导入 Robyn 并创建一个 Robyn 实例。在这种情况下，`app`变量。

然后，我们定义一个路径操作装饰器。当一个请求通过一个`get`操作到达路径`/`时，`hello`函数将处理这个请求，并以“Hello，World！”。

为了运行服务器，我们在终端中编写:

# 提供静态文件

`index.html`

```
<h1>Hello, World!<h1>
```

这里，我们导入了`static file`对象，在处理程序内部，我们传递了我们想要提供的 HTML 文件的路径。

# CRUD API

这个处理程序以 JSON 的形式返回`fake_fruit_database`列表。

## 通过 ID 处理程序获取

`helper.py`包含一个函数，帮助我们在一个列表中查找并返回带有相应`id`的字典。

在`fruit/:id`处理程序中，我们添加了路径参数`id`，以检索传递了`id`的水果。

请求对象具有以下形式:

*   为了提取`id`的值，我们首先传递键`params`，然后传递键`id`。
*   如果没有匹配的`id`，处理器返回消息“没有找到水果”否则，它返回一个 JSON 响应。

## 后期处理程序

在下面的处理程序中，我们从请求对象中提取主体并解码:

然后，我们使用`json.loads()`方法将主体反序列化并转换为 python 字典。

我们为添加的水果生成一个`id`。我们创建一个新的字典，并将其添加到水果列表中。并返回状态代码`201`和一个 JSON 作为主体响应。

## 放置处理程序

我们需要提取这个处理程序的`id`参数和主体。

然后，我们搜索传递了`id`的水果，并用请求体中的值替换水果值。

## 删除处理程序

这里，我们从水果列表中删除带有作为参数传递的`id`的字典。

默认的`PORT`和`URL`分别是`5000`和`127.0.0.1`。

如果我们想在不同的端口和 URL 上启动服务器，我们必须将值传递给`app.start()`。

```
app.start(port=8000, url="0.0.0.0")
```

# 一锤定音

我学习 Rust 已经有几个月了，我认为使用 Rust 来构建一个 Python web 框架并利用 Rust 的力量是很有趣的。我热衷于使用 Robyn 并撰写相关文章。我很兴奋地关注着它的发展。如果您对其他软件包、架构等有任何建议，请告诉我。

源代码可在[这里](https://github.com/carlosm27/robyn_demo)获得。感谢阅读。

# 参考

*   [罗宾的 GitHub 页面](https://github.com/sansyrox/robyn)
*   [罗宾文档](https://sansyrox.github.io/robyn/#/)
*   你好，罗宾！

*原载于*[*https://Carlos mv . hash node . dev*](https://carlosmv.hashnode.dev/robyn-a-web-framework-for-python-built-it-on-top-of-rust)*。*