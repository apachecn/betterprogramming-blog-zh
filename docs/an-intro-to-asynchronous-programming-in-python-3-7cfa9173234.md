# Python 3 中异步编程的介绍

> 原文：<https://betterprogramming.pub/an-intro-to-asynchronous-programming-in-python-3-7cfa9173234>

## 运行异步 Python 代码

![](img/9608cd83b8214c847d360844ea335256.png)

(图片来源于[来自 Pixabay](https://pixabay.com/users/boskampi-3788146/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1873831)) 的 Boskampi)

Python 被认为是最容易学习的语言之一。另一方面，Python 处理异步代码的方法可能相当混乱。本文将介绍异步 Python 代码的关键概念和示例，以使其更易于理解。

特别是，您应该带走以下内容:

1.  异步编程的核心词汇
2.  当异步方法有意义时
3.  Python 3 中异步代码的基础
4.  进一步调查的有用资源

我们开始吧！

# 什么是异步编程？

异步程序在不阻塞主进程的情况下并行执行操作*。这听起来有点拗口，但它的全部意思是:异步代码是一种确保你的程序在可以做其他工作的时候不会不必要地花费时间等待的方法。*

如果你以前读过任何关于异步编程的东西，你可能已经听过国际象棋的例子很多次了(一个国际象棋大师一次进行一场比赛，而不是一次进行所有的比赛)。虽然这是经典的有助于说明这个概念，烹饪食物提供了一个更相关的隐喻，你应该记住一些更辛辣的细节。

## 同步烹饪

你多久做一次这样的早餐？

第一步:煮鸡蛋(或者烤面包，给我们的素食朋友)

第二步:煮培根(燕麦片)

第三步:吃冷鸡蛋/烤面包和热培根/燕麦片

希望答案是“绝对不会”在进入下一道菜之前，一次做一道菜可能会产生一些非常恶心的食物(这是完全没有效率的)。这就是我们所说的:`synchronous cooking.`如果你有经常这样做的朋友，立即帮助他们。

## 异步烹饪

做一顿像样的饭时，很少想一次只准备一道菜。相反，如果你在做燕麦片和烤面包，你可以放上咖啡，然后开始烧水，取出燕麦片和面包。当水烧开时，你开始做燕麦片，在燕麦片做好的几分钟前，把面包片放进烤面包机。

现在，当一切都准备好了，你就有希望在同一时间准备好热咖啡、烤面包和燕麦片。这就是我们所说的:`asynchronous cooking`。

请注意，同时烹饪所有食物并不会减少每道菜的烹饪时间。你仍然需要让烤面包变成金棕色，咖啡渗滤出来，燕麦片必须……做燕麦片做好的时候做的任何事情。异步制作吐司和同步制作花费的时间是一样的。

然而，*任务并没有浪费时间等待每一项完成，而是作为烹饪过程的各个阶段*来执行。这意味着多项任务会尽快开始，您的宝贵时间会得到有效利用。

## 考虑

异步方法的另一个重要特征是*顺序不如我们继续其他任务*重要。例如，如果我们正在煮一顿有三道菜的饭，第一道菜在第二道菜之前，第三道菜在第三道菜之前。在这种情况下，我们可能需要同步烹饪这些菜肴。

即使异步方法是正确的，知道何时继续新的任务对于使它有用也是非常重要的。例如，在沸水的情况下，我们可以在打开炉子、拿出面包、拿出锅等等之间来回切换，但是，这真的能给我们带来任何价值吗？

当没有什么可等待的时候，我们会在任务之间跳跃。在沸水中需要很长时间的东西是水需要在炉子上加热的部分。异步将水烧开*甚至可能效率更低*，因为我们必须在任务之间来回移动(这被称为*执行开销*，例如，从炉子走到食品室拿面包，然后当锅离炉子更近时从食品室走到储锅室)。

简而言之，异步并不适合每个用例，也不会神奇地让您现有的同步代码更快。它的设计也不简单，需要对顺序比效率更重要的地方进行大量的预先考虑。

抛开这个扩展的隐喻，让我们看看异步 python 代码实际上是什么样子的！

# Python 3 中异步逻辑的秘诀

关于用 Python 编写异步程序的各种方法，你可能会在网上看到很多材料(例如回调、生成器等),为了全面了解，我推荐这个[演练](https://realpython.com/async-io-python/)，但是 Python 中的现代异步代码通常使用`async`和`await`。

## 水槽和重物？

`async`和`await`是 Python 3 中用于编写异步程序的关键词。`async/await`语法如下:

```
async def get_stuff_async() -> Dict:
    results = await some_long_operation()
    return results["key"]
```

这与同步版本没什么不同:

```
def get_stuff_sync() -> Dict:
    results = some_long_operation()
    return results["key"]
```

唯一的文本差异是`async`和`await`的存在。那么`async`和`await`实际上是做什么的呢？

`async`只是声明我们的函数是异步操作。`await`告诉 Python 这个操作可以暂停，直到`some_long_operation`完成。

这两个调用的功能差异是这样的:

1.  在`get_stuff_sync`中，我们调用`some_long_operation`，等待该调用返回`results`，然后返回结果的重要子集。当我们等待`results`时，不能执行其他操作，因为这是一个*阻塞*调用。
2.  在`get_stuff_async`中，我们*调度* `some_long_operation`，然后*让出控制权*回到主线程。一旦`some_long_operation`返回`results`，则`get_stuff_async`继续执行，并返回`results`的重要子集。当我们等待`results`时，主进程可以自由执行其他操作，因为这是一个*非阻塞*调用。

这是一个抽象的例子，但是您可能已经看到了这种异步方法的一些优点(和缺点)。`get_stuff_async`的实现给了我们一个更有效的方法来使用我们的资源，而`get_stuff_sync`提供了更多关于排序的确定性和更简单的实现。

然而，使用异步函数和方法比这个例子稍微复杂一些。

# 运行异步 Python 代码

在前一个例子中，我们看到了一些重要的新词汇:

*   日程安排
*   产量
*   阻塞
*   无阻塞
*   主流中泓线

当学习如何在 Python 中运行异步代码时，所有这些都可以更容易地解释。

在同步程序中，我们可以这样做:

```
if __name__ == "__main__":
    results = get_stuff_sync()
    print(results) # ***returns*** “The Emperor’s Wayfinder is in the Imperial Vault”
```

我们会把结果打印到控制台上。

如果您用我们的异步代码这样做，您会得到完全不同的消息:

```
if __name__ == "__main__":
    results = get_stuff_async()
    print(results)# ***returns*** <coroutine object get_stuff_async at 0x7f80372b9c40>
# ***bonus!!*** RuntimeWarning: coroutine 'get_stuff_async' was never awaited
```

这告诉我们的是`get_stuff_async`返回一个`coroutine`对象，而不是我们的重要结果。它也提示我们为什么:我们从来没有等待函数本身。

所以，我们只需要把`await`放在函数调用的前面，对吗？可惜，事情没这么简单。`await`只能在`async`函数或方法内部使用。我们需要使用`asyncio` *在*事件循环*上调度我们的逻辑，而不是顶级的‘await’。*

## 事件循环

Python 中异步操作的核心是事件循环。称之为“事件循环”给了它某种程度的庄严感，对吗？事实是，事件循环在各种程序中都有使用，它们并不特别或神奇。

以任何 web 服务器为例:它等待请求，然后当它接收到请求时，它将该请求视为一个事件，并将该事件与一个响应相匹配(例如，转到本文的 URL，媒体后端说“新事件:浏览器请求了`super-genius-article`，我们应该返回`super-genius-article.html`”)。这是一个事件循环。

“The”事件循环指的是 Python 内置的事件循环，它允许我们调度异步任务(它也适用于多线程和子流程)。您真正需要知道的是，它将您安排的任务与事件相匹配，这样它就知道流程何时完成。

为了使用它，我们消耗标准库`asyncio`模块，就像这样:

```
import asyncioif __name__ == "__main__": # asyncio.run is like top-level `await`
    results = asyncio.run(get_stuff_async())
    print(results)# ***returns*** “The Emperor’s Wayfinder is in the Imperial Vault”
```

在大多数情况下，这就是你从事件循环中所需要的。在一些高级用例中，您可能希望在编写低级库或服务器代码时直接访问事件循环，但这对于现在来说已经足够了。

因此，我们的小样本脚本如下所示:

```
import asyncioasync def some_long_operation():
    return {"key": "The Emperor's Wayfinder is in the Imperial Vault"}async def get_stuff_async():
    results = await some_long_operation()
    return results["key"]if __name__ == "__main__":
    results = asyncio.run(get_stuff_async())
    print(results)
```

这肯定是可行的，但是这种逻辑并不要求异步行为，甚至没有从异步行为中获益，所以让我们看一个更健壮的例子，在这个例子中，异步实际上是有益的。

# 网络请求的异步性

向 web 上的不同位置发送数据或从 web 上的不同位置接收数据是异步编程的一个用例。等待来自一个缓慢的远程 API 的响应并不有趣。在等待其他数据的同时执行其他重要的操作有助于提高程序的效率。现在让我们写一个例子。

## 快速、慢速的服务器

为了说明这个例子，我们将自己编写缓慢的远程 API:

```
import time
import uvicorn
from fastapi import FastAPIapp = FastAPI()  # the irony, amirite? @app.get("/{sleep}")
def slow(sleep: int):
    time.sleep(sleep)
    return {"time_elapsed": sleep} if __name__ == "__main__":
    uvicorn.run(app)  # uvicorn is a server built with uvloop, an asynchronous event loop!
```

这是一个简单的服务器，有一个端点接受路径参数`sleep`，休眠一段时间，然后在 JSON 响应中返回这个数字。(想了解更多关于编写高质量 API 的知识？新文章即将发布！)

不用说，这将让我们模拟一些我们可能正在等待的缓慢操作。

## 快速异步脚本

现在，对于客户端代码，我们将选择一些随机数，并等待一些随机的时间:

```
import asyncio
import aiohttpfrom datetime import datetime
from random import randrange async def get_time(session: aiohttp.ClientSession, url: str):
    async with session.get(url) as resp:  # async context manager!
        result = await resp.json()
        print(result)
        return result["time_elapsed"] async def main(base_url: str):
    session = aiohttp.ClientSession(base_url)
    # select 10 random numbers between 0, 10
    numbers = [randrange(0, 10) for i in range(10)]
    # await responses from each request
    await asyncio.gather(*[
        get_time(session, url)
        for url in [f"/{i}" for i in numbers]
    ])
    await session.close()if __name__ == "__main__":
    start_time = datetime.now()    
    asyncio.run(main("http://localhost:8000"))
    print(start_time - datetime.now())
```

运行这个脚本，我们得到如下输出:

```
[7, 2, 6, 4, 0, 9, 4, 2, 5, 5]  # times we requested the API to wait
{'time_elapsed': 0}  # API response JSON for waiting X seconds
{'time_elapsed': 2}
{'time_elapsed': 2}
{'time_elapsed': 4}
{'time_elapsed': 4}
{'time_elapsed': 5}
{'time_elapsed': 5}
{'time_elapsed': 6}
{'time_elapsed': 7}
{'time_elapsed': 9}
0:00:09.020562       # time it took the program to run
```

## 一个不太快的同步脚本

客户端代码的同步版本使用相同的可重复性请求时间，如下所示:

```
import requests
from datetime import datetime def get_time(url: str):
    resp = requests.get(url)
    result = resp.json()
    print(result)
    return result["time_elapsed"] def main(base_url: str):
    numbers = [7, 2, 6, 4, 0, 9, 4, 2, 5, 5]
    print(numbers) for num in numbers:
        get_time(base_url + f"/{num}") if __name__ == "__main__":
    start_time = datetime.now()
    main("http://localhost:8000")
    print(datetime.now() - start_time)
```

结果:

```
# returns:[7, 2, 6, 4, 0, 9, 4, 2, 5, 5]  # same numbers
{'time_elapsed': 7}
{'time_elapsed': 2}
{'time_elapsed': 6}
{'time_elapsed': 4}
{'time_elapsed': 0}
{'time_elapsed': 9}
{'time_elapsed': 4}
{'time_elapsed': 2}
{'time_elapsed': 5}
{'time_elapsed': 5}
0:00:44.099638      # 5x slower
```

# 观察

您可能会立即注意到，数字列表没有排序，但是当我们运行异步客户端代码时，远程 API 的输出是排序的。这是因为我们在接收结果的同时输出结果，等待时间长的自然比等待时间短的返回得晚。

第二，当我们的最长等待时间*是* 9 秒时，我们在 9 秒多一点的时间内打了 10 个电话。*同步*执行时间是我们从 API 请求的所有时间的总和，所以是 44 秒(或者慢 5 倍),因为它在继续下一个调用之前等待每个调用完成。*异步*执行时间相当于*我们请求的最长等待时间*(在这个客户端代码中*最长 9 秒*)，因此效率显著提高。

尽管 async 和 sync 调用都请求 API 等待相同的时间(44 秒)，但是通过使用异步编程，我们可以获得更快的整体程序。

最后，您会注意到这些客户端代码样本使用了一些有趣的东西，来自`asyncio`和`aiohttp`:

1.  异步上下文管理器(`async with`语法)的使用方式与常规的`with`语句相同，但是在异步代码中
2.  `aiohttp.ClientSession`对象是一个用于编写客户端异步网络请求的 API 在`aiohttp`的[文档](https://docs.aiohttp.org/en/stable/index.html)中查看关于它的更多信息
3.  `asyncio.gather`调用是执行一组异步函数并将其结果作为列表返回的一种非常方便的方式——当您需要来自多个地方的数据，但不想等待任何单个请求时，您可以想象使用它来进行有用的 API 调用

# 结论

希望本文为您提供了一些在 Python 中使用异步编程的弹药。它远非全面，因为它涉及到并行编程范例，所以有大量的知识需要学习，但这只是一个开始。

前进过程中需要记住的关键事项:

1.  异步并不总是一蹴而就的——在许多用例中，同步执行将比异步编程更简单、更快，因为并不是每个程序都必须等待数据返回
2.  使用异步需要设计——考虑程序的顺序以及何时需要哪些数据，而同步代码倾向于想当然地认为数据就在那里，每次调用都会立即返回
3.  虽然有许多方法可以编写异步代码，但是您几乎总是希望使用`async/await`——如果您不确定是否需要其他方法，那么您希望使用`async/await`语法

暂时就这样吧！晚安，祝您的异步编程之旅好运。

# 资源

这并不是促成这篇文章的所有因素的总和，但不管怎样，它们都是值得一看的好东西。

1.  https://docs.python.org/3/library/asyncio.html
2.  https://uvloop.readthedocs.io/
3.  FastAPI:【https://fastapi.tiangolo.com/ 
4.  令人敬畏的 Asyncio:[https://github.com/timofurrer/awesome-asyncio](https://github.com/timofurrer/awesome-asyncio)