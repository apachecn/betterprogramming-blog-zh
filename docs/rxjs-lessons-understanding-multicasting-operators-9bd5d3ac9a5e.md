# RxJS 课程:了解多播运营商

> 原文：<https://betterprogramming.pub/rxjs-lessons-understanding-multicasting-operators-9bd5d3ac9a5e>

## 多播并不像看起来那么难

![](img/76a2576d920b6d44190ee06fb9b89637.png)

乌古尔·阿克德米尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

说到 RxJS，多播可能是最令人生畏的特性之一。多播需要现有的 RxJS 知识库，这本身就是一些程序员难以掌握的一个方面。对此，我们补充说，有相当多的多播运营商，如果我们不了解它们是如何工作的，以及它们解决哪个特定的问题，知道选择哪一个可能是困难的。

这是 RxJS 多播运营商的入门指南。在本指南结束时，您将(希望)更好地理解这些操作符，并知道何时以及为什么需要使用它们。我们将从研究热/冷可观测概念开始，然后我们将研究所有多播运营商，讨论它们的特殊性并查看示例，直到我们完全理解它们。

我已经为我们将在本指南中看到的每一个示例创建了一个 StackBlitz 项目，这样您就可以摆弄代码片段了，因为，以我的拙见，这将大大有助于理解操作符是如何工作的。您可以在每个示例下面的标题中找到相应 StackBlitz 项目的链接。

在我们开始之前，这里有一个我们今天要讨论的所有运营商的列表:

