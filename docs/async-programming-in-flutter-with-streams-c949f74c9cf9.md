# 用流进行 Flutter 中的异步编程

> 原文：<https://betterprogramming.pub/async-programming-in-flutter-with-streams-c949f74c9cf9>

## 部分代码的独立工作流

![](img/59cd3f60fcca517ac54f09124a9b9e9a.png)

一语双关|照片由亨德里克·科内里森在 [Unsplash](https://unsplash.com/search/photos/stream?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

异步编程的原则是允许一部分程序运行，同时等待应用程序线程中的其他活动发生。它允许部分代码独立于主工作流运行。

异步进程的几个例子是:从网络上获取数据，或者在一些非常大的数据块上循环。

在 flutter 中，有一系列方法可以编写异步代码。一些流行的方法有:

*   未来。
*   异步/等待。
*   溪流。

这篇文章的目的是让你开始在你的 flutter 应用程序中使用 streams，我希望你有编写 flutter 应用程序的先验知识，让我们开始吧！

# 什么是反应式编程？

据[维基百科](https://en.wikipedia.org/wiki/Reactive_programming):

> *反应式编程是一种* [*编程范式*](https://en.wikipedia.org/wiki/Programming_paradigm) *面向围绕* [*数据流*](https://en.wikipedia.org/wiki/Dataflow_programming) *而变化的传播。这意味着应该可以用所使用的编程语言轻松地表达静态或动态数据流，并且底层执行模型将自动通过数据流传播更改。*

在反应式编程中，数据在由您使用的语言、框架或库设置的结构中流动，当*数据*在一端被传入结构*时，如果该*数据的值改变*，输出将总是*更新自身*。*

这正是流所做的。流是异步事件的流动，将其视为包含液体的管道。你从管子的一端注入液体，液体从另一端流出。

流可以携带和处理不同类型的数据，比如对象、函数、地图、列表，甚至是流的流。很酷吧？

# 现在，代码示例

第一个示例创建了一个简单的流，它接收一个字符串，并在完成后将其打印出来:

```
***@override******void*** *initState() {**super.initState();****Stream****<String> stream = new* ***Stream****.fromFuture(inputData());****stream****.listen((data) {**print("Our data: " + data);**}, onDone: () {**print("Done");**}, onError: (error) {**print("Error returned");**});**}****Future****<String> inputData()* ***async*** *{**print("Fetching Data...");****return*** *"Let's Use Streams!";**}*
```

## 输出

获取数据…
我们的数据:让我们使用流！完成了

这里，流从返回文本`“Let’s use streams!”` ***的`inputData()`函数获取数据。*** 然后我们使用`stream.listen()` 对进入数据流的数据进行处理。在这里我们有一个*`onDone()`**`onError()`*的方法。`onDone()`在流程完成时使用数据，如果有错误可以用`onError()`抛出。****

# ***第二个例子***

```
***final **StreamController** controller= **StreamController**();controller.stream.**listen**((data) {print("received data: $*data*");}, ***onDone***: () {print("Stream done");}, ***onError***: () {print("Error occured");});*// data flowing into the stream*controller.sink.**add**('stream controllers are awesome');controller.sink.**add**("Because You can do more");controller.sink.**add**('random string');*// Close the StreamController when done to avoid memory leaks*controller.***close***();***
```

## **输出**

**接收数据:流控制器很棒
接收数据:因为你可以做更多
接收数据:随机串
流完成**

**`StreamController` 类为任何创建的流创建一个控制器。使您更容易使用多个侦听器(我们很快会谈到这一点)。这里我们使用`[sink](https://api.flutter.dev/flutter/dart-async/StreamSink-class.html).add()` **，**添加数据，这是一个接受输入到流中的数据的对象。还有，你应该在使用完控制器后*关闭* ，以防止[内存泄露](https://en.wikipedia.org/wiki/Memory_leak)。**

# **广播流**

**这种类型的流允许您使用多个侦听器。我们之前创建的流只能有一个侦听器；他们被称为*单一订阅流。***

## **代码示例**

```
*****@override***void initState() {super.initState();*stream*();}final **StreamController** *controller* = **StreamController**.broadcast(); *//add a .broadcast()* *stream*() {*controller*.stream.*where*((data) => (data is **String**)).*listen*((data) {print("DataReceived: " + data);}, onDone: () {print("Task 1 Done");}, onError: (error) {print("Some Error");});*controller*.stream.where((data) => (data is **int**)).listen((data) {print("DataReceived: " + data.toString());}, onDone: () {print("Task 2 Done");});*controller*.stream.where((data) => (data is **Map**)).listen((data) {print(data);}, onDone: () {print("Task 3 Done");}); *controller*.sink.add('random string');*controller*.sink.add(1234);*controller*.sink.add({'key 1': 'value A', 'key 2': 'value B'});*controller*.close();}**
```

## **输出**

> **接收的数据:随机字符串**
> 
> **接收数据:1234**
> 
> **{关键字 1:值 A，关键字 2:值 B}**
> 
> **任务 1 完成**
> 
> **任务 2 完成**
> 
> **任务 3 完成**

**在这里，我们用`.broadcast()` 初始化一个`StreamController` 然后，我们听三次不同的流。注意我们在监听之前使用了一个`.where()` 方法，这有助于我只监听流中满足给定条件的数据。首先，我们只打印字符串，然后是整数，然后是映射。**

**现在你知道如何创建和使用一个流了。我会写一篇单独的文章，在那里我们可以使用流在我们的屏幕上用一个真正的工作应用程序来绘制小部件！**

**你可以在推特上找到我。**