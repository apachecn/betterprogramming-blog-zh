# Laravel:失败、重试或延迟排队作业本身

> 原文：<https://betterprogramming.pub/laravel-fail-retry-or-delay-a-queued-job-from-itself-41e0bb14440c>

## 你可能不知道你可以使用这些技巧

![](img/4e552ae50212456efa2313118aecdf23.png)

Emile Guillemot 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当创建要推入队列的作业、侦听器或订阅者时，您可能会开始想，一旦被分派，队列工作器决定如何处理您的逻辑，您就只能靠自己了。

嗯…并不是说你*不能*从工作内部与队列工作人员互动，而是你通常不*需要*去…直到你这样做。

神奇的事情发生是因为`InteractsWithQueue`的特性。当排队的作业被从队列中取出时，`[CallQueuedListener](https://github.com/laravel/framework/blob/5.8/src/Illuminate/Events/CallQueuedListener.php#L90-L104)`将检查它是否使用了`InteractsWithQueue`特征，如果是，框架将*注入*内部的底层“作业”实例。

这个“job”实例就像是包装真正的`Job`类的驱动程序，其中包含关于连接和尝试的信息。

# 语境

我将用一个代码转换`Job`作为例子。这是一项将播客音频文件转码为 192kbps 的 MP3 的工作。因为这是在免费转码队列中设置的，所以它的可用性有限。

# 检查尝试

第一种方法叫做`attempts()`，顾名思义，返回尝试次数。总是从一次尝试开始。

这种方法可以和其他方法一起使用，比如`fail()`或`release()`(延迟)。出于举例说明的目的，我们将通知用户第*n*次重试:每次我们试图转码空闲队列中的播客时，我们将通知用户我们将重试第*n*次，给他取消未来转码的选项。

当逻辑已经失败时，告诉用户我们正在第*n*次重试是有用的，让用户(或开发人员)检查哪里出错了，但是当然你可以做得更多。

就我个人而言，我喜欢在和`Job`失败后做那个*，如果它还有重试时间，告诉他我们稍后会重试。当然，这个例子只是为了说明的目的。*

# 删除作业

第二种方法是`delete()`。您可以猜到，您可以从队列中删除当前的`Job`。

当您由于多种原因而不应该处理队列中的作业或侦听器时，这可能会很方便。例如，考虑一下这个场景:上传播客的发布者在转码发生之前由于任何原因(比如违反 TOS)被停用，我们应该**而不是**处理播客。

我们将把这段代码添加到前面的例子中:

如果您需要删除可能已被删除的模型上的作业，您可能希望[将](https://laravel.com/docs/5.8/queues#ignoring-missing-models) `[$deleteWhenMissingModels](https://laravel.com/docs/5.8/queues#ignoring-missing-models)` [设置为真](https://laravel.com/docs/5.8/queues#ignoring-missing-models)以避免处理不存在的东西。

# 工作失败

当您需要控制逻辑的人为失败时，这非常非常方便，因为使用空的`return`语句会将`Job`标记为成功完成。您可以强制使排队的作业失败，希望出现异常，如果可能的话，允许处理程序稍后重试。

这使您在作业失败时可以更好地控制。在任何情况下，您也可以使用`failed()`方法，该方法允许您在失败后[执行任何清理，如通知用户或删除某些内容。](https://laravel.com/docs/5.8/queues#cleaning-up-after-failed-jobs)

在这个例子中，如果由于某种原因无法从存储中检索到播客，比如当 CDN 关闭时，抛出一个自定义异常，那么我们的任务就会失败。

现在，进入最后一个方法。

# 释放(延迟)作业

这可能是最有用的方法，因为它允许你把工作推向未来。此方法用于[限制您的作业](https://laravel.com/docs/5.8/queues#rate-limiting)的速率。

除了速率限制之外，你还可以在某样东西不可用但你希望它在不久的将来可用的时候使用它。此外，为了避免先发制人的失败。

在最后一个例子中，我们将延迟代码转换:如果代码转换器的使用量很大，我们将延迟代码转换五分钟，直到负载降低。

我们可以使用一些魔法，例如，将一些插槽分配给代码转换器，如果代码转换器插槽已满，则延迟作业。

这几乎是您在排队作业中所能做的全部工作。排队愉快。