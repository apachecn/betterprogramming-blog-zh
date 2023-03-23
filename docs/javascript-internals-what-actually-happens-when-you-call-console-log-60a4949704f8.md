# JavaScript 内部:调用 Console.log 时实际会发生什么？

> 原文：<https://betterprogramming.pub/javascript-internals-what-actually-happens-when-you-call-console-log-60a4949704f8>

## 深入了解我们每天习以为常的功能

![](img/abd48e32dec680667415abc943c94b7c.png)

Irvan Smith 在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

我们都知道 JavaScript 只是基于 ECMAScript 的一种方言。ECMAScript 是关于语法和代码应该如何运行的规范。它可以用任何语言实现，就像任何其他标准一样。
V8 是用谷歌 Chrome 使用的 C++写的，火狐的 JavaScript 引擎(SpiderMonkey 和现在的 TraceMonkey)也是用 C++写的，Rhino 是用 Java 写的。你会惊讶地发现，JavaScript 也可以完全用 JavaScript 编写，就像 Project 水仙所做的那样。这将让您明白 JavaScript 可以用任何语言编写。它只是一个标准。

JavaScript 的大多数实现显示的行为显然是由使用指针和 ByRef 参数传递引起的，这通常指向 C 或 C++的使用。

# 调用 console.log 时会发生什么？

记住我们站在巨人的肩膀上工作总是好的，即使在调用最基本的代码行时也是如此。因此，让我们以 V8——用 C++编写的支持 Chrome 和 Node.js 的 JavaScript 运行时和 WebAssembly 引擎——为例进行深入研究。

## 它将首先调用一个实用函数 WriteToFile

```
**void** D8Console::Log(**const** debug::ConsoleCallArguments& args,
                   **const** v8::debug::ConsoleContext&) {
    *WriteToFile*(nullptr, stdout, isolate_, args);
}[*https://github.com/v8/v8/blob/4b9b23521e/src/d8-console.cc#L52-L55*](https://github.com/v8/v8/blob/4b9b23521e/src/d8-console.cc#L52-L55)
```

## 经过内部预处理后，JavaScript 值将调用 fwrite

函数`fwrite`是`libc`的一部分，按照 ANSI C 标准的规定，T1 是 C 编程语言的标准库。众所周知，C 标准库在不同平台上有几种实现。让我们以`musl`为例，这是一个在 Linux 平台上用 C 语言编写的著名实现:

它将在内部调用一个实用函数(`__stdio_write` ) )经过一点间接调用，然后调用操作系统调用(`writev`):

`syscall`符号是一个宏，经过大量预处理后会扩展到`__syscall3`。操作系统之间的系统调用不同，处理器架构之间执行系统调用的方式也不同。它通常需要您编写(或生成)一点汇编。在 x86–64 上，`musl`对`__syscall3`的定义如下:

这将设置系统调用号和参数。在 x86–64 上，进行系统调用的指令称为`syscall`。

在进行 syscall 之后，控制权转移到内核(在本例中是 Linux)。但那是另外一个故事了…

系统调用将陷入内核。内核将保存大量关于进程和系统调用上下文的信息。在最简单的情况下，内核会将该进程放在一个等待处理的进程队列中，然后继续处理另一个等待处理的进程(例如，其他发出系统调用或硬件中断的进程)。然后，内核将继续服务最高优先级的请求/进程，最终返回到我们调用`console.log`的进程。内核将会看到这个进程被阻塞并等待 syswrite。它将获取之前存储的参数和其他数据，并通过特定于内核的实现写入文件。然后，它会将进程标记为“就绪”，并将其返回到就绪队列，以便在轮到运行时将控制权返回到调用进程。

他们在系统调用之后停止了，因为很难说之后会发生什么。它完全依赖于操作系统内核及其实现细节。

## 别忘了给我们你的👏！

此外，关注与科技相关的出版物[更好的编程](https://medium.com/better-programming)。

![](img/2c9c73c410c025ffffffa8dfee10dbce.png)

拍手！拍手！拍手！

请关注作者，定期获取未来文章的更新。

[](https://medium.com/@singhjp) [## 贾尚·普里特·辛格——中等

### 阅读 Jashan Preet Singh 在媒体上的文章。全栈 Web 开发人员。每天，贾尚·普里特·辛格和成千上万的…

medium.com](https://medium.com/@singhjp) 

随时通过 Linkedin 联系:

[](https://www.linkedin.com/in/singhjp006/) [## jashan Preet Singh——软件工程师——Chai Point | LinkedIn

SDE | |热情 _ 火山| |科技 _ 漫游癖| |新 _ 风险投资 _ 领导力@麻省理工学院](https://www.linkedin.com/in/singhjp006/)