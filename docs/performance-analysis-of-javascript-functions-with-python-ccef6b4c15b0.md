# Python 中 JavaScript 函数的性能分析

> 原文：<https://betterprogramming.pub/performance-analysis-of-javascript-functions-with-python-ccef6b4c15b0>

## 使用 WebSockets 记录静态网站的功能持续时间

![](img/bc4713dca2684305e5173b075444da34.png)

照片由 [Slejven Djurakovic](https://unsplash.com/@slavudin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

有时，我们对脚本中单个函数的执行时间感兴趣。有很多方法可以做到这一点，但是使用内置的 performance JavaScript `performance`库仍然是最简单的解释。

尽管可以手动计时函数，并从开发人员控制台获取结果，但使用任何数据分析工具的组合在 Python 中处理数据通常更简洁、更高效。在本教程中，我们将描述如何做到这一点。

# 设置 Python 代码

为了在 Python 和浏览器之间进行通信，我们依赖于一种叫做`WebSockets`的东西。本质上，这些是客户端(例如，浏览器)和后台服务(例如，服务器或 Python 脚本)之间的持久双向通信信道。

为此，我们使用了`asyncio`和`websockets` Python 库。因为我可能想要保存输出，所以我也以“追加”模式打开一个文件，如下所示:

```
import asyncio
import websockets
import datetimef = **open**('debug.txt','a')
```

接下来，我们想设置一个函数，一旦数据流连接上就运行。这包含一个无限循环，不断检查是否收到新消息。如果是，则将其打印到屏幕上或写入文件。代码如下:

```
async def **start**(websocket, path): print("connected")
    while True: data = **await** websocket.recv() print(f"< {data}",datetime.datetime.now().__str__())
       f.write('%s: %s\n'%(datetime.datetime.now().__str__(),data))
```

最后，我们希望定义主服务器进程并异步执行它，如下所示:

```
async def **main**():
    server = **await** websockets.serve(start, 'localhost', 5001)
    await server.wait_closed()**asyncio.run**(**main**())
```

# 创建可导入的 JS 概要分析模块

现在我们有了后端监听脚本，我们需要创建我们的分析工具和两者之间的通信通道(WebSocket)。

## 创建 WebSocket

创建初始 WebSocket 相对容易，但是，在发送任何信息之前，我们需要确保连接到后台服务。

这可以通过承诺来实现，如下所示:

## 包装现有代码

现在剩下的就是计时并发回结果。计时本身是通过获取两个`performance.now()`块之间的差值，将任何执行代码封装在其中来完成的。

这是通过创建一个包装函数来完成的。执行之后，它使用我们新创建的 WebSocket 将输出发送给 Python，如下所示:

或者，我们也可以通过添加附加的 print 语句在开发人员控制台中查看结果，如下所示:

```
console.warn('DEBUG profile',`${fn.name}: ${end-start}`)
```

## 异步函数呢？

如果我们有一个异步函数，并且我们想知道返回一个结果需要多长时间呢？

嗯，有两种方法可以解决这个问题:

*   最简单的是用我们现有的函数包装当前的执行，如下所示:

```
timer_as( function **customWrapper** () {asyncfn(arguments).then(d=> // do something //})});
```

*注意，虽然可以使用简写定义* `*()=>{fn}*` *，但是它不包含名称属性，因此在我们的调试日志中不会被命名。*

*   或者更好的是，我们可以调整计时器包装器来执行并等待代码结果，如下所示:

# 最后一步

最后，我们需要的只是一些代码来导入它，以及一个 Python 脚本来查看结果。下面是代码:

```
**import** {timer,timer_as,connect} **from** './debug_ws.js'
```

## 示例输出:

```
2021–11–02 14:37:15.275944: **get_data**: 345.8999999985099
2021–11–02 14:37:45.801908: **check_value**: 39.79999999701977
```

# 其他 JS 分析技术

还有一系列其他的方法来分析代码。以下是一些建议:

*   Chrome 开发工具中的灯塔/性能分析器/网络
*   性能库(更详细)
*   对于动态站点，服务器日志可以指示加载时间
*   GNU 性能

仅此而已。快乐剖析！