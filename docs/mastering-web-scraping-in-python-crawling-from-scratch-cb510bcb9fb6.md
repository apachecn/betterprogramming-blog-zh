# 掌握 Python 中的 Web 抓取:从头开始抓取

> 原文：<https://betterprogramming.pub/mastering-web-scraping-in-python-crawling-from-scratch-cb510bcb9fb6>

## 建立一个大规模抓取的网络爬虫。从一个接一个地访问页面开始，并以线程安全的方式扩展并行性

![](img/5d331ba27bed423897de618b235d6b89.png)

照片由[穆赫德·阿斯拉夫](https://unsplash.com/@minimxlist_?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

你试过爬上千页吗？进一步放大？处理系统故障并从中恢复？

在看过如何[从一个网站提取内容](https://medium.com/codex/mastering-web-scraping-in-python-from-zero-to-hero-51e27705b51b)和[如何避免被屏蔽](https://uxdesign.cc/stealth-web-scraping-in-python-avoid-blocking-like-a-ninja-8cb76db119ae)之后，我们再来看看抓取的过程。要获得大规模的数据，手动获取几个 URL 不是一个选项。我们需要使用一个自动系统，将发现新的网页，并访问它们。

*免责声明:对于真实世界的使用，请找到合适的软件。本指南假装是对爬行过程如何工作和做基础工作的介绍。但是还有很多细节需要解决。*

# 先决条件

为了让代码工作，你需要安装 [python3 和](https://www.python.org/downloads/)。有些系统已经预装了它。之后，通过运行`pip install`安装所有必要的库。

```
pip install requests beautifulsoup4
```

# 如何获取页面上的所有链接

从本系列的第一篇文章中，我们知道使用`requests.get`和`BeautifulSoup`很容易从网页中获取数据。我们将从在为测试刮痧准备的[山寨店中找到链接开始。](https://scrapeme.live/shop/page/1/)

获取内容的基础都是一样的。然后我们获取分页器上的所有链接，并将这些链接添加到一个`set`中。我们选择了一套以避免重复。如您所见，我们为链接硬编码了选择器，这意味着它不是一个通用的解决方案。目前，我们将专注于手头的页面。

# 一次一个 URL，连续

现在我们有几个链接，但没有办法全部访问。我们需要某种循环来为每个可用的 URL 执行提取部分，以解决这个问题。

也许最直接的方法，虽然不是可伸缩的方法，是使用相同的循环。但在此之前，有一个缺失的部分:避免抓取同一页面两次。

我们将跟踪另一个`set`中已经访问过的链接，并通过在每次请求前检查它们来避免重复。在这种情况下，`to_visit`没有被使用，只是出于演示目的而维护。

为了防止访问每个页面，我们还将添加一个`max_visits`变量。目前，我们忽略`robots.txt`文件，但我们必须文明友好。

这是一个递归函数，有两个退出条件:没有链接可访问，或者我们达到了最大访问量。在这两种情况下，它都将退出并打印已访问的链接和待定的链接。

需要注意的是，同一个链接可以多次添加，但只会被抓取一次。在一个大项目中，想法是设置一个计时器，只在几天后请求每个 URL。

# 关注点分离

我们说过这与提取或解析内容无关，但是我们需要在内容纠缠在一起之前将它们分开。

为此，我们将创建三个助手函数:获取 HTML、提取链接和提取内容。正如他们的名字所暗示的，他们中的每一个都将执行网络搜集的主要任务之一。

第一个将使用与前面相同的库从一个 URL 获取 HTML，但是为了安全起见，将它包装在一个`try`块中。

```
def get_html(url): 
	try: 
		return requests.get(url).content 
	except Exception as e: 
		print(e) 
		return ''
```

第二个，提取链接，将像以前一样工作。

```
def extract_links(soup): 
	return [a.get('href') for a in soup.select('a.page-numbers') 
		if a.get('href') not in visited]
```

最后一个将是提取我们想要的内容的占位符。由于我们正在简化这一部分，它将从同一页面获取基本信息，无需在详细信息页面输入。

为了表明我们可以提取一些内容，我们将打印每个产品的标题(神奇宝贝名称)。

```
def extract_content(soup): 
	for product in soup.select('.product'): 
		print(product.find('h2').text) 
 *# Bulbasaur, Ivysaur, ...*
```

把它们组装在一起。

注意到什么不同了吗？爬行逻辑不附属于链接提取部分。每个助手处理一个单独的部分。而`crawl`函数通过调用它们并应用结果来充当指挥者。

随着项目的发展，所有这些部分都可以被转移到文件中，或者作为参数/回调来传递。如果核心独立于所选的页面和内容，我们可以概括用例。

我们错过了什么吗？
我们需要添加第一个 URL 并调用爬行函数。由于`crawl`不再是递归的，我们将在一个单独的循环中处理它。

```
to_visit.add('https://scrapeme.live/shop/page/1/') 

while (len(to_visit) > 0 and len(visited) < max_visits): 
	crawl(to_visit.pop())
```

# 并行请求

这里缺少了一个重要的部分:并行性。HTTP 请求处理程序大部分时间都是空闲的，等待响应返回。这意味着我们可以同时发送几个，而不会使机器过载。然后在它们回来时进行处理。

值得注意的是，这种方法仅在命令不是强制性的情况下才有效。但是我们已经在使用集合，根据 [Python 的定义](https://docs.python.org/3/tutorial/datastructures.html#sets)，“集合是一个没有重复元素的**无序集合**”这意味着我们的过程从一开始就是无序的。

在深入研究并行请求之前，我们必须理解几个概念:同步和队列。

# 同步队列

线程或[并行计算](https://en.wikipedia.org/wiki/Parallel_computing)存在巨大的风险:从不同的线程修改相同的变量或数据结构。这意味着我们的两个请求是向集合添加新链接(即`to_visit`)。由于数据结构不受保护，两者都可以像这样读写它:

*   既读其内容，即`(1, 2, 3)` *(简体)*
*   线程一给页面添加链接`4, 5` : `(1, 2, 3, 4, 5)`
*   线程二添加页面链接`6, 7` : `(1, 2, 3, 6, 7)`

这是怎么发生的？当线程 2 编写新链接时，它将它们添加到只有三个元素的集合中。
*这是一个非常简化的版本；查看链接了解更多信息。*

我们能做些什么来避免这些冲突？同步或锁定。来自[文档](https://docs.python.org/3/library/queue.html):“队列使用锁来临时阻塞竞争线程。”这意味着线程一将获得集合上的锁，没有任何问题地进行读写，然后自动释放锁。同时，线程 2 必须等到锁可用。然后才读和写。

就目前而言，它不起作用。不要担心。现有代码的变化是最小的:我们用一个队列替换了`to_visit`。

但是队列需要处理程序或工人来处理它们的内容。通过上面的操作，我们创建了一个队列并添加了一个条目(原来的那个)。

我们还修改了`crawl`函数，将链接放入队列中，而不是更新之前的集合。

我们将创建一个使用[线程模块](https://docs.python.org/3/library/threading.html)来处理该队列的工人。

我们定义了一个新的函数来处理排队的项目。为此，我们进入了一个无限循环，当所有的处理完成时，这个循环就会停止。

然后`get`一个项目，它将阻止，直到一个项目可用。我们处理那个项目；目前，只需打印它来展示它是如何工作的。它稍后会调用`crawl`。

最后，我们通过调用`task_done`通知队列该项目已经被处理。

一旦队列得到所有项目的通知并为空，它将停止执行并结束无限循环。这就是`join`函数所做的，“阻塞，直到队列中的所有项目都被获取和处理。”

现在我们还需要两件事:处理项目和创建更多的线程(它不会与一个并行，不是吗？).

运行时要小心，因为`num_workers`和`max_visits`中的大数字会启动大量请求。如果脚本因为任何原因有一些小错误，您可能会在几秒钟内执行数百个请求。

# 表演

我们使用不同的设置运行基准测试，只是作为参考。

*   连续请求:29，32 秒
*   一个工人排队(`num_workers = 1`):2941 秒
*   两个工人排队(`num_workers = 2`):20.05 秒
*   五人队列(`num_workers = 5`):1197 人
*   十人队列(`num_workers = 10`):1202

顺序请求和只有一个工作者之间几乎没有区别。线程会带来一些开销，但是在这里几乎看不到。这将需要更严格的负载测试。一旦我们开始增加工人，开销就有回报了。我们可以添加更多，但这不会影响结果，因为他们大多数时间都是空闲的。

# 分布式处理

我们不会讨论下面的扩展步骤:在几个服务器之间分布爬行过程。 [Python 允许的](https://docs.python.org/3/library/multiprocessing.html#using-a-remote-manager)，有些库可以帮你做([芹菜](https://docs.celeryproject.org/en/stable/)或者 [Redis 队列](https://python-rq.org/))。这是一个巨大的进步，我们今天已经走得够远了。

作为一个快速预览，其背后的想法与线程的想法是一样的。每个项目都将像我们之前看到的那样被处理，但是是在不同的线程中，甚至是在运行相同代码的机器中。

通过这种方法，我们可以进一步扩展；理论上，没有限制。但在现实中，总有一个极限或瓶颈，通常是处理分发的中心节点。

# 在扩大规模时考虑

出于教育目的，我们展示了一个简化版本的爬行过程。要大规模应用所有这些，你应该首先考虑几件事。

# 构建 vs 购买 vs 开源

在您编写自己的爬行库之前，请尝试一些选项。很多很棒的开源库都可以实现: [Scrapy](https://docs.scrapy.org/en/latest/) ， [pyspider](http://docs.pyspider.org/en/latest/) ， [node-crawler](https://github.com/bda-research/node-crawler) (Node.js)，或者 [Colly](https://github.com/gocolly/colly) (Go)。以及很多为你提供抓取抓取解决方案的公司和服务。

# 避免被屏蔽

正如我们在以前的帖子中看到的，我们可以采取一些措施来避免阻塞。其中一些是代理和头。下面是一个简单的代码片段，将它们添加到我们当前的代码中。
*注意，这些* [*免费代理*](https://free-proxy-list.net/) *可能不适合你。它们是短命的。*

# 提取内容

我们在这里不详细介绍，只介绍一个简单的代码片段，用于提取每个商品的 id、名称和价格。我们将所有东西存储在一个`data`数组中，这不是一个好主意。但是对于演示来说已经足够了。

# 持续

我们没有保存任何东西，这是不可伸缩的。在现实世界中，我们应该存储内容，甚至 HTML 本身，以便以后处理。

以及所有发现的带有时间戳时间的 URL。这听起来似乎需要一个数据库。根据需要，我们可以只存储实际的内容或整个 URL、日期、HTML 等等。

# 礼服

链接提取部分不考虑[规范链接](https://en.wikipedia.org/wiki/Canonical_link_element)。一个页面可以有多个 URL:查询字符串或散列可能会修改它。在我们的例子中，我们会爬两次。现在不是问题，是需要考虑的事情。

正确的方法是将规范的 URL(如果存在的话)添加到访问列表中。那么我们可以从不同原始 URL 到达相同的页面，但是我们会检测到它是重复的。我们还可以使用 [url_query_cleaner](https://w3lib.readthedocs.io/en/latest/w3lib.html#w3lib.url.url_query_cleaner) 删除一些查询字符串参数。

# Robots.txt

我们没有检查它，因为我们正在使用一个为刮准备的测试网站。但是请检查 robots 文件，并在搜索实际目标时遵守它。除此之外，不要造成超过他们处理能力的流量。再次强调，要文明礼貌；)

# 最终代码

# 结论

我们希望你放弃三个要点:

1.  将获取 HTML 和从爬行本身提取链接分开。
2.  为您的用例选择合适的系统:简单顺序、并行或分布式。
3.  从零开始大规模建设可能会带来伤害。看看免费或付费的图书馆/解决方案。

我们即将完成这个关于网络抓取的系列文章。请继续关注下一篇关于进一步扩展这个爬行过程的文章。

不要忘记看看本系列的其他文章。
+ [缩放到分布式抓取](https://www.zenrows.com/blog/mastering-web-scraping-in-python-scaling-to-distributed-crawling?utm_source=medium&utm_medium=blog&utm_campaign=crawling_scratch) (4/4)
+ [像忍者一样躲避阻挡](https://www.zenrows.com/blog/stealth-web-scraping-in-python-avoid-blocking-like-a-ninja?utm_source=medium&utm_medium=blog&utm_campaign=crawling_scratch) (2/4)
+ [掌握抽取](https://www.zenrows.com/blog/mastering-web-scraping-in-python-from-zero-to-hero?utm_source=medium&utm_medium=blog&utm_campaign=crawling_scratch) (1/4)

感谢阅读。

*最初发表于*[*https://www.zenrows.com*](https://zenrows.com/blog/mastering-web-scraping-in-python-crawling-from-scratch?utm_source=medium&utm_medium=blog&utm_campaign=crawling_scratch)