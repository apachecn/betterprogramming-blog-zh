# 比较领先的 JavaScript 函数式反应流库

> 原文：<https://betterprogramming.pub/compare-leading-javascript-functional-reactive-stream-libraries-544163c1ded6>

## RxJS、@most/core 和 XStream

![](img/8ebe4b07e79478d504f249db9baafcda.png)

照片由[奎诺·阿尔](https://unsplash.com/@quinoal?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 关于功能反应流

> *“反应式编程是一种声明式编程范式，关注数据流和变化的传播[1]。”—* [维基百科](https://en.wikipedia.org/wiki/Reactive_programming)

每个反应式编程应用程序的基础都有一个库，它提供数据结构和操作符来组成模拟解决方案的数据流。

这些库通常被称为*流库*，其特征在于不同的因素。让我们看看其中的一些。

# 推/拉

在基于推的行为中，程序图准备好了，并且不断地监听新的输入数据。例如，DOM 元素上的事件侦听器就是这种情况:当新的输入事件发生时，相对回调就会运行。

相比之下，对于基于拉的输入，它在需要时被读取。这通常发生在连续变化的值上，例如，当我们调用一个返回当前时间的函数时。

功能反应库通常专注于推，但也有实验扩展到支持拉。(例如，参见[主动行为](https://github.com/funkia/hareactive)【2】和 [Xstream 信号](https://github.com/cyclejs/cyclejs/issues/581)【3】)。)

# 冷/热流

反应式库为构建我们的应用程序业务图提供了基础。当图形开始运行输入数据时，生成器连接并监听输入事件。

这里有两种口味。如果在将侦听器添加到流(或相关流)时，在流内部创建了数据生成器，则该流被定义为冷流。这通常意味着流在默认情况下是单播的，并且每个连接的侦听器都会获得不同的生成器实例化。

当生产者被创建在流之外时，我们称之为热流。这种情况通常是多播，许多流可以监听同一个共享数据源。为了得到更多的解释，我把戴夫·塞克斯顿关于热/冷现象的文章转发给你。

# 隐式/显式时间建模

如前所述，反应库的基本功能是实现应用程序数据图，该数据图对输入数据做出反应，并随着时间的推移产生所需的输出。

时间概念可以用输入数据推送时间和函数调用顺序显式表示。或者，以一种更含蓄的方式，为每个输入数据创建一个元组，将它与一个时间号相关联。

# 同步/异步启动

在应用程序图被声明和实例化之后，需要调用一个方法来开始观察输入事件。

有两种策略。同步启动时，通常会在流上调用一个`addListener`方法。该方法同步向后运行网络，在到达输入源时创建并观察输入源。

通常，在同步启动的应用程序中，当应用程序需要观察新的数据源时，我们会在不同的时间点发现许多对`addListener`方法的调用。

在异步启动中，通常每个应用程序只有一个运行效果函数调用。所有的流(现有的和未来的)合并/合成为这个单一的最终流。这种启动方法通常是异步的，所有流都等待处理输入事件，直到整个网络预热。

让我们介绍一下竞争者…

# RxJS:可观察的 Boss

![](img/89d715e4eda0f79dfd09491b1c8d3b8c.png)

可能是 JavaScript 生态系统中最成熟、最常用的反应式 lib。

*   仓库:【https://github.com/ReactiveX/rxjs】T2。
*   当前版本:6.3.3。
*   星星和所用:2.1K 2m。
*   类型支持:本机类型脚本。
*   推/拉:推。
*   冷/热:热(能支持冷)。
*   隐式/显式时间:隐式。
*   API 样式:静态或由管道点状。
*   原生 ES6 模块支持:不支持(在 v.7 中支持，目前在 alpha 中支持。)

# XStream:循环之王

![](img/e21816f3d84cd6b6a4ad145e9fdf2327.png)

为了更好地解决 [Cycle.js](https://cycle.js.org/) 的特定功能需求，它可以被认为是来自 [André Staltz](https://medium.com/u/3ba9db42812d?source=post_page-----544163c1ded6--------------------------------) 的 RxJS 的精简版和自以为是的版本。

*   知识库:[https://github.com/staltz/xstream](https://github.com/staltz/xstream)。
*   当前版本:11.11.0。
*   星星:1.9K。
*   类型支持:本机类型脚本。
*   推/拉:推。
*   冷/热:热。
*   隐式/显式时间:隐式。
*   API 风格:以点为主。
*   原生 ES6 模块支持:否

# @most/core:新城

![](img/8a36e29eba138b1a5b451dd1fe2947f1.png)

来自 Brian Cavalier 的 FRP 一元流工具包的新化身。

*   知识库:[https://github.com/mostjs/core](https://github.com/mostjs/core)。
*   当前版本:1.4.1。
*   星星:223(主项目上 3.1K)。
*   类型支持:流和类型脚本定义。
*   推/拉:推。
*   冷/热:热。
*   隐式/显式时间:显式。
*   API 风格:静态。
*   原生 ES6 模块支持:是。

# 基准应用程序

为了进行基准测试，用三个库复制了一个简单的图形网络。

每个版本都在独立的 ES6 模块中实现。对于每一个输入脉冲，网络都会生成一个待办事项列表。每个项目都有一个旧项目的副本(旧项目每四个元素更新一次)。

该网络非常简单，但使用了三个 libs 的一些主要运营商。

该网络的另一个特点是存在一个“钻石”。不建议使用菱形流，因为它会在执行过程中[导致故障](https://staltz.com/rx-glitches-arent-actually-a-problem.html)。在演示中，钻石被自愿引入，以比较三个 lib 的反应。

简而言之，[最](https://github.com/mostjs/core)，其明确的时间表示和异步启动是最直观和抗故障的。对于另外两个库，有必要强制一个异步行为，添加一个`setTimeout`以使菱形网络按预期工作。

让我们看看三个版本的核心加上一个命令式的传统版本:

命令式繁体版本

RxJS 版本

@ most/核心版

XStream 版本

# 速度基准

[Benchmark.js](https://benchmarkjs.com/) 用于比较三个库的速度。

每秒操作数(越高越好)

*   @most/core — 60.83 操作/秒 2.60% (72 次运行采样)。
*   XStream — 54.59 操作/秒 1.46%(采样了 81 次运行)。
*   RxJS — 51.58 操作/秒 2.59% (80 次运行采样)。

最快的是@most/core。18.40s 完成，纱速 17.22s，用户 0.58s，系统 93% cpu，总计 18.955。

所有三个 lib 都非常有性能。Most/core 以最佳每秒运算值胜出。

# 内存基准

内存管理与速度同等重要，甚至更重要。在现实世界的应用程序中，我们将拥有成千上万的流节点。正确有效的内存管理将是基础。

为了测量内存压力，Node 以一个暴露的[垃圾收集器 API](https://nodesource.com/blog/request-garbage-collection-for-node-js-processes-with-n-solid/) 启动，并且使用 [Airbnb Memwatch](https://github.com/HolgerFrank/node-memwatch) 库来跟踪堆使用情况。

这些库甚至与该算法的强制性变异“传统”版本进行了比较。

进行了两种不同的测试。一个递归，一个基于承诺。

# 递归记忆测验

基于递归的内存测试应用

给出了两个图表，一个带有命令式版本，另一个没有。这是因为命令式版本的结果很糟糕，混淆了其他库的比较。

递归算法(越低越好)

递归算法(没有命令版本，低更好)

命令式版本无法在递归迭代之间卸载内存。除去命令，我们看到@most/core 是更好的。

RxJS 和 XStream 稍微贵一点。XStream 显示出增长趋势，这表明需要更多的调查。

# 基于承诺的记忆测验

基于承诺的记忆测试应用

基于承诺(越低越好)

使用承诺，命令通过优化内存使用来报复。@most/core 仍然是三个流库中最好的，但偏差更多，XStream 更贵，但发热峰值更线性。

# 结论

在 Node.js 上测试了三个主要的功能反应式编程流 JavaScript 库的速度和内存使用情况。

这三个库在速度和内存使用效率方面都非常出色。

@most/core 已被确定为速度最好且内存使用效率最高的。此外，显式时间表示和始终异步启动有助于防止故障，并具有易于理解和可预测的数据流。

# **引文**

[1]维基百科—【https://en.wikipedia.org/wiki/Reactive_programming 

[2]https://github.com/funkia/hareactive 的行为—

[3] XStream 信号—[https://github.com/cyclejs/cyclejs/issues/581](https://github.com/cyclejs/cyclejs/issues/581)

[4]冷热可观测量—[http://davesexton . com/blog/post/Hot-and-Cold-observables . aspx](http://davesexton.com/blog/post/Hot-and-Cold-Observables.aspx)

# 资源链接

*   [RxJS](https://rxjs-dev.firebaseapp.com/)
*   [XStream](https://github.com/staltz/xstream)
*   [最核心](https://mostcore.readthedocs.io/en/latest/)
*   [基准的源代码](https://github.com/FbN/reactive-benchmark)
*   [基准测试的结果数据](https://drive.google.com/drive/folders/12aDWclrQXGn2F3XlVFGqOemxqzHEJ4pe?usp=sharing)