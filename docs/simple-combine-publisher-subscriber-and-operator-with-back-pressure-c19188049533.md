# 一个简单的结合了发布者、订阅者和操作者的反压力

> 原文：<https://betterprogramming.pub/simple-combine-publisher-subscriber-and-operator-with-back-pressure-c19188049533>

## 让我们学习联合收割机

![](img/81ae576e18e2ac60d93af696fe48d8e7.png)

[田宽](https://unsplash.com/@realaxer)在 [Unsplash](https://unsplash.com) 上拍照

# 摘要

看了很多`Combine`的教程和深度治疗——花了很长时间才得到它。

苹果给了我们各种组件的协议定义和一个包含许多预建组件的框架——但不幸的是还没有示例代码。

所以我开始着手一个项目，构建一个简单的、最基本的`Publisher`、`Subscriber`、`Publisher-Subscriber`(我的术语，在这里我称之为`Complex Publisher`)和`Operator`。

我还想探索`backpressure`是如何工作的，在我自己的代码中支持它，并寻找任何有问题的边缘情况(我确实找到了一些)。

我将首先展示一个简单的 UNIX shell 管道，然后使用`Combine`重新构建它。您将看到`Combine`的`backpressure`是如何工作的，以及如何改编一个`Complex Publisher`来构造一个`Operator`。

# 介绍

几十年前我开始接触 C 和 UNIX。2005 年，我努力理解 Objective C 的面向对象编程。然后，去年夏天，苹果推出了利用函数式反应编程概念的`SwiftUI`和`Combine`。似曾相识的感觉又来了！

浏览 Medium 和其他地方，我发现了许多做复杂事情的复杂例子，但是它们太多了！我想要一些非常简单的东西，这样我就可以把我的头脑包裹在各种各样的概念中。没有找到任何东西，我决定自己创造一些。

还有另一个`Combine`组件——`Subjects`——您可以通过编程来控制它。他们得到了大量的报道，所以我不会提到他们。下面是普通的旧 Swift 代码。

在许多方面，`Combine`网络看起来像 [UNIX 管道](https://toroid.org/unix-pipe-implementation)。第一个组件(`Publisher`)生成数据并将其发送到下游。中间组件(`Operators`)在转发输入数据之前对其进行修改。然后，最终组件(`Subscriber`)接收最终结果。`Combine`支持更复杂的图形，但这是基本的。

在 UNIX 中，这里有一个非常简单的 shell 管道:

```
echo "Hello World" | tr "[:lower:]" "[:upper:]" | awk -v FS="" '{
    for (i=1;i<=NF;i++) {
        if(i == 1) {
            printf "Chars: " $i
        }  else {
            printf " "$i
        }
    }
}'
```

`Echo`是`Publisher` (字符串的)`tr`是`Operator`(将小写转换成大写)`awk`是`Subscriber`(打印每个字符，用空格隔开)*。*

输入:`Hello World`

输出:`Chars: H E L L O W O R L D`

我们将使用`Combine`元素来复制它。两者都支持`backpressure`，这意味着`Operators`和`Subscribers`可以节流上游`Publishers` *—* UNIX `pipes`本身提供了这一点(而不是单独的程序)。`Combine`通过限制`Publishers`仅输出达到`Subscriber`指定限制的数据来协同工作。

有一件事我花了很长时间才明白:虽然一个简单的`Publisher`只产生发送给下游的数据，但是一个`Complex Publisher`(我的术语)也提供了一个`Subscriber`接口来连接到上游的`Publisher`。

然后就是`Operator`。`Operator`是`Publisher`扩展中的一个普通函数，它可以包装`*Complex Publisher*`(或`Subscriber`)，从而允许它在函数链中使用。

我还没有讨论错误和取消(有很多文章涉及这些主题)——但是完整的代码(在 [Github](https://github.com/dhoerl/MediumCombineExamples) 上)支持取消，如果`String`参数为空，就会生成一个错误。

# 简单发布者

我见过的大多数例子都使用一个`struct`来符合`Publisher`协议，但是一个`Class`也应该可以。我们的`Publisher`示例将模仿`echo`*“Hello World”事情是这样开始的:*

*`Publishers`是通用的，所以它们有关联的类型`Output`和`Failure`，如上图所示。通过提供一个`String`(即`StringPublisher(string: “Hello World”)`)来创建一个`StringPublisher`。*

*一旦创建，在`Subscriber`通过`receive`函数订阅它之前不会发生任何事情。注意`Subscriber`的输入必须与`Publisher`的输出相匹配。*

*我见过的所有自定义`*Publisher*`示例都使用一个类对象来创建一个自定义`Subscription`，这就是我们在这里要做的:*

*它需要知道`subscriber`和数据，因为它将在稍后被请求时发送该数据。它可以用任意多的参数初始化——对于这个简单的`Publisher`,它只是将`String`变形为一个`Character`数组。这个对象也可以是一个`NSObject`子类，这使得它有可能成为基于 Objective C 的对象的代表。*

*你可以在发布者内部编写你的类，这是一种流行的技术。*

## *需求*

*`Subscription`有一个`receive`函数，下游的`Subscriber`用它来请求数据。然而，对于一定量的数据，该函数只获得一个`Demand`(即`request`)。`Demand`很有趣，因为它要么是`infinite`要么是`Int`—`Int`可能意味着一个字节、一个对象或整个文件。*

*看起来每个`demand`应该是在之前请求的数据之外减去发送的任何数据，尽管苹果的文档并没有真正说明这一点。因此，每个`receive`的`demand`被加到一个累计`demand`上，并且每次`Subscription`发送数据时，累计`demand`被减少相同的发送量。*

*数据通过`receive`功能发送到`Subscriber`。该函数返回一个`demand`，苹果称其为*还需要多少个元素*。理论上，在我看来，它应该与该时刻计算的累计值`demand`相同。*

*`Demand`有一个`max`属性，当`demand`为`infinite`时为`nil`。我见过的大部分代码都假设了这一点，并且没有限制输出。然而，`Combine`文档强调`Publishers`决不能发送超过请求的数据。这里所有的*订阅者*只请求一个元素，这样我们就可以探究代码的含义。*

## *通过*发送数据，接收**

*当一个`Subscription`有一个非零`demand`并且它有或接收数据时，它应该异步发送它，直到未完成的`demand`。因此，现实世界中的`Subscription`必须有某种弹性缓冲区(如果`demand`是无限的，那就没有意义了，但是任何给定的`Publisher`都不知道会被请求什么)，除非你认为你的数据可能会被丢弃(下面会进一步讨论)。*

*这个架构提出了一个有趣的问题，即`Subscription`应该何时以及如何发送数据。想象一下它有一个巨大的数据量——为 100 个元素获取一个`request`,然后发送 100 个元素。在其`receive`功能中，`Subscriber`请求 100 个以上，等等。我个人认为，代码需要考虑到这一点，而不是在堆栈上无限递归。*

## *简单接收*

*在示例`Publisher`中，直接发送`Subscriber`消息:*

```
*func request(_ demand: Subscribers.Demand) {
 guard !isFinished else { return }
 guard let subscriber = subscriber else { return }
 guard data.count > 0 else {return sendError(.inputStringWasEmpty)} runningDemand += demand*
```

*在确保我们可以继续后，`runningDemand`增加了`demand`参数(显然如果原来是`infinite`，这没有影响):*

```
*if isProcessingRequest == true {
 return
} else {
 isProcessingRequest = true
}*
```

*如果`Subscriber`在`receive`消息中用另一个请求发送消息，上面的测试可以防止递归:*

*while 循环的存在是因为`Subscriber`可以在`receive`内部发送`request`。既然它可能做到了这一点(并增加了`runningDemand` *)，*我们就这样循环下去，直到供需平衡。*

*一旦`Subscription` *的*数据用完，则`subscriber`被告知`Subscription`已经完成。下面是完整的代码:*

## *试验*

*我们可以使用一个内置的`sink Subscriber`来测试我们的`Publisher`，它需要无限个元素:*

*输出:`Char: H e l l o W o r l d FINISHED`*

# *订户*

*这个简单的`Subscriber`将请求一个单字符，然后，在每个`*receive request*`、*、*内多一个:*

## *试验*

*我们现在可以连接`Publisher`和`Subscriber`:*

```
*let publisher = StringPublisher(string: "Hello World")
let subcriber = StringSubscriber()
publisher.subscribe(subcriber)*
```

*输出:`Char: H e l l o W o r l d Subscriber completion finished`*

# *复杂发布者(即订阅者-发布者)*

*我们现在要创建一个同时满足`Publisher`和`Subscriber`协议的组件。这就有点棘手了。*

*这段代码归功于[马特·加拉格尔](https://www.cocoawithlove.com/about/)，因为我遵循了他出色的`[Combine](https://www.cocoawithlove.com/blog/twenty-two-short-tests-of-combine-part-1.html)`文章中列出的步骤(参见*自定义实现*)。*

*这个`Publisher`产生的`Subscription`也必须采用`Subscriber` 协议:*

*你可以看到我们需要为`UpperCasePublisher`提供上游`Publisher`。它维护该引用，直到收到来自下游的订阅请求。该`receive`消息提示它创建一个`subscription`对象，并将其传递给上游的`Publisher`。此时，`UpperCasePublisher`结构可以被丢弃，因为`Publisher`只存在很短的时间，直到它们产生一个`Subscription`。*

*`UpperCaseSubscription`本质上合并了已经呈现的`Subscription`和`Subscriber`代码。它需要适应需求超过供应的情况，反之亦然，所以它有点复杂:*

*您可以看到它同时具有`Subscriber`和`Publisher`引用(ivars):*

*上面最大的不同是`data`可以在上游`Publisher`还在运行的时候清空——所以我们需要等待，直到它向我们发出它已经完成的信号。*

*这里最大的补充是，在`receive`中，数据现在从小写转换成大写。同样，在处理和转发接收到的数据后，它返回一个`1`的`demand`。*

## *试验*

*现在，我们已经具备了建立像 UNIX 命令行这样的链所需的所有条件:*

```
*let p1 = StringPublisher(string: "Hello World")
let p2 = UpperCasePublisher(upstream: p1.eraseToAnyPublisher())let subscriber = StringSubscriber()
p2.subscribe(subscriber)*
```

*输出: `Char: H e l l o W o r l d Subscriber completion finished`*

# *操作员*

*我很快就理解了`Publisher`和`Subscriber`。我*有点得到了*一个`Complex Publisher`一个*的概念。*但是我真的很纠结`Operator`。*

*你会读到一个`Operator`是一个可以组合成一个`Publisher-Subscriber`的函数，但是我很难将它作为一个函数应用到另一个`Publisher`中。*

*最后看着别人的代码，突然*得到了*它。该功能只是一种利用实际`Complex Publisher`的机制。根据定义，该函数是对`Publisher`的扩展，因此它可以应用于任何发布者(给定*关联类型*约束)。*

*所以让我们为上面的`UpperCasePublish` *er* 创建一个`Operator`:*

```
*extension Publisher where Output == [Character], Failure == Error {
 func toUpper() -> AnyPublisher<Output, Failure> {
  let p2 = UpperCasePublisher(upstream: self.eraseToAnyPublisher())
  return p2.eraseToAnyPublisher()
 }
}*
```

*使用中:*

```
*let p2 = StringPublisher(string: "Hello World").toUpper()
let subscriber = StringSubscriber()
p2.subscribe(subscriber)*
```

*或者，使用`sink`:*

```
*var count = 0
let _ = StringPublisher(string: "Hello World")
.toUpper()
.sink(
 receiveCompletion: { completion in
  switch completion {
  case .failure(let err):
   print("\nERROR: ", err)
  case .finished:
   print("\nFINISHED")
  }
 }) { (chars: [Character]) in
  chars.forEach({ print(count == 0 ? "Char:" : " ", $0, terminator: ""); count += 1 })
 }*
```

*这将输出与先前测试完全相同的文本*

# *背压，重新访问*

*我发现了三个关于反压力的讨论，它们对于`Publishers`应该如何对待两种类型的`demand` ( `receive`消息，从`request`返回`demand`)都有略微不同的看法。我对此没有什么特别的见解，除了我对苹果的少量文档的看法。*

*   *[供需](https://www.cocoawithlove.com/blog/twenty-two-short-tests-of-combine-part-3.html)，马特·加拉格尔，来自 2019 年 8 月*
*   *[背压](https://heckj.github.io/swiftui-notes/)，乔·赫克，2020 年 3 月*
*   *[应对背压](https://www.apeth.com/UnderstandingCombine/publishers/publisherscustom.html)，马特·纽堡，2020 年 3 月*
*   *[了解联合收割机的出版商和订户](https://www.donnywals.com/understanding-combines-publishers-and-subscribers/)，唐尼·瓦尔斯*

*马特·加拉格尔说如果没有当前的`demand`，那么`Publishers`应该丢弃数据，作为一个来自 UNIX 世界的开发人员，这是我永远无法接受的(注意我的代码提供了一个弹性缓冲)。也就是说，维护一个连接上游`Publisher`和下游`Subscriber`的缓冲区，就像许多`Operators`所做的那样，提出了许多关于线程同步的问题，我希望在以后的文章中探讨这些问题。*

# *结论*

*我喜欢`Combine`并且每天都在继续学习。我正在升级我的几个开源项目，这些项目目前支持`Steams`来提供`Combine`接口。我为本文编写的代码对我个人更好地理解`Combine`，尤其是`Complex Publisher`和`Operator`概念有很大的帮助。再次感谢马特·加拉格尔！*

*自从我开始写这篇文章以来，已经出现了许多其他的文章，它们也涵盖了用`back pressure`编写定制的`Combine`组件。如果你想深入研究，我在下面添加了一些链接。*

*以上所有代码都可以在一个 [Github 游乐场](https://github.com/dhoerl/MediumCombineExamples)找到。*

*非常感谢你的阅读——我希望你能从中得到一些有用的东西！*

# *乐章结尾部*

*关于使用`Combine`和编写`Combine`组件的文章太多了，我无法一一列举(而且列表每天都在变长！).也就是说，我想重点介绍一些之前提到过的:*

*   *[“联合收割机简介](https://www.apeth.com/UnderstandingCombine/start.html)”作者 Matt Neuburg。Matt 已经写了非常有用的苹果文档，追溯到 *AppleScript* ，以及最近覆盖每个 iOS 版本的大部头(刚刚出版:[编程 iOS 13:深入研究视图、视图控制器和框架](https://www.amazon.com/Programming-iOS-13-Controllers-Frameworks/dp/1492074616))。这些书中的讨论确实帮助我理解了困难和复杂的主题，我怎么推荐他的作品都不为过！*
*   *[“联合收割机的 22 项简短测试](https://www.cocoawithlove.com/blog/twenty-two-short-tests-of-combine-part-1.html)”作者 Matt Gallaher。为了更好地理解`Combine`，Matt 编写了一系列测试来探索它的内部结构。虽然这三篇文章读起来很慢，但它们提供了对`Combine`的方法和原因的真正见解。*
*   *乔·赫克的《联合收割机的使用》。乔的书是我第一次深入了解`Combine`。虽然它更侧重于使用，但也有许多关于内部的见解。*