# 生产者-消费者模式下的并行操作

> 原文：<https://betterprogramming.pub/hands-on-go-concurrency-the-producer-consumer-pattern-c42aab4e3bd2>

## 记录以了解关系

![](img/61cfeda061d9a7302901ac4e3d492bb3.png)

莎士比亚，没有他我的文章是不完整的！(由[杰西卡·潘普](https://unsplash.com/@yessijes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄)

过去三个月我一直在写围棋。

不可避免地，我最终了解了 Go 漂亮的并发原语。

正如一位智者曾经说过的，“宇宙只不过是一组不同的模式。”因此，我开始研究 Go 领域中一些常见的并发模式！

今天，我想讨论经典的并发模式:*生产者-消费者问题。*希望把自己理解的东西固化下来，为自己，也希望为你，建立一个未来的参考。

# 生产者-消费者问题到底是什么？

先来点背景。让我们定义一下我们正在讨论的问题:

*   有一个*生产者*即*生产*作业。
*   有一个*消费者*是*消费*他们。
*   它们(生产者和消费者)共享一个有限大小的*缓冲区*。
*   *生产者将其工作放入缓冲区。*
*   消费者*从缓冲区中获取*一个作业，然后*处理*该作业。

注意:你可以把生产者/消费者想象成函数，把作业想象成预定的任务或数据，或者传入的请求，或者本质上是许多其他的实体。问题定义中使用的术语是通用的，您可以将它们映射到许多不同的场景。

回到问题上。

所以，我们想同步生产者和消费者之间的关系。如果缓冲区还有一些剩余空间，生产者可以将作业放入其中。类似地，如果缓冲区有一个或多个未消耗的作业，消费者可以选择并处理它们。

问题是要确保消费者永远不会试图从一个空的缓冲区消费，生产者永远不会试图将作业放入一个满满的缓冲区。此外，生产者和消费者访问缓冲区的方式应该是安全的，不能危及缓冲区上的任何状态或数据。

# 戈朗对问题的回答

在大学，我在操作系统课程中学习了这些并发模式。课程是艰难的。然后，加上我们必须使用 C++ `pthread`库来完成这些实验作业的事实。

许多语言，如 Java 或 C++，都有这种用*线程*来处理并发的概念。我想，作为 Golang 的读者，你至少多少意识到了这一点。虽然您当然可以使用它们来满足您的并发需求，但是线程通常很难使用。

Golang 采用了不同的方法来回答并发性的终极问题。如你所知，围棋有 *goroutines* 和*通道*。

那么，我们如何解决 Golang 的生产者-消费者问题呢？在这篇文章中，我们将看看四种不同的变化。

# 单一生产者和单一消费者

让我们试着理解最简单的场景——一个生产者发送消息，一个消费者接收消息。

*一个建议:你会发现在打开两个浏览器窗口的情况下阅读这篇文章的其余部分很方便——一个窗口包含上面的代码，另一个显示下面的解释。*

我来试着解释一下上面的代码。

*   声明了两个不同的通道，一个能够处理字符串，另一个用于布尔值。
*   我们为每个函数运行两个独立的*例程*:`producer`和`consumer`。
*   `producer`简单地将消息从全局声明的数组发送到通道`link`。在成功发送所有消息后(没有陷入死锁或进入睡眠状态)，它在`close(link)`关闭通道。
*   那么，`consumer`内部发生了什么？`Consumer`正在接收来自`link`通道的消息。Go 中通道的一个特点是它们可以通过`range`被*迭代*。因此，当一个通道关闭时，我们可以在该通道上迭代以接收值。迭代继续，直到通道关闭。Go 中频道的这一功能相当方便。
*   如果您还不知道，在封闭通道上接收返回两个值:`zero value`和`false`。`zero value`不是数字`0` —它是通道正在处理的相应数据类型的零值。`false`是指出通道不再打开。这里有一篇关于这方面的简明文章:[戴夫·切尼的渠道公理](https://dave.cheney.net/2014/03/19/channel-axioms)。
*   请注意，我们的通道`link`是一个*无缓冲*通道。它只是在`producer`和`consumer`之间充当一个*链接器*。从信道的基本知识中你已经知道，`producer`不能通过`link`发送下一条消息，除非它已经消耗了上一条发送的消息。
*   因此，当`producer`关闭第 32 行的通道时，意味着消息发送成功，所以关闭是安全的。
*   那`done`频道呢？如你所知，围棋为`main`产生了一个 goroutine。这个主 goroutine 不会等待任何其他 go routine 完成。但是我们不希望这样——我们希望我们的生产者——消费者做好他们的工作。所以，我们使用`done`通道来确保主`goroutine` 只能在其他完成时退出。在第 47 行，主 goroutine 被阻塞，直到有人通过`done`通道发送一个值，这样它就可以立即接收和退出。该值由`consumer`在线路 39 上发送。

这非常简单明了。我们来看第二个。

# 单一生产者多重消费者

第二个变化是:

*这些“信息”包含了一段美丽节目《机器人先生》中的引言。如果你没有，那就去看看吧！*

让我们看一下代码。这与我们上一个例子非常相似，除了我们运行多个 Goroutines。这是唯一的区别。输出示例:

```
Message "just one big hoax." is consumed by worker 1.
Message "The world itself's" is consumed by worker 3.
Message "masquerading as insight, our social media" is consumed by worker 3.
Message "Spamming each other with our" is consumed by worker 2.
Message "running commentary of bullshit," is consumed by worker 1.
Message "Not with our rigged elections," is consumed by worker 1.
Message "but with our things, our property, our money." is consumed by worker 1.
Message "I'm not saying anything new." is consumed by worker 1.
Message "We all know why we do this," is consumed by worker 1.
Message "faking as intimacy." is consumed by worker 3.
Message "books make us happy," is consumed by worker 3.
Message "but because we wanna be sedated." is consumed by worker 3.
Message "Because it's painful not to pretend," is consumed by worker 3.
Message "because we're cowards." is consumed by worker 3.
Message "Or is it that we voted for this?" is consumed by worker 2.
Message "not because Hunger Games" is consumed by worker 1.
```

如您所知，由于 Goroutines 固有的不可预测性，每次运行相同的代码都会产生不同的输出结果，就像我们的生活一样。

![](img/3f63aac68795c3e7c27d35f277677fe9.png)

照片由[塞维里努斯·德万塔拉](https://unsplash.com/@dwntara?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 单一消费者多重生产者

是时候看看我们的第三个变化了:

样本输出:

```
Producer 1 sending message "but with our things, our property, our money."
Producer 1 sending message "I'm not saying anything new."
Consumed message "but with our things, our property, our money."
Consumed message "I'm not saying anything new."
Producer 0 sending message "The world itself's"
Producer 1 sending message "We all know why we do this,"
Producer 2 sending message "masquerading as insight, our social media"
Producer 0 sending message "just one big hoax."
Producer 3 sending message "but because we wanna be sedated."
Consumed message "The world itself's"
Consumed message "We all know why we do this,"
Consumed message "masquerading as insight, our social media"
Consumed message "just one big hoax."
Consumed message "but because we wanna be sedated."
Producer 3 sending message "Because it's painful not to pretend,"
Producer 3 sending message "because we're cowards."
Consumed message "Because it's painful not to pretend,"
Consumed message "because we're cowards."
Producer 3 sending message "- Elliot Alderson"
...(truncated)
```

让我们浏览一下代码:

*   正如你所看到的，我们有一个 2D 字符串的消息。这只是为了模拟这样一个事实，即每个生产者都有一组专用的消息要发送给消费者。他们每个人从自己的消息池中选取一条消息——我们有四个生产者，所以有四个消息池。
*   在主函数中，我们现在有了一个`WaitGroup`。我假设你知道 Go 的`sync`包。我们使用这个是出于通常的原因——等待制作人 Goroutines 完成。当我们等待的时候，Goroutines 成功地完成了他们的工作，我们可以关闭 67 行的`jobs`频道。
*   你可能会立即想到的问题是*为什么我们不从生产商内部关闭渠道？*很明显——当你有多个 go routine 时，你不知道它们中的哪一个会最后结束，所以从`produce`方法关闭`jobs`通道是不安全的——这基本上会在一些 go routine 完成它们的*职责线*之前关闭通道。
*   很简单，`produce`函数为每个单独的生产者 Goroutine 产生工作。我们将它传递给我们的`jobs`渠道，让消费者提货。
*   就像前面的例子一样，`consume`函数使用消息和信号`done`。

是时候进入我们的最后一个变体了。

![](img/39e6b329ed7fb4c1e50927871dc0b8f2.png)

照片由[蒂姆·特拉德](https://unsplash.com/@timtrad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 多生产者多消费者

我给你们看两个版本。第一个现在应该很容易理解了:

我们在这里使用两个`WaitGroups`。我们等待制片人结束，然后关闭频道。然后，最后，我们等待消费者完成。看一下示例输出:

```
Message "The world itself's" is consumed by consumer 1
Message "but because we wanna be sedated." is consumed by consumer 2
Message "Because it's painful not to pretend," is consumed by consumer 2
Message "masquerading as insight, our social media" is consumed by consumer 0
Message "faking as intimacy." is consumed by consumer 0
Message "I'm not saying anything new." is consumed by consumer 0
Message "Spamming each other with our" is consumed by consumer 0
Message "because we're cowards." is consumed by consumer 0
Message "- Elliot Alderson" is consumed by consumer 0
Message "Mr. Robot" is consumed by consumer 0
Message "just one big hoax." is consumed by consumer 2
Message "but with our things, our property, our money." is consumed by consumer 1
...(truncated)
```

是时候看看我们的最终代码了——这是*实现上述内容的另一种方式:*

这需要一些解释:

*   我们在这里想做什么？目的是避免使用`WaitGroup`。一般来说，Go 的`sync`包提供了对低级并发原语的支持。在上面的代码中，我们尽量保持在`channels`内部。
*   这是好的做法吗？我们应该避免使用`sync`包吗？我不知道。把它想成是解决同一个问题的不同方法。

现在是代码。这个有点长，和其他的不一样。让我解释一下:

*注:以上代码中的任何* `*worker*` *均指生产者。*

*   我们声明了一个包含三个不同字段的`type producers struct`:`myQ`、`quit`和`id`。把`myQ`想象成一个由制片人自己拥有的频道。所以如果我们声明一个`producers struct`的数组，那么它们每个都会有自己交换字符串的通道。`quit`通道用于安全退出每个制作人。最后，`id`只是一个标识。
*   我们在 80 线有一个交换`*producers`的通道。注意这里声明的`workerPool`是指针的通道，不是实际值。我们还有一个`jobQ`通道，用于向制片人发送工作，还有一个`allDone`通道，用于在一切完成后安全退出。
*   你可能想知道为什么我们使用一个渠道来发送工作。难道它本身就不会表现出生产者的行为吗？这是一个很好的问题，事实上，这只是模仿了这样一个事实，即生产者可能从一些外部来源生产工作，对它们进行处理，然后将它们发送给消费者。
*   我们在第 82 行创建一些生产者，在第 88 行生成我们的 Goroutines。我们也为消费者做类似的事情。
*   在中间，在第 91 行，我们运行我们的`execute`函数。这就像一个管理程序——它通过`jobQ`发送任务，关闭任务，给每个工人发信号`quit`，关闭`workerPool`通道，最后，发信号通知*一切都完成了*。
*   是时候看看`produce`功能了。我们在这里利用 Golang 的`select case`陈述。如你所知，`select`会执行第一个非阻塞通道，即使前面几个都在阻塞。
*   在我们的`select`块的第一个例子中，生产者 Goroutine 试图接收来自`jobQ`的消息。如果是这样，它将一个指向自己实体的指针传递给`workerPool`。仔细想想——*它本质上是通过* `workerPool` *来传递自身的。*
*   在第 58 行，消息被发送到`producer p`自己的通道。
*   我们也在`p`的`quit`频道收听。每当这里接收到一个值，Goroutine 就返回。
*   现在来了`consume`函数。它只是从`workerPool`中选取一个`*producer`，并在其通道上迭代。
*   本质上，为了避免使用`WaitGroup`，这在我们的例子中要简单得多，我们将每个生产者视为一个单独的实体来通过一个通道。我们的消费者选择一个随机的生产者，迭代生产者的`myQ`，然后消费。
*   正如我们前面提到的，`execute`函数试图优雅地停止一切。

上面代码的输出示例:

```
Job "The world itself's" produced by worker 0
Job "Spamming each other with our" produced by worker 2
Job "just one big hoax." produced by worker 1
Message "The world itself's" is consumed by consumer 0 from worker 0
Job "running commentary of bullshit," produced by worker 2
Message "just one big hoax." is consumed by consumer 1 from worker 1
Job "masquerading as insight, our social media" produced by worker 0
Message "running commentary of bullshit," is consumed by consumer 0 from worker 2
Job "faking as intimacy." produced by worker 1
Message "masquerading as insight, our social media" is consumed by consumer 1 from worker 0
Job "Or is it that we voted for this?" produced by worker 2
Message "Or is it that we voted for this?" is consumed by consumer 1 from worker 2
Job "Not with our rigged elections," produced by worker 0
Message "faking as intimacy." is consumed by consumer 0 from worker 1
Message "Not with our rigged elections," is consumed by consumer 1 from worker 0
Job "I'm not saying anything new." produced by worker 2
Job "but with our things, our property, our money." produced by worker 1
Message "Spamming each other with our" is consumed by consumer 2 from worker 2
...(truncated)
```

# 结论

在我忘记之前，这里发布的最后一个代码有一些值得注意的问题。我想讨论一下。但相反，你为什么不停下来好好想想呢？思考这些代码的问题，以及我们如何解决它们。这将是一个很好的事情弄清楚。

当然，如果你有任何不同的方法来为上面讨论的模式编写代码，请在评论中与我们联系。

希望我的解释足够清楚。

这很有趣。在写这篇文章的时候，我有机会对 Go 对并发性的支持进行了调整。我希望你也玩得开心！

非常感谢！

如果你喜欢这篇文章，考虑订阅我推荐的媒体！[https://medium.com/subscribe/@mottakin](https://medium.com/subscribe/@mottakin)