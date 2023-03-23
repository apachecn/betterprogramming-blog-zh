# 如何在 Python 中旋转代理

> 原文：<https://betterprogramming.pub/how-to-rotate-proxies-in-python-650bcea20db5>

## 使用 Python 构建一个自定义代理旋转器，以避免在 web 抓取时被阻塞。从自动运行状况检查的 IP 池中随机选择。

![](img/904c8606f760f31c3f3daa14c05c7c8f.png)

代理可以隐藏你的 IP 地址，但是当它被禁止的时候会发生什么呢？你需要一个新的 IP。或者您可以维护它们的列表，并为每个请求轮换代理。最后一个选择是使用[智能旋转代理](https://www.zenrows.com/?utm_source=medium&utm_medium=blog&utm_campaign=rotate_proxies)，稍后会详细介绍。

现在，我们将专注于构建我们的定制代理旋转器。我们将从常规代理列表开始，检查它们以标记工作的代理，并提供简单的监控以从工作列表中删除失败的代理。提供的例子使用 Python，但是这个想法在任何语言中都适用。

让我们开始吧！

# 先决条件

为了让代码工作，你需要安装 [python3 和](https://www.python.org/downloads/)。有些系统已经预装了它。之后，通过运行`pip install`安装所有必要的库。

```
pip install aiohttp
```

# 代理列表

您可能没有包含域+端口列表的代理提供程序。别担心，我们会找到的。

网上有几个免费代理人的名单。对于这个演示，抓取其中一个并将它的内容(只是 URL)保存在一个文本文件(`rotating_proxies_list.txt`)中。或者用下面的。

![](img/8ccf6177c1d09c40c93f59d491cbfe80.png)

从站点导出和复制代理

免费代理是不可靠的，下面的那些可能对你不起作用。它们通常是短命的。

```
167.71.230.124:8080
192.155.107.211:1080
77.238.79.111:8080
167.71.5.83:3128
195.189.123.213:3128
8.210.83.33:80
80.48.119.28:8080
152.0.209.175:8080
187.217.54.84:80
169.57.1.85:8123
```

然后，我们将读取该文件并创建一个包含所有代理的数组。读取文件，去掉空白，并拆分每一行。保存文件时要小心，因为我们不会对有效的`IP:port`字符串进行任何健全性检查。我们会保持简单。

```
proxies_list = open("rotating_proxies_list.txt",
                    "r").read().strip().split("\n")
```

# 检查代理

让我们假设我们想要大规模运行铲运机。演示是简化的，但其思想是将代理和它们的“健康状态”存储在一个可靠的介质中，如数据库。我们将使用在每次运行后消失的内存中的数据结构，但是你得到了想法。

首先，让我们编写一个简单的函数来**检查代理是否工作**。为此，调用 [ident.me](http://ident.me/) ，它将返回 IP。这是一个适合我们用例的简单页面。我们将使用`[asyncio](https://docs.python.org/3/library/asyncio.html)`和`[aiohttp](https://docs.aiohttp.org/en/stable/)`，一个类似于著名的`requests`的“异步 HTTP 客户端/服务器”。它更适合我们，因为它的目的是异步工作，当同时检查几个代理时，它会帮助我们。

目前，它从代理列表中获取一个项目，并调用提供的 URL。大部分代码都是样板文件，很快就会被证明是有用的。有两种可能的结果:

*   如果一切正常，它打印响应的内容和状态代码(即 200)，这可能是代理的 IP。
*   由于超时或其他原因，打印出一个错误。这通常意味着代理不可用或无法处理请求。当使用免费代理时，许多这样的问题都会出现。

```
import aiohttp
import asyncioproxies_list = open("rotating_proxies_list.txt", "r").read().strip().split("\n")
timeout = aiohttp.ClientTimeout(total=30)async def get(url, session, proxy):
    try:
        async with session.get(url, proxy=f"http://{proxy}", timeout=timeout) as response:
            print(response.status, await response.text())
    except Exception as e:
        print(e)async def check_proxies():
    proxy = proxies_list.pop()
    async with aiohttp.ClientSession() as session:
        await get("http://ident.me/", session, proxy=proxy)asyncio.run(check_proxies())
```

我们有意使用 HTTP 而不是 HTTPS，因为许多免费代理不支持 SSL。

# 添加更多检查以验证结果

异常意味着请求失败，但是我们应该检查其他选项，比如状态代码。我们将认为**只对特定的代码**有效，其余的标记为错误。这个列表并不详尽，可以根据你的需要进行调整。例如，您可能认为 404“未找到”是无效的，应该再次测试。

我们还可以添加其他检查，比如验证响应是否包含 IP 地址。

```
VALID_STATUSES = [200, 301, 302, 307, 404]async def get(url, session, proxy):
    try:
        async with session.get(url, proxy=f"http://{proxy}", timeout=timeout) as response:
            if response.status in VALID_STATUSES: # valid proxy
                print(response.status, await response.text())
            else:
                print(response.status)
    except Exception as e:
        print('Exception: ', type(e))
```

# 迭代所有代理

太好了！我们现在需要为数组中的每个代理运行检查。我们将像以前一样遍历调用`get`的代理列表。但是我们将使用`asyncio.gather`来启动所有的请求并等待它们完成，而不是按顺序执行。Async 使得代码更加复杂，但是它加快了网页抓取的速度。

为了安全起见，该列表被硬编码为最多 10 项，以避免数百个非自愿请求。

```
async def check_proxies():
    proxies = proxies_list[0:10] # limited to 10 to avoid too many requests
    async with aiohttp.ClientSession() as session:
        tasks = [
            get("http://ident.me/", session, proxy=proxy)
            for proxy in proxies
        ]
        await asyncio.gather(*tasks, return_exceptions=True)
```

我们还应该**限制并发请求的数量**。我们将使用[信号量](https://docs.python.org/3/library/asyncio-sync.html#asyncio.Semaphore)来实现，信号量是一个获取和释放锁的对象。它将维护一个内部计数器，只允许这么多的调用(在本例中是 10 个)，从而创建一个最大的并发性。

我们也需要改变如何称呼`check_proxies`。

```
sem = asyncio.Semaphore(10)
# ...
async def get(url, session, proxy):
    async with sem:
        try:
            # ...loop = asyncio.get_event_loop()
loop.run_until_complete(check_proxies())
```

# 将工作的代理与失败的代理分开

检查输出日志并不理想，不是吗？我们应该为代理列表保留一个内部状态。我们将他们分成三组:

*   未选中:未知状态，待选中。
*   工作中:使用此代理的最后一次调用成功。
*   不起作用:最后一个请求失败。

从`set`中添加或删除条目比从数组中添加或删除条目更容易，并且它们具有避免重复的优点。我们可以在列表之间移动代理，而不用担心同一个列表会出现两次。如果有，就是不加。这将简化我们的代码:从一个集合中删除一个项目，并将其添加到另一个集合中。为了实现这一点，我们需要稍微修改一下代理存储。

将存在三个集合，上面看到的每个组一个。第一个，`unchecked`，将包含文件中的代理。一个集合可以从一个数组中初始化，这使得我们很容易创建它。

```
proxies_list = open("rotating_proxies_list.txt", "r").read().strip().split("\n")
unchecked = set(proxies_list[0:10]) # limited to 10 to avoid too many requests
# unchecked = set(proxies_list)
working = set()
not_working = set()# ...
async def check_proxies():
    async with aiohttp.ClientSession() as session:
        tasks = [
            get("http://ident.me/", session, proxy=proxy)
            for proxy in unchecked # use the new set for the loop
        ]
        await asyncio.gather(*tasks, return_exceptions=True)
#...
```

现在，编写助手函数在状态之间移动代理。每个州一个助手。他们会将代理添加到一个集合中，并从其他两个集合中删除它(如果存在)。这就是集合派上用场的地方，因为我们不需要担心检查代理是否存在或循环数组。如果存在或被忽略，则调用" discard "来移除，但不会引发任何异常。

例如，当一个请求成功时，我们将调用`set_working`。并且该函数将从未检查的或不工作的集合中移除代理，同时将其添加到工作集合中。

```
def reset_proxy(proxy):
    unchecked.add(proxy)
    working.discard(proxy)
    not_working.discard(proxy)def set_working(proxy):
    unchecked.discard(proxy)
    working.add(proxy)
    not_working.discard(proxy)def set_not_working(proxy):
    unchecked.discard(proxy)
    working.discard(proxy)
    not_working.add(proxy)
```

我们错过了关键的部分！我们需要编辑`get`来在每次请求后调用这些函数。`set_working`表示成功的，其余的用`set_not_working`表示。

```
async def get(url, session, proxy):
    async with sem:
        try:
            async with session.get(url, proxy=f"http://{proxy}", timeout=timeout) as response:
                if response.status in VALID_STATUSES: # valid proxy
                    set_working(proxy)
                else:
                    set_not_working(proxy)
        except Exception as e:
            set_not_working(proxy)
```

目前，在脚本的末尾添加一些跟踪，看看它是否运行良好。因为我们运行了所有的项目，所以集合`unchecked`应该是空的。这些项目将填充其他两个集合。希望`working`不是空的😅-这可能会发生在免费代理上。

```
#...
loop = asyncio.get_event_loop()
loop.run_until_complete(check_proxies())print('unchecked ->', unchecked)
# unchecked -> set()
print('working ->', working)
# working -> {'152.0.209.175:8080', ...}
print('not_working ->', not_working)
# not_working -> {'167.71.5.83:3128', ...}
```

# 使用工作代理

这是一种检查代理的简单方法，但还不是真正有用的。我们现在需要一种方法来**获得工作代理**并为真正的原因使用它们:web 抓取实际内容。我们将创建一个选择随机代理的函数。

我们在示例中包括了工作代理和未检查代理，如果符合您的需要，请随意只使用工作代理。我们将在后面看到为什么未检查的也会出现。

`random`不适用于集合，所以我们将使用`tuple`来转换它们。

```
import randomdef get_random_proxy():
    # create a tuple from unchecked and working sets
    available_proxies = tuple(unchecked.union(working))
    if not available_proxies:
        raise Exception("no proxies available")
    return random.choice(available_proxies)
```

接下来，如果没有代理，我们可以编辑`get`函数来使用随机代理。`proxy`参数现在是可选的。我们将使用该参数来检查初始代理，就像我们之前所做的那样。但在那之后，我们可以忘记代理名单，没有它也可以打电话给`get`。**如果失败，将随机使用一个**并添加到`not_working`集合中。

因为我们现在想要获取实际内容，所以我们需要返回响应或引发异常。与`requests`不同，使用`aiohttp`，响应的内容必须是`await`。这是最终版本。

```
async def get(url, session, proxy=None):
    if not proxy:
        proxy = get_random_proxy()async with sem:
        try:
            async with session.get(url, proxy=f"http://{proxy}", timeout=timeout) as response:
                if response.status in VALID_STATUSES:
                    set_working(proxy)
                else:
                    set_not_working(proxy)await response.text() # content needs to be "awaited"
                return response # return response
        except Exception as e:
            set_not_working(proxy)
            raise e # raise exception
```

在脚本下面包含您想要抓取的内容。我们将再次调用相同的测试 URL 进行演示。

想法是从这里开始，基于这个主干构建一个真实世界的刮刀。并且为了对其进行缩放，将项目存储在诸如数据库(即 Redis)的永久存储器中。

```
#....
loop = asyncio.get_event_loop()
loop.run_until_complete(check_proxies())# real scraping part comes here
async def main():
    async with aiohttp.ClientSession() as session:
        result = await get("http://ident.me/", session)
        print(result.ok) # True
        print(result.status) # 200
        print(await result.text()) # 152.0.209.175asyncio.run(main())
```

假阴性或一次性错误会怎样？一旦我们向`not_working`集合发送了一个代理，它将永远留在那里。已经无路可退了。

# 重新检查不工作的代理

我们应该**不时地重新检查失败的代理**。有许多原因:失败是由于网络问题、错误或代理提供者修复了它。

在任何情况下，Python 都允许我们设置`[Timers](https://docs.python.org/3/library/threading.html#timer-objects)`，“一个应该只在经过一定时间后才运行的动作”。有不同的方法可以达到相同的目的，这非常简单，只需使用三行代码就可以运行它。

还记得`reset_proxy`功能吗？直到现在我们才开始使用它。我们将设置一个`Timer`来为每个被标记为不工作的代理运行该功能。20 秒对于真实世界来说是个小数字，但是对于我们的测试来说已经足够了。我们排除一个失败的代理，并在一段时间后将其移回未检查状态。

这就是在`get_random_proxy`中同时使用工作集和未检查集的原因。修改该函数，只使用有效的代理，以获得更健壮的用例。然后，您可以定期运行`check_proxies`,它将遍历未检查的元素——在本例中，失败的代理在 sin bin 中保留了一段时间。

```
from threading import Timerdef set_not_working(proxy):
    unchecked.discard(proxy)
    working.discard(proxy)
    not_working.add(proxy)# move to unchecked after a certain time (20s in the example)
    Timer(20.0, reset_proxy, [proxy]).start()
```

对于更健壮的系统，还有最后一个选项，但是我们将把实现留给您。**存储每个代理**的分析和使用情况，例如，失败的次数以及最后一次失败是什么时候。使用这些信息，调整重新检查的时间——失败几次的代理需要更长的时间。或者甚至在工作代理的数量低于阈值时设置一些警报。

# 结论

构建一个简单的代理旋转器对于小型的抓取脚本来说似乎是可行的，但是它可能会变得很痛苦。但是，嘿，你做到了！！

这些是我们遵循的步骤:

1.  以纯文本形式存储代理列表
2.  作为数组从文件导入
3.  检查每一个
4.  把工作的分开
5.  清理时检查故障，并将其从工作列表中删除
6.  不时地重新检查不工作的代理

注意，在抓取登录或任何其他类型的会话/cookie 时，不要轮换 IP。

如果你不想担心手动旋转代理，你可以随时使用我们的 [ZenRows](https://www.zenrows.com/?utm_source=medium&utm_medium=blog&utm_campaign=rotate_proxies) ，一个包含智能旋转代理的 Web 抓取 API。它像一个普通的代理一样工作——只有一个 URL——但是为每个请求提供不同的 IP。

感谢阅读。

*原载于*[*https://www.zenrows.com*](https://www.zenrows.com/blog/how-to-rotate-proxies-in-python?utm_source=medium&utm_medium=blog&utm_campaign=rotate_proxies)