# Python 生成器函数简介

> 原文：<https://betterprogramming.pub/an-introduction-to-python-generator-functions-cd9662b1d797>

## 分解生成器的机制并通过示例工作

![](img/34b2ef9a94ef3ea764de7a5b7e8b4a4b.png)

在 [Unsplash](https://unsplash.com/search/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Python 的生成器函数为管理数据和计算资源提供了一个强大的机制，但是对于那些 Python 新手来说，它们不一定是直观的。在本文中，我将分解生成器的机制，同时也介绍一个我希望是激励性的例子:一个用于管理和流式传输 S3 文件资源的小类。

# 简介和历史

鉴于开始使用 Python 并编写实际执行某些操作(例如，迭代一系列值，计算和/或打印这些值)的代码是多么容易，对于新的或不熟悉的 Python 程序员来说，这种语言可能不会理解这种概念，即 ***拖延*** 或延迟计算。对于有编译语言(例如 C++语言)工作背景的人来说，这种语言本身固有的松散性(或懒惰性)可能看起来很陌生。

大多数程序员都了解“[懒惰评估](https://en.wikipedia.org/wiki/Lazy_evaluation)”，并被教导编写代码来实现这种做法。但是 Python 对这种语言的本地支持(通过一个关键字简单而优雅地实现)引入了在其他编程语言中似乎很少见的功能和表达能力。毫不奇怪，[惰性求值作为一个概念被引入作为“lambda 演算](https://en.wikipedia.org/wiki/Lazy_evaluation#History)”的一部分，Python——尽管不仅仅是一种函数式语言(如 Lisp)——体现了这种函数式编程 DNA。( [Python 的上下文管理](https://medium.com/p/python-function-decorators-a-brief-tutorial-78377046c253#with-statement-context-managers)接口，因此该类可以像标准库的 file 对象一样使用。这消除了对更冗长的 try/except 块的需要。`__exit__`函数利用了无偿的对象删除——这违背了我过去遵守类析构函数最佳实践的`C++`习惯；但是它也明确了在对象清理时释放所有 S3 资源的意图——会话、资源和对象。boto3 库似乎不支持`close()`方法。

在构造函数中完成必要的 S3 内务处理后，该类提供了一个很好的接口，用于通过`__iter__`方法迭代文件流。客户端代码可能希望在对流进行迭代时实现额外的处理或逻辑。对他的小类的一个很好的增强是添加一个过滤谓词，这样如果用户知道他们只对数据的一个子集感兴趣，那么`__iter__`方法就不需要发出一个大文件的每一行。在这里使用标准库的`itertools.dropwhile`函数会很好。

主要的好处是 S3FileReader 类的客户端不需要担心 S3 的日常管理和维护，只需要表示感兴趣的资源。尽管该类在文件流上迭代，以便在`__iter__`方法中产生行，但是控制迭代和数据产生的是该类的客户端。

# 结论

Python 生成器函数在标准库中被广泛使用，它为程序员提供了一个强大的推迟计算的工具，节省了时间和空间。

# 参考

David Beazley，Brian k . Jones,《Python 食谱》,第三版，O'Reilly Media，2013 年 5 月。

[卢西亚诺·拉马尔霍，流畅的 Python，奥莱利媒体，2015 年 8 月](http://shop.oreilly.com/product/0636920032519.do)

[马克·卢茨，学习 Python，第五版，奥莱利媒体，2013 年 6 月](http://shop.oreilly.com/product/0636920028154.do)

 [## PEP 255 -简单发电机

### Python 编程语言的官方主页

www.python.org](https://www.python.org/dev/peps/pep-0255/)