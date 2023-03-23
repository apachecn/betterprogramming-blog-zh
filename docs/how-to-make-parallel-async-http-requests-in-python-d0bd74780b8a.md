# 如何用 Python 发出并行异步 HTTP 请求

> 原文：<https://betterprogramming.pub/how-to-make-parallel-async-http-requests-in-python-d0bd74780b8a>

## 使用线程的请求与使用信号量的 aiohttp

![](img/647cdc0e236915198b95b89f801c3295.png)

马特·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

通过阅读这篇文章，您将学会用 Python 并发地发出多个异步请求。本教程涵盖了两种不同的方法:

*   通过带有线程的请求包(每个请求一个本地线程)
*   通过带有信号量的 aiohttp 客户端(限制和共享任务的数量)

这两种实现都受到了以下[博客文章](https://blog.jonlu.ca/posts/async-python-http)中的解释的启发。作者提供的代码样本是为了在 Jupyter Notebook 上运行，它自带事件循环，不需要调用`asyncio.run`。在本教程中，将对代码进行清理和修改，使其能够在 Python 文件中工作。

除此之外，我们将实现自己的服务器，而不是调用外部 API。因此，您可以根据自己的意愿轻松增加或减少并发请求的数量。

让我们继续下一部分，开始安装必要的模块。

# 设置

强烈建议您在继续安装之前创建一个新的虚拟环境。

## aiohttp

激活它并运行以下命令来安装`aiohttp`:

```
pip install aiohttp[speedups]
```

它将自动为您安装所有必需的组件和依赖项。这包括:

*   `aiodns`
*   `chardet`
*   `brotlipy`

## 要求

确保你的虚拟安装了`requests`包。如果没有，请按如下方式安装:

```
pip install requests
```

# 模拟服务器

让我们首先创建一个名为`mockup_server.py`的新 Python 文件。它充当模型 API 服务器。实现将基于`aiohttp`。

## 导入

从`aiohttp`导入 web 模块并实例化一个新的`RouteTableDef`对象:

```
from aiohttp import webroutes = web.RouteTableDef()
```

接下来，定义一个新函数并用`route.get`修饰它，如下所示:

```
@routes.get('/user/{id}')
async def user(request):
    id = request.match_info.get('id', 0)
    data = {'id': int(id)}
    return web.json_response(data)
```

它将使用名为`id`的路径参数来处理所有传入的对`/user/` url 的请求。随后，它将向用户返回一个 JSON 响应。

最后，定义`Application`对象并调用`run_app`函数，如下所示:

```
app = web.Application()
app.add_routes(routes)if __name__ == '__main__':
    web.run_app(app)
```

保存文件并运行服务器:

```
python mockup_server.py
```

您应该会在控制台上看到以下输出:

```
======== Running on [http://0.0.0.0:8080](http://0.0.0.0:8080) ========
(Press CTRL+C to quit)
```

默认情况下，它将服务于端口 8080，并可通过以下方式访问:

```
# IP
http://0.0.0.0:8080# localhost
http://localhost:8080
```

# 方法 1:使用线程的请求

在本节中，您将实现一个具有以下特性的测试脚本:

*   一个`Worker`类，作为单个线程执行来自给定任务队列的任务
*   一个`ThreadPool`类表示使用队列中任务的线程池
*   通过`requests`模块调用 HTTP API 的函数
*   用 40 个工人实例化一个`ThreadPool`对象以调用 1000 个 API 请求的代码

大多数代码片段取自 JonLuca 的博客上创建的第二代实现。

## 导入

让我们首先创建一个名为`test_requests.py`的新 Python 文件。将以下导入语句追加到文件的顶部:

```
from queue import Queue
import requests
from threading import Thread
import time
```

## 工人阶级

继续添加下面的`Worker`类:

## 线程池类

你将需要另一个类作为`ThreadPool`。实现应该如下所示:

## HTTP GET 函数

之后，创建一个名为`get`的新函数，负责通过`requests`模块进行 HTTP GET 调用。

```
def get(url):
    i = url.split('/')[-1]
    r = session.get(url)
    results[i] = r.json()
```

## 主代码

使用以下代码片段为您的 Python 脚本添加最后一笔:

```
urls = [f"http://localhost:8080/user/{i}" for i in range(1000)]
pool = ThreadPool(40)
results = {}
session = requests.session()now = time.time()
pool.map(get, urls)
pool.wait_completion()
time_taken = time.time() - nowprint(time_taken)
```

*   在列表中创建 1，000 个 URL
*   用 40 个线程初始化一个线程池对象
*   初始化一个`requests.session`对象
*   将所有任务添加到队列中，并开始异步运行它们
*   等待所有任务完成，并打印出总时间

您可以在以下[要点](https://gist.github.com/wfng92/76f3ea11ad185bc073b7b8c004baaf7a)中找到完整的代码:

## 运行测试

保存文件并打开一个新的终端。确保您的模拟服务器正在后台运行。然后，在您的终端上运行以下命令:

```
python test_requests.py
```

运行它需要几秒钟的时间，您应该会看到一个浮点值作为输出，指示运行 1，000 个 HTTP 请求所用的总时间。

```
5.294068336486816
```

# 方法 2:带有信号量的 aiohttp

让我们创建另一个使用带有信号量的`aiohttp`的测试脚本。实现的结构如下:

*   用`n`线程实例化信号量并通过`asyncio.gather`等待任务的函数
*   通过`aiohttp`模块调用 HTTP API 的函数
*   用 40 个线程实例化`aiohttp.ClientSession` 并调用 1000 个 API 请求的主函数
*   通过`asyncio.run`运行主要功能的代码

代码片段取自 JonLuca 的博客上创建的第三代实现，并进行了相应的修改。

## 导入

用下面的 import 语句创建一个名为`test_aiohttp.py`的新文件:

```
import asyncio
import aiohttp
import time
```

## 信号量的帮助函数

下一步是创建一个新函数，用 n 个线程实例化一个信号量实例。请注意，信号量会限制一次可以执行的任务数量。此外，使用信号量会有额外的开销，但是使用池化是一个很好的做法。

它将调用`gather`函数，基于任务并发运行一个合适的对象，并在所有任务完成后返回结果；

## HTTP GET 函数

然后，创建一个名为`get_async`的新函数。该函数负责通过`aiohttp`进行 HTTP GET 调用:

## 主代码

主要功能的实现如下:

它将生成 1000 个 URL，并用 40 个线程调用`gather_with_concurrency`函数。随后，它将打印出所用的总时间，并关闭会话连接。最后一点就是用`asyncio.run`执行主功能。

完整代码位于以下[要点](https://gist.github.com/wfng92/2d2ae4385badd0f78612e447444c195f)中:

## 运行测试

保存文件并在终端上运行以下命令:

```
python test_aiohttp.py
```

您应该看到下面的输出，显示处理 1，000 个请求所用的总时间。

```
0.35976457595825195
```

**注意**:我做了一个实验，用 FastAPI 框架替换模型服务器，当使用 aiohttp 和 Semaphore 时，总时间大约是 2.5 秒。对于有线程的请求，花费的时间几乎是两倍。

# 结论

让我们回顾一下你今天所学的内容。

本文首先概述了运行并行异步 HTTP 请求的两种方法。

接下来，它通过`pip install`转移到安装过程，并提供了对服务器模型实现的解释。

它深入探讨了如何通过带有线程的请求来调用模型服务器。随后，它介绍了第二种方法，这种方法使用了带有信号量的`aiohttp`。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [JonLuca 的博客——用 Python 编写快速异步 HTTP 请求](https://blog.jonlu.ca/posts/async-python-http?ref=rpr)
2.  [aiohttp —官方文件](https://docs.aiohttp.org/en/stable/)
3.  [请求——正式文件](https://docs.python-requests.org/en/master/index.html)