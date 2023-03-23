# 如何在 RxJS 中创建可观测量

> 原文：<https://betterprogramming.pub/how-to-create-observables-in-rxjs-aa3bf79b05e0>

## 从各种数据类型、对象和事件中创建可观察值

![](img/6552df4e5fa6a7ffb9363f533653d1ef.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/observe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 介绍

RxJS 是 JavaScript 中最流行的函数式反应式编程(FRP)框架。Angular 完全围绕 RxJS 和 FRP 概念构建。这是一项神奇的技术，非常有用，但是它有一个主要缺点:它有一个有点陡峭的学习曲线，这让一些开发人员感到沮丧。

有鉴于此，我决定创建“RxJS 课程”系列，它将通过简单的例子涵盖 RxJS 提供的每一个操作符。以下是到目前为止该系列包含的部分:

*   RxJS 课程:如何创建可观测量

# 如何创造可观的

RxJS 为我们提供了许多创建可观测量的功能。事实上，数量如此之多，以至于一些开发者发现自己不知从何下手。在“RxJS 课程”系列的这一部分中，我们将涵盖所有可观察的创建函数，如下所示:

*   `of()`
*   `from()`
*   `fromEvent()`
*   `interval()`
*   `timer()`
*   `range()`
*   `defer()`
*   `ajax()`
*   `fromFetch()`
*   `generate()`

事不宜迟，让我们开始吧:

# 创建功能

## 的()'

`of()`允许我们从数组、对象、字符串等中创建可观察对象。它会将收到的每个参数作为一个整体发出，然后完成。让我们来看看如何使用它:

[用‘of()’创造可观测量](https://gist.github.com/NyaGarcia/34937b07f5014e2e9890acddde44825d)

这里我们用`of()`创建了四个不同的观察点:

*   从一系列数字中产生的`number$`可观察值
*   `pokemon$`可观察，由三个字符串创建
*   从两个字符串数组创建的`fruit$`可观察值
*   `iceCream$`可观察，由两个物体创建

那么，我们如何获取我们的可观察物将发出的价值呢？订阅它们，就像这样:

[订阅我们的 Observables](https://gist.github.com/NyaGarcia/0f9ad96869f4a574aae87bd60c56ce4f)

如您所见，我们可以订阅 Observable 来访问它发出的值。

**提示:**如果我们愿意，我们可以避免使用匿名函数(`=>`函数),而是这样做:

```
pokemon$.subscribe(console.log);// Output: Squirtle Charmander Bulbasur
```

我相信，如果您实际使用它们并实时查看它们的输出，会更容易理解可观测量及其工作原理，这就是为什么我为我们将在本教程中介绍的每个代码片段准备了一个 StackBlitz。因此，我建议您在[这一栈中使用`of()`](https://stackblitz.com/edit/creation-from?file=index.html)。

## 来自()'

`from()`从数组、承诺、对象、字符串等创建可观察对象。`of()`听起来很耳熟吧？那么它们之间有什么区别呢？

不同之处在于，`from()`发出的是它所接收的参数中的项目，而`of()`发出的是整个参数。这是什么意思？一旦我们订阅了一些用`from()`创建的可观测量，我们就会明白了。首先，让我们创造一些可观察的事物:

[使用“from()”创建可观察值](https://gist.github.com/NyaGarcia/425b9cc1e5f0a6ecb4da9b1823396bc3)

正如您所看到的，我们在上面的代码片段中创建了五个不同的观察点:

*   从字符串创建的`letters$`可观察值
*   从字符串数组创建的`fruit$`可观察值
*   从`Map`对象创建的`pokemon$`可观察对象
*   `promise$`可观察，由`Promise`创建
*   `node$`可观察，由`NodeList`创建

让我们订阅并看看这些可观测量的输出:

[订阅用“from()”创建的观察值](https://gist.github.com/NyaGarcia/08b2a1f37a4e20c3fa1a3029b542cd60)

是时候玩点区别了。让我们比较一下这两个`fruit$`观察值，它们是我们使用`from()`和`of()`函数从一个字符串数组中创建的:

[比较‘from()’和‘of()’的可观测量](https://gist.github.com/NyaGarcia/cb2b4ccb196da66c4021376373bc1c9e)

你能看出区别吗？我们现在可以看到,`from()`发出数组中的项目，而`of()`发出整个数组。我们可以说`from()`有一种扁平化的效果:它接受一个数组、一个字符串或一个对象，并发出它们所包含的项目。

你可以在[这个堆栈](https://stackblitz.com/edit/creation-from?file=index.ts)里玩`from()`。

## FromEvent()'

JavaScript 有许多类型的事件，让我们知道有趣的事情何时发生。使用`fromEvent()`，我们可以创建当这些事件被触发时发出的可观测信号。`fromEvent()`接收两个不同的参数:

*   事件处理函数将附加到的事件目标
*   我们想听的事件类型

我们将从四个不同的事件中创建四个不同的可观测量:`click`、`keydown`、`scroll`和`copy`:

“fromEvent()”可观察值

如果我们使用的四个事件中的任何一个是由用户与文档的交互触发的，那么相应的 Observable 将发出。发出的值会是什么？让我们来看看输出:

[订阅“fromEvent()”观察值](https://gist.github.com/NyaGarcia/2d42baeb2f286dcd0a5d75b477dfad5b)

正如您所看到的，我们正在接收一个事件对象，它包含许多我们可以使用的有用的事件属性。

但是我们的观察对象如何能够监听事件目标呢？我们不需要添加事件处理程序吗？

嗯，这就是`fromEvent()`的妙处。当我们订阅用`fromEvent()`创建的可观察对象时，一个事件处理函数会自动附加到所提供的事件目标上。这就是我们的可观察对象如何监听，以查看事件是否被触发。一旦我们取消订阅任何用`fromEvent()`创建的可观察对象，可观察对象就会为我们移除事件处理程序。很酷，对吧？

你可以在[这个堆栈](https://stackblitz.com/edit/creation-fromevent)里玩`fromEvent()`。

## 间隔()'

`interval()`创建一个可观察对象，它根据提供的时间间隔发出一系列升序数字。时间间隔大小参数是可选的，默认值为 0。对此我们必须非常小心。说到这里，让我们来看几个例子:

使用“interval()”创建可观察值

如您所知，`interval()`是一个非常简单的函数。我们唯一需要注意的是默认的间隔值是 0。

你可以在[这个堆栈](https://stackblitz.com/edit/creation-interval)里玩`interval()`。

## 计时器()'

`timer()`产生一个可观测值，在初始延迟后，开始根据规定的时间间隔发出一系列递增的数字。它与`interval()`非常相似，不同之处在于我们可以指定何时开始排放。`timer()`接收两个参数:

*   发出第一个值之前等待的初始延迟
*   排放之间的间隔值

这两个参数都是可选的，默认值为 0。

让我们来看一些例子:

[使用“timer()”创建可观察值](https://gist.github.com/NyaGarcia/e8bbb8d2b3fe1ad1315586fef4d514a9)

你可以在[这个堆栈](https://stackblitz.com/edit/creation-timer)里玩`timer()`。

## 范围()'

`range()`创建一个发出一系列递增序列号的可观察对象。它接收两个参数:

*   范围的起点
*   范围的长度——也就是将要发出的数字的数量。该参数是可选的，默认值为 0。

这里有一个例子:

使用“range()”创建可观察值

你可以在[这个 StackBlitz](https://stackblitz.com/edit/creation-range?file=index.ts) 里摆弄`range()`。

## defer()'

`defer()`让我们可以懒散地创造可观的事物。它一直等到我们订阅，然后执行可观察的创建逻辑。

这意味着每个订阅都会创建一个新的可观察对象。这听起来可能有点混乱，但是如果我们看一个例子，比较用`defer()`创建的可观察值和用其他创建函数创建的可观察值，这就很容易理解了。

让我们用`of()`创建一个可观察值:

[从一个函数创建一个可观察值，该函数返回一个带有‘of()’的随机值](https://gist.github.com/NyaGarcia/48bab36cb65c8ff0380cba55c09762ec)

正如你在例子中看到的，我们有一个`getRandomPokemon()`函数，正如它的名字所示，返回一个随机的神奇宝贝。我们已经使用`of()`从`getRandomPokemon()`函数中创建了一个`randomPokemon$`可观察值。然而，当我们订阅这个可观测的，我们总是得到相同的值:`Charmander`。难道我们每次订阅的时候不应该得到一个随机的神奇宝贝吗？答案是否定的，为什么？

因为`getRandomPokemon()`函数是在可观察对象创建时执行的，而不是在我们订阅时执行的。这意味着它只执行一次——当我们创建可观察对象时。不管我们订阅多少次，可观察的东西已经被创造出来了，所以我们总是得到同样的神奇宝贝。

基本上，我们订阅了三次相同的可观测值，它发出`Charmander`。那么如何才能让`getRandomPokemon()`函数在每次订阅时执行，而不是在创建可观察对象时执行呢？通过使用`defer()`:

[使用“defer()”从返回随机值的函数中创建可观察值](https://gist.github.com/NyaGarcia/2fb6f2232115babe23a475e6fbff72a2)

我们已经使用来自`getRandomPokemon()`函数的`defer()`创建了`randomPokemonForReal$`可观察值。正如你在上面的例子中看到的，每次我们订阅它，我们都会获得一个不同的神奇宝贝。就像我们之前说过的，这是因为可观察的创建逻辑(在本例中，是`getRandomPokemon()`函数)是在每次订阅时执行的。

综上所述，需要懒人可观测量的时候可以用`defer()`。你可以在[这个堆栈](https://stackblitz.com/edit/creation-defer?file=index.ts)里玩`defer()`。

## ajax()'

顾名思义，`ajax()`函数从 AJAX 请求中创建一个可观察对象。让我们来看看如何使用它:

**注意:**我们将使用 [Ghibli API](https://ghibliapi.herokuapp.com) 来测试我们的 AJAX 调用。

['ajax()'示例](https://gist.github.com/NyaGarcia/1a3d806aea24f78b52a12708f611ecc8)

当我们用`ajax()`创建一个可观察对象时，它将发出从 API 调用返回的整个`AjaxResponse`对象。但是如果我们只对获取返回的数据感兴趣呢？嗯，原来`ajax()`有一个叫做`getJSON()`的简洁的小功能:

[‘get JSON’示例](https://gist.github.com/NyaGarcia/22d31ea2007dd8228b39e9aea17623e1)

不错！我们已经得到了我们需要的数据。然而，另一个问题出现了:如果我们需要设置自定义头怎么办？没问题，我们可以通过向`ajax()`传递一个对象来设置它们:

[为“ajax()”设置自定义标题](https://gist.github.com/NyaGarcia/fa3d9ff16d12bbd8065c5b55ae74cf6e)

瞧啊！我们现在有了一个用`ajax()`创建的可观察对象，带有自定义标题。你可以在[这个堆栈](https://stackblitz.com/edit/creation-ajax?file=index.ts)里玩`ajax()`。

## fromFetch()'

AJAX 请求很好，但是如果我们想使用`fetch`呢？没问题，`fromFetch()`允许我们这么做。让我们来看一个例子:

[使用“fromFetch()”创建可观察对象](https://gist.github.com/NyaGarcia/d878328b867ea85269a32987226332a4)

如您所见，就像使用`ajax()`一样，Observable 返回整个`Response`对象。如果我们只是想要数据呢？与`ajax()`不同，`fromFetch()`没有`getJSON()`功能。那么我们如何获取数据呢？通过做这样的事情:

[使用“切换图”获取数据](https://gist.github.com/NyaGarcia/dea1b69a708c4ed08f9110d61928f0af)

错误处理呢——因为对于 HTTP 请求，很多事情都可能出错。以下是如何处理错误的示例:

[使用‘from fetch()’进行错误处理](https://gist.github.com/NyaGarcia/866292edb3283c49b1e31f9fdd55471d)

我知道这是相当多的代码，所以让我们把它分解一下:

*   首先，我们使用`switchMap()`操作符并检查响应是否正常。通过使用 guard 子句，如果响应不正常，我们将抛出一个错误。如果没有出错，我们使用`json()`函数从响应中返回 JSON 主体内容。
*   然后，我们使用`catchError()`操作符来捕捉错误(万一发生错误)并打印出来。然后，我们返回一个对象的可观察值，包含错误消息和一个错误属性，设置为`true`。
*   最后，我们订阅。如果`fetch`调用顺利，我们将收到响应的数据。如果没有，我们将收到一个错误对象，以及出错的消息。

你可以在[这个堆栈](https://stackblitz.com/edit/creation-fromfetch)里玩`fromFetch()`。

## '生成()'

我想把`generate()`献给所有的 for loop 爱好者。`generate()`将通过运行一个状态驱动的循环来创建一个可观察的序列，该循环将产生序列中的元素。它接收三个不同的参数:

*   `initialState`:确定循环的起点，初始值
*   `condition`:决定循环结束条件的函数。它接收当前值并检查是否满足指定的条件。如果是，则循环继续。否则，循环结束。
*   `iterate`:决定每次迭代时如何修改定义值的函数。

这听起来可能有点复杂，但一旦你看到它，就很容易理解了:

使用“generate()”创建可观察对象

第一个参数是`initialState`值。如你所见，我们循环的第一个值是`1`。

第二个参数是条件函数—只要当前值低于`10`，我们的循环就会继续。

最后，第三个参数对应于每次交互时当前值将如何被修改。在这种情况下，它将增加`1`，这意味着我们的`number$`可观测对象将发出从 1 到 9 的数字。

然而，将匿名函数传递给`generate()`看起来有点奇怪，不是吗？如果我们喜欢这种语法，我们可以传递一个对象。这里有一个[的例子](https://gist.github.com/NyaGarcia/73d7de00cf78c100de53888ce2e8bb1b)。

如你所见，我们的`evenNumber$`可观测体将发射 2 到 10 之间的所有偶数。

**警告:**向`generate()`传递一个对象允许我们省略条件变量。如果我们这样做，我们将创建一个无休止的循环，所以要非常小心。

这是一个无限循环的例子。不要这样做:

[‘generate()’死循环](https://gist.github.com/NyaGarcia/97d58a1d36c3d2ef7eb920fde0b4edb6)

你可以在[这堆](https://stackblitz.com/edit/creation-generate)里玩`generate()`。

# 结论

那都是乡亲们！我们已经介绍了 RxJS 为可观察创建提供的每一个功能。正如你所看到的，有很多方法可以创造可观测量，尽管并不是所有的方法都被广泛使用。

知道如何创建一个可观察对象对于开始我们的 RxJS 之旅至关重要，但是 RxJS 的真正力量来自于可管道操作符的流操作。我们将在“RxJS 课程”系列的后续教程中介绍所有现有的 RxJS 运算符。

我希望你喜欢这个教程，并发现它很有用。干杯。