*   [组播](https://rxjs.dev/api/operators/multicast)
*   [发布](https://rxjs.dev/api/operators/publish)
*   [发布回放](https://rxjs.dev/api/operators/publishReplay)
*   [发布最后一次](https://rxjs.dev/api/operators/publishLast)
*   [发布行为](https://rxjs.dev/api/operators/publishBehavior)
*   [引用计数](https://rxjs.dev/api/operators/refCount)
*   [分享](https://rxjs.dev/api/operators/share)
*   [分享回放](https://rxjs.dev/api/operators/shareReplay)

# 热和冷的可观测量

注意:如果你已经熟悉这些概念，可以跳过这一部分，继续下一部分。

默认情况下，大多数可观察物体都是冷的。每次我们订阅一个冷的可观察对象，它的生产者就被重新创建。那么这到底意味着什么呢？首先，我们必须理解生产者是什么:简而言之，它是我们可观察价值的来源。它可以是 DOM 事件、回调、HTTP 请求、迭代器等等。简而言之，任何可以产生价值并传递给观察者的东西。

现在我们知道了生产者是什么，就更容易理解前面的陈述，它基本上是说我们的可观察的生产者随着每个订阅被一遍又一遍地创建。这里有一个例子:

[https://gist . github . com/nya Garcia/ee 38 dfeadbd 39 ca 75950979719 be 9a 31](https://gist.github.com/NyaGarcia/ee38dfeadbd39ca75950979719be9a31)https://stackblitz.com/edit/cold-observable-example T2

如您所见，因为我们的可观察对象是冷的，并且它的生产者随着每个订阅被重新创建，副作用被执行两次，每个订阅一次。如果可观察物是热的，不管我们订阅多少次，副作用都只会发生一次。

在看到前面的代码后，有些人可能会认为这种行为产生的后果并不特别重要，这就是为什么我总是喜欢用 HTTP 请求作为例子来解释这个概念及其相当重要的意义。

假设我们有一个 Ajax Observable 为我们获取一些数据。因为 Ajax Observable 是冷的，所以每次我们订阅它时，都会产生一个新的 HTTP 请求。是的，您没有看错，每个订阅都有一个新的请求。20 个订阅= 20 个 HTTP 请求。让我们来看看一些代码:

https://stackblitz.com/edit/cold-observable-ajax-example?[file=index.html](https://gist.github.com/NyaGarcia/5e2978f151a5f5fe548eaee915e44a1b)

看到这里，我相信正确处理热/冷可观测量的重要性变得非常明显。在我们的代码中，我们肯定不希望在每次订阅时都重新创建生产者。那么，我们如何解决这个严重的问题呢？通过让我们的寒冷变热。我们如何做到这一点？多播运营商！所以，事不宜迟，让我们从这些操作符开始吧。

*注:有一篇*[*Ben Lesh*](https://medium.com/u/da6839d28258?source=post_page-----9bd5d3ac9a5e--------------------------------)*的精彩文章，深入探讨了热/冷可观的话题。你可以在这里找到*[](https://medium.com/@benlesh/hot-vs-cold-observables-f8094ed53339)**。**

# *多播()*

*`multicast`通过使用主题共享可观察到的源。让我们来看一个使用`multicast`的例子:*

*[https://gist . github . com/nya Garcia/433269 DAA 077 B4 BC 4d 9 a 90907 EDB 4298](https://gist.github.com/NyaGarcia/433269daa077b4bc4d9a90907edb4298)[https://stack blitz . com/edit/multicast-multicast-no-connect？file=index.ts](https://stackblitz.com/edit/multicasting-multicast-no-connect?file=index.ts)*

*你试过 StackBlitz 吗？注意到什么奇怪的事了吗？如果我们运行前面的代码，我们根本不会收到任何值！为什么我们的观测源没有发出任何东西？*

*因为组播会返回一种特殊的可观察值:T2。这种特殊类型的可观察对象有一个`connect()`方法，当它被调用时，负责订阅带有我们提供的主题的源可观察对象。*

*这意味着如果我们不打电话给`connect()`，这个来源将永远不会被订阅，也永远不会开始释放价值。所以，让我们改变我们之前的代码，添加一个对`connect()`的调用:*

*[https://gist . github . com/nya Garcia/6431815 f 7 E4 a 7 fa 2 a 7 CFB 2d 31 bebba 2e](https://gist.github.com/NyaGarcia/6431815f7e4a7fa2a7cfb2d31bebba2e)[https://stackblitz.com/edit/multicasting-multicast?file=index.ts](https://stackblitz.com/edit/multicasting-multicast?file=index.ts)*

**瞧！我们的代码现在正常工作了。由于`multicast`是共享源可观察的，副作用将只执行一次，即使我们订阅 1000 次。**

## *注销*

*与所有可观测量一样，取消订阅我们的多播可观测量以避免内存泄漏非常重要。我们需要记住，当处理返回`ConnectableObservable`的多播操作符时，我们需要取消订阅多播订阅。*

*让我们看看前面的代码片段，从负责结束我们的可观察对象的源代码中删除`take(2)`,然后取消订阅:*

*[https://gist . github . com/nya Garcia/e 02550 c 2915680 c8 da 7 be 0 e 5d 5d 8 BD 01](https://gist.github.com/NyaGarcia/e02550c2915680c8da7be0e5d5d8bd01)[https://stack blitz . com/edit/multicast-multicast-unsubscribe？file=index.ts](https://stackblitz.com/edit/multicasting-multicast-unsubscribe?file=index.ts)*

*成功避免内存泄漏！*

## *后期订阅者呢？*

*在一个完美的沙盒环境中，对多播可观察的所有订阅同时发生。然而，现实生活中发现这种行为的几率有多大？我可以向你保证它们不是很好。让我们看一个更现实的例子，我们有不同的订阅发生在不同的时间:*

*[https://gist . github . com/nya Garcia/c 708 E8 f 54 c 39113022 c 29 fa a9 EAC 390 b](https://gist.github.com/NyaGarcia/c708e8f54c39113022c29faa9eac390b)https://stackblitz.com/edit/multicasting-multicast*

*似乎我们的后期观察者错过了在它订阅多播可观察之前发出的值。这可能会导致意想不到的行为和试图找出原因时的大麻烦。*

*那么，如何才能解决这个问题呢？这其实很简单，我们所要做的就是用一个`[ReplaySuject](https://rxjs.dev/api/index/class/ReplaySubject)`代替常规的`Subject`。由于`ReplaySubject` s 向新订户重播旧值，我们的问题得到了有效解决:*

*[https://gist . github . com/nya Garcia/29 a3 DDD 98 F4 a6 ab 39 baace 10524459 EB](https://gist.github.com/NyaGarcia/29a3ddd98f4a6ab39baace10524459eb)[https://stack blitz . com/edit/multicast-multicast-replay subject](https://stackblitz.com/edit/multicasting-multicast-replaysubject)*

*我们的后期观察者现在可以访问以前发出的值。*

# *发布()*

*我们都同意`multicast`是一个令人惊讶的操作者，但是每次我们想要多播我们的流时都必须键入`multicast(() => new Subject())`会变得有点冗长。*

*`publish`接线员来救援了！`publish`基本上是用`multicast`加一个引擎盖下的主语，这样我们就不用费劲去打了。很酷，对吧？让我们来看一个例子:*

*[https://gist . github . com/nya Garcia/a 3a 21 BD 3 b 0 ea 87044 abf 234 EBD 2 ECA 11](https://gist.github.com/NyaGarcia/a3a21bd3b0ea87044abf234ebd2eca11)https://stackblitz.com/edit/multicasting-publish*

*记住，如果我们想订阅我们的 source Observable，我们仍然需要调用`connect()`！*

## *发布变体*

*还记得我们与后期订户和`multicast`的问题吗？在这种情况下，我们如何处理他们？由于`publish`相当于使用`multicast(() => new Subject())`，我们不能只手动将`Subject`换成一个`ReplaySubject`。幸运的是，publish 有几种变体，每种类型的主题都有一个。让我们来看看它们:*

## *发布重放()*

*`publishReplay`相当于`multicast(() => new ReplaySubject())`。由于我们已经看到了一个带有`multicast` + ReplaySubject 的例子，我们知道它允许后期订户在他们订阅之前接收排放:*

*[https://gist . github . com/nya Garcia/12 f1 afbf 3 efb 4545 a 485814967625682](https://gist.github.com/NyaGarcia/12f1afbf3efb4545a485814967625682)https://stackblitz.com/edit/multicasting-publishreplay*

## *publishLast()*

*`[publishLast](https://rxjs.dev/api/operators/publishLast)`相当于`multicast(() => new AsyncSubject())`。它将一直等到源可观测值发出最后一个值。这里有一个例子:*

*[https://gist . github . com/nya Garcia/c 10 e 80 C5 a6d 822d 764009 a 89 c 08 be 382](https://gist.github.com/NyaGarcia/c10e80c5a6d822d764009a89c08be382)https://stackblitz.com/edit/multicasting-publishlast*

## *发布行为()*

*`publishBehavior`相当于`multicast(() => new BehaviorSubject())`。由于它使用了`[BehaviorSubject](https://rxjs.dev/api/index/class/BehaviorSubject)`，`publishBehavior`允许我们指定一个初始值:*

*[https://gist . github . com/nya Garcia/c 281 CCA 3 f 8 b 0 f 45d 274 fa 7451 E8 d 460 f](https://gist.github.com/NyaGarcia/c281cca3f8b0f45d274fa7451e8d460f)[https://stackblitz.com/edit/multicasting-publishbehavior?file=index.ts](https://stackblitz.com/edit/multicasting-publishbehavior?file=index.ts)*

# *refCount()*

*我们现在知道有几个了不起的运营商分享我们的流。然而，不得不打电话给`connect()`很快就变老了。它很罗嗦，如果我们忘记调用它，我们可能会浪费时间去弄清楚为什么我们的观测不发射。那么，没有更好的替代方案吗？*

*当然有！请允许我介绍一下`refCount`操作员。`refCount`负责统计内部订阅源的数量，这为我们处理了两件至关重要的事情:*

*   *如果订阅数大于 0，也就是说至少有一个订阅者，`refCount`订阅(仅一次)源，调用`connect()`。*
*   *如果订阅的数量小于 1，也就是说没有任何订阅者，`refCount`取消订阅源。*

*让我们重构我们之前的代码来包含`refCount`:*

*https://stackblitz.com/edit/multicasting-refcount*

*如您所见，`refCount`负责调用`connect()`并为我们取消订阅源 Observable。*

# *共享()*

*最后但同样重要的是，我们有`share`操作符，它相当于使用`multicast(() => new Subject())` + `refCount`。它是最简单也是最常用的多播运营商，因为它可以处理所有的事情。这是我们之前的例子，使用了`share`:*

*[https://gist . github . com/nya Garcia/acbf 46915766768 a 26969 ca 324 c 76254](https://gist.github.com/NyaGarcia/acbf46915766768a26969ca324c76254)[https://stackblitz.com/edit/multicasting-share?file=index.ts](https://stackblitz.com/edit/multicasting-share?file=index.ts)*

*为了好玩，让我们来看一个稍微真实一点的例子，它的特点是共享的 Ajax 可观察对象，而不是枯燥的旧间隔:*

*[https://gist . github . com/nya Garcia/2fa 1 F8 f 97337 FD 9d 4419 f1 ea 3 f1 DDB 3 f](https://gist.github.com/NyaGarcia/2fa1f8f97337fd9d4419f1ea3f1ddb3f)[https://stackblitz.com/edit/multicasting-share-ajax?file=index.ts](https://stackblitz.com/edit/multicasting-share-ajax?file=index.ts)*

# *共享重播()*

*再次，我们必须记住我们的已故订户。在这种情况下，share 只有一个变体，`shareReplay`。可以想象，`shareReplay`相当于`multicast(() => new ReplaySubject())` + `refCount`。这里有一个例子:*

*[https://gist . github . com/nya Garcia/9e 919977 C2 DD 6120566 Fe 13510 be 6 DD 1](https://gist.github.com/NyaGarcia/9e919977c2dd6120566fe13510be6dd1)[https://stackblitz.com/edit/sharereplay-multicasting](https://stackblitz.com/edit/sharereplay-multicasting)*

# *包扎*

*   *`publish`相当于`multicast(() => new Subject())`。*
*   *`publishBehavior`相当于`multicast(() => new BehaviorSubject())`。*
*   *`publishLast`相当于`multicast(() => new AsyncSubject())`。*
*   *`publishReplay`相当于`multicast(() => new ReplaySubject())`。*
*   *有了`refCount`，我们不再需要手动调用`connect()`，也不再需要处理退订。*
*   *`share`相当于`multicast(() => new Subject()), refCount()`。*
*   *`shareReplay`相当于`multicast(() => new ReplaySubject()), refCount()`。*

# *结论*

*那都是乡亲们！我希望这篇文章能帮助你更好地理解多播运营商，并意识到多播并不像最初看起来那么难。*

*正如我前面提到的，我已经为本指南中的每一个例子创建了一个 StackBlitz 项目，所以请随意使用代码，我保证它真的有助于理解操作符是如何工作的。*

*如果你有任何问题，你可以在评论中提出来，我会尽力回答。*