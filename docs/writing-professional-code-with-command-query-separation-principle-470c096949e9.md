# 如何利用命令查询分离原则编写专业代码

> 原文：<https://betterprogramming.pub/writing-professional-code-with-command-query-separation-principle-470c096949e9>

## 改进您的代码库

![](img/3227580cae910e0ba7b7a4394141397e.png)

由[文森特·博塔](https://unsplash.com/@0asa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/particles?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

量子力学的一个基本事实挑战了我们对世界的基本认知:观察某物会改变它的行为。

这太疯狂了。在现实生活中，你不会期望看电视的行为会改变演播室里录制的原始内容。

出于同样的原因，您不会期望文件浏览器应用程序在每次打开时对其显示的文件进行更改。这种行为是没有用的——如果不担心不必要的更改，你就无法打开文件浏览器。

在软件工程中，划分数据观察和修改的相同原则也适用。以数据库为例。查询、插入和删除方法之间有明显的区别。你不会在`query()`方法的实现中编写改变数据的代码。

这种分离使得使用数据库 API 变得容易，而不必考虑在使用这些功能时会发生什么。

如果这种分离对数据库有意义，为什么不在我们代码库中编写的每个功能中强制执行呢？毕竟，每段代码最终都归结为查询数据或执行影响数据的命令。

# 例子

将一些代码打包到这样的函数中可能很有诱惑力:

```
fun scheduleAlarmIfNeeded(alarmInfo: AlarmInfo)
```

你可以知道它是做什么的，但是让我们花一点时间想想如何用一句话来描述它的行为。

事实上，没有办法用一句话来描述这个行为，因为你必须把函数行为分成两个不同的场景:

1.  如果闹钟已经预定，则调用该函数。
2.  未安排闹铃时调用的函数。

这意味着使用这个函数的代码的读者必须投入额外的精力去理解它做什么。为了安全起见，他们可能还必须阅读函数实现。

当我们声明一个函数时，我们希望不止一次地重用它。除了在非常高级的业务逻辑过程中，这样的函数没有机会在任何地方使用。

# 应用命令查询分离

```
if (!isAlarmScheduled(alarmInfo.id)) {
    scheduleAlarm(alarmInfo)
}
```

现在有两个独立的功能。高级过程现在必须包含三行而不是一行，但是可读性会更好。

这种分离的另一个积极效果是，有很多很好的例子可以说明这两个函数(或不同的 API)是如何被重用的。例如:

*   其他业务逻辑可以利用警报是否预定的知识。
*   他们可以在 UI 中向用户显示当前的调度状态。

这种架构方法与函数式编程范式密切相关，函数式编程范式鼓励您隔离和避免依赖于状态的代码，并编写具有清晰输入和输出的描述性函数。将改变系统的“命令”功能与负责查询数据的功能分开，使得功能代码不可避免。

将软件工程视为一门手艺或艺术可能很容易。
然而，我认为我们的目标应该是制定规则和原则，以确保以确定的方式产生健壮的、可重用的和可伸缩的代码。

根据我的经验，遵循像 CQS(命令查询分离)这样的规则可以显著改善这些因素。

延伸阅读:

[](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) [## 命令-查询分离

### 命令-查询分离(CQS)是命令式计算机编程的一个原则。它是由伯特兰·迈耶设计的…

en.wikipedia.org](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation) [](https://martinfowler.com/bliki/CommandQuerySeparation.html) [## bliki: CommandQuerySeparation

### 术语“命令查询分离”是由 Bertrand Meyer 在他的书《面向对象软件构造》中提出的…

martinfowler.com](https://martinfowler.com/bliki/CommandQuerySeparation.html)