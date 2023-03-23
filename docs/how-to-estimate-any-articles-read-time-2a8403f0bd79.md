# 如何估计任何文章的阅读时间

> 原文：<https://betterprogramming.pub/how-to-estimate-any-articles-read-time-2a8403f0bd79>

## 了解如何使用 Python 进行网络爬行来确定文章的平均阅读时间

![](img/5c6bab2bb173c4b2dff89a66440162dc.png)

要查看源代码，请访问我的 [GitHub 页面](https://github.com/assafelovic/reading_time_estimator)。

你最后一次根据文章的阅读时间决定阅读它是什么时候？对我来说，这经常发生。

为你的网站内容提供一篇文章的阅读时间估计可以对你的终端用户有很大的帮助。

首先，它允许最终用户准备他们需要的时间来完整地阅读一篇文章。其次，它可以帮助他们在有限的时间内选择合适的文章。

最后，它提供了一系列全新的特性、排序选项和过滤器改进(比如按阅读时间过滤文章)。

在这篇文章中，我将带你通过爬行和简单的计算(用 Python 写的)来估计任何公共文章 URL 的阅读时间。

对了，这个帖子的阅读时间估计是五分钟。Medium 说是三分钟(见[他们怎么计算阅读时间这里](https://help.medium.com/hc/en-us/articles/214991667-Read-time))。那么，告诉我谁更准确。

# 估计每分钟字数

每分钟字数，通常缩写为 *WPM* ，是衡量一分钟内处理的字数，常用来衡量打字或阅读的速度。

WPM 有许多含义和复杂性。首先是平均阅读时间是主观的。其次，单词的长度或持续时间显然是可变的，因为一些单词可以很快阅读(如“狗”)，而另一些单词则需要长得多的时间(如“犀牛”)。

因此，每个单词的定义往往被标准化为五个字符长。

其他参数会影响阅读时间，如字体类型和大小、你的年龄、你是在显示器上还是在纸上阅读，甚至文章网站中的段落、图像和按钮的数量。

根据这一领域的研究，人们在纸上阅读英语的速度是 200 WPM，在显示器上是 180 WPM(目前的记录是 290 WPM)。

为了简单起见，我们将一个单词定义为五个字符(包括空格和标点)，WPM = 200。请随意向您的计算中添加其他参数。请注意，如果您要寻找的只是一个粗略的估计，我们所定义的就足够了。

# 从 URL 到估计读取时间

让我们设计简单的算法流程:

1.  提取可见的网页文本(标题、副标题、正文、页面按钮等)。)从给定的 URL。
2.  从文本中过滤掉不必要的内容。
3.  估计过滤文本的阅读时间。

# 1.提取可见的网页文本

为了提取网页的文本内容，我们将使用 Python 的库，名为 [Beautiful Soup](https://pypi.python.org/pypi/beautifulsoup4) 和 [urllib](https://docs.python.org/2/library/urllib.html) :

```
import bs4
import urllib, redef extract_text(url):
    html = urllib.urlopen(url).read()
    soup = bs4.BeautifulSoup(html, 'html.parser')
    texts = soup.findAll(text=True)
    return texts
```

# 2.过滤不必要的页面内容

一旦我们提取了想要的文本内容，我们需要过滤掉所有不必要的内容，比如样式(CSS)、脚本(JS)、HTML 标题、注释等等。：

```
def is_visible(element):
    if element.parent.name in ['style', 'script', '[document]', 'head', 'title']:
        return False
    elif isinstance(element, bs4.element.Comment):
        return False
    elif element.string == "\n":
        return False
    return Truedef filter_visible_text(page_texts):
    return filter(is_visible, page_texts)
```

# 3.估计阅读时间

为了估计文章的阅读时间，我们需要计算字数(如上所述)并除以定义的 WPM (200):

```
WPM = 200
WORD_LENGTH = 5def count_words_in_text(text_list, word_length):
    total_words = 0
    for current_text in text_list:
        total_words += len(current_text)/word_length
    return total_wordsdef estimate_reading_time(url):
    texts = extract_url(url)
    filtered_text = filter_visible_text(texts)
    total_words = count_words_in_text(filtered_text, WORD_LENGTH)
    return total_words/WPM
```

就是这样！通过调用方法`estimate_reading_time`，您可以用任何字符串 URL 随意测试它。

要查看源代码，请访问我的 [GitHub 页面](https://github.com/assafelovic/reading_time_estimator)。

我的下一篇文章将重点讨论如何总结文章(TL；DR)使用自然语言处理和网页抓取，敬请关注！要了解更多编码基础知识，请访问 [devclass.io.](https://devclass.io)