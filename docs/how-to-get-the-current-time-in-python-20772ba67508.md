# 如何在 Python 中获取当前时间

> 原文：<https://betterprogramming.pub/how-to-get-the-current-time-in-python-20772ba67508>

## 日期时间模块简介

![](img/abb644c4162e37dc6d7e9ba59f52d669.png)

[布拉德·奈瑟利](https://unsplash.com/@bradneathery?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/time?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

学习与时间一起工作是任何程序员工具箱中不可或缺的一部分。与 JavaScript 不同，JavaScript 不需要导入就可以使用`Date()`类，Python 中的标准过程要求我们导入一个库。

该库的名称是`[datetime](https://docs.python.org/2/library/datetime.html)`。

让我们学习如何使用`datetime`在 Python 中检索当前时间。我们还将进一步学习创建一个带有特定时间的`datetime`对象。

# 如何导入日期时间库？

尽管 Python 要求我们导入库，但它是标准的，所以不需要安装任何东西。我们像这样使用`import`关键字和库名:

```
import datetime
```

这意味着我们已经导入了`datetime`库，并将通过库名来引用它。

# 如何使用日期时间？

嗯，这是一个有内涵的问题，因为`datetime`有太多的应用。

让我们保持专注，获取当前时间。棘手的是我们导入了`datetime`库，而不是类本身。这个类——碰巧同名——存在于库中。

为了获得当前时间戳，我们将执行以下操作:

```
dt = datetime.datetime.now()
print(dt) # 2020-02-01 11:24:31.436563
```

注意到我们在`datetime`上加倍了吗？第一个是对库名的引用，第二个是类本身，然后是方法`.now()`。

不要被输出迷惑，`dt`不是简单的字符串；它是一个`datetime`对象，类似于 JavaScript 日期是`Date()`对象。你可以用`datetime`类做很多事情，我推荐你查阅[官方文档](https://docs.python.org/3.8/library/datetime.html)。

但是等等，标题说的是当前时间，不是时间戳。一旦我们用当前时间戳声明了我们的`datetime`对象，我们就使用`.time()`方法只返回时间部分。

```
time = dt.time() # 11:24:31.436563
```

额外的好处是，现在我们已经一步一步地完成了这个过程，如果我们唯一的用途是获取当前时间，我们可以稍微清理一下代码。

```
from datetime import datetime as dttime = dt.now().time()print(time)) # 11:24:31.436563
```

我们在这里做了三件新的事情:

*   直接从库中导入了`datetime`类。
*   为`datetime`类分配了一个自定义标识符。
*   将`.now()`和`.time()`方法链接在一起。

# 获取特定的时间戳

要为特定的时间点创建一个`datetime`对象，我们需要向对象的初始化传递参数。

我们将依次经过*年*、*月*、*日*、*小时*、*分钟*、*秒*和*微秒*。这很容易记住，因为单位会逐渐变小。

```
from datetime import datetime as dttime = dt(2020,2,1,11,24,31,436563)print(time) # 2020-02-01 11:24:31.436563
```

# 结论

如果这是你第一次(一语双关)使用 Python 的`datetime`库，那么我希望它对你有所帮助，并告诉我使用情况。如果你是一名头发斑白的兽医，那么在下面的评论中分享你最喜欢的`datetime`方法。谢谢大家！