# 如果您想扩展您的应用程序，请学习如何异步

> 原文：<https://betterprogramming.pub/if-you-want-to-scale-your-application-learn-how-to-async-9c600a8f64ba>

## 理解异步编程的重要性

![](img/12d311086fcadd94bcf32bb43e6c481f.png)

照片由 [Cookie 在](https://unsplash.com/@cookiethepom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 Pom 拍摄

当我们开始创建一个 web 应用程序时，我们并不关心它的性能如何(老实说，我们也不应该关心)。我们的目标是完成一个基本的概念验证，看看它是否有一些观众。这种方法非常适合，直到您的用户群超过一定数量。让我们看看当你的用户群增长时会发生什么。

您的服务器每秒接收 5 个请求。您有一个反向代理，用于将这些请求转发到您的 HTTP 服务器。您已经将 HTTP 服务器配置为将这些请求转发给应用程序，应用程序依次处理这些请求并返回响应。如果您正在使用像 Django 这样的框架，该框架使用 Python，而 Python 在运行并行进程时有局限性，那么这种方法可能会导致问题。如果您是应用程序开发人员之一，并且没有做太多工作来优化您的端点，那么请相信我，您的应用程序完全依赖于您的 HTTP 服务器和反向代理的惊人架构！

当大量请求涌入您的服务时，通过减少循环次数或用内置函数替换操作来优化您的应用程序代码只能到此为止。异步编程来拯救！这是一种新的编程模式。不直观。我自己学的时候发现真的很难懂。但是很有效！

# 像个五岁小孩一样解释？

假设一个用户正在你的网站上创建一个个人资料。现在，你必须发送一封邮件到他们的邮箱，要求确认。但是当服务器试图发送邮件时，它必须与 SMTP 服务器通信，等待它的响应，发送邮件，等待确认。

看看服务器做了多少等待？如果它能在这段时间内执行其他任务而不是无所事事呢？

现在，这将减少完成该过程所需的总时间。因此，在这样的场景中，我们打破了逐行执行代码的模式，使用异步函数来加速执行。

思考异步的另一种方式是理解什么是阻塞和非阻塞任务。在后端，当您从数据库请求一些数据时，运行您的程序的进程正在等待数据库的响应并处于空闲状态。在这种情况下，它阻塞了进一步代码的执行。

虽然某些语言(如 Javascript)在默认情况下有异步的 AJAX 调用，但在其他语言(如 Python)中，您将不得不故意使您的请求异步。

您会考虑进行异步操作的一些场景:

1.  当用户希望导入 CSV 记录并根据其中给出的详细信息创建配置文件时。您的服务器接收文件并向用户确认您正在处理请求，而您在后台创建记录。
2.  当用户需要来自多个来源的详细信息时，您必须向每个来源发出 HTTP 请求。您可以为每个源创建异步操作，并并行执行获取操作。

# 怎么做？

每种语言都有自己的做法。虽然 javascript 有 async/await 关键字来做这件事，但 Java 有线程，Python 两者都有。在您的场景或语言中，哪种方法最适合您，这是您需要探索的。例如，如果你用 python 编程，你不必从头开始编写异步函数。

1.  如果您必须将功能执行委托给另一个进程，您可以看看 [Celery 项目](https://docs.celeryproject.org/en/stable/)。这个工具令人难以置信，每个开始大规模使用 python 的人都应该学习如何使用它
2.  如果你必须异步发出多个请求，你可以看看 [grequests](https://pypi.org/project/grequests/)

如果你想深入了解如何从头开始编写异步程序，你可以看看蒂姆科技的这个教程——[视频](https://www.youtube.com/watch?v=t5Bo1Je9EmE)

我现在不打算讨论并发性和并行性。这是另一篇文章的主题。敬请期待！

# 当这不是个好主意的时候。

如果您的任务是 CPU 密集型的，那么异步编程就不适合，因为异步编程利用了 CPU 在执行 I/O 操作时的空闲时间。

因此，如果你正在使用深度学习模型来分析图像，它不会对你的应用程序的规模产生太大影响。

# 外卖食品

熟悉不是线性执行的代码。这将需要一些努力，但它也将有助于您创建可伸缩性极高的应用程序。

```
**Want to Connect?**If you enjoyed this, you can subscribe to my [Software Engineering Weekly](https://softwareengineeringwk.substack.com/) newsletter and get similar stories directly in your inbox
```