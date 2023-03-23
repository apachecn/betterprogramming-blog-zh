# Golang:使用 Go-Feature-Flag 实现产品发布周期的自动化

> 原文：<https://betterprogramming.pub/automate-your-product-release-cycles-using-go-feature-flag-6ab73f869f>

## 仅使用基本配置进行渐进式推广、安排工作流程和实验

![](img/c3ad97e5bcc6756ea4358dacac354c4f.png)

由[迪安·布瑞尔利](https://unsplash.com/@milkandbourbons?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当您构建一个新功能时，在产品、工程和营销团队之间协调实际的发布时间表可能会很有挑战性。

虽然推出新的东西似乎很容易，但执行不好的首次展示可能会成为你最糟糕的噩梦。

在本文中，我将向您展示如何使用 Go 模块`[go-feature-flag](https://github.com/thomaspoignant/go-feature-flag)`来顺利推出您的新功能，并帮助您在推出阶段充满信心。如果你不熟悉特性标志或特性切换的概念，我鼓励你阅读 Martin Fowler 的这篇文章。

`go-feature-flag` 是一个 Go 模块，轻松管理您的旗帜。可以参考[我几个月前写的这篇文章](/golang-feature-flags-a-simple-go-implementation-without-a-back-end-7c5c00078a58)来了解它的工作原理。

# 如何使用 go-feature-flag

该库使用起来非常简单:

1.  安装模块:

```
go get github.com/thomaspoignant/go-feature-flag
```

2.用您的标志配置文件的位置初始化客户端:

3.根据标志值有条件地放置新要素:

你可以有更多的配置，但是我会让你检查文档[来得到它。](https://thomaspoignant.github.io/go-feature-flag/)

# 渐进式推广

当你发布一个对你的系统有很大影响的新特性时，你可能不想让所有用户都使用这个新特性。

例如，您正在部署可能会消耗 CPU 的东西，并且您想要有时间来检查您的服务器是否正确地处理它。这通常是渐进式首次展示的一个很好的用例。

它会逐渐增加受您的功能标志影响的用户数量，以避免一次对所有用户进行大规模推广。在此期间，它允许您监控您的系统，并确信您的基础架构能够处理这种新的负载。

```
**progressive-flag**:
  true: true
  false: false
  default: false
  **rollout**:
    **progressive**:
      **percentage:**
        initial: **0**
        end: **100**
      **releaseRamp:**
        start: 2021-03-20T00:00:00.10-05:00
        end: 2021-03-21T00:00:00.10-05:00
```

为了在`[go-feature-flag](https://github.com/thomaspoignant/go-feature-flag)`中做到这一点，您将像上面的例子那样配置您的标志。使用初始百分比值和释放渐变设置渐进式卷展栏。随着时间的推移，越来越多的用户会受到该标志的影响，并将拥有新的功能。

根据这个特性的关键程度，您可以有一个长的或短的发布斜坡。如果出现问题，您可以随时编辑您的标志来停止卷展栏。

# 计划工作流

调度为用户引入了为未来时间点改变其标志的能力。虽然这听起来似乎很简单，但是它释放了用户通过预先安排增量步骤来创建复杂发布策略的潜力。

例如，您可能希望明天打开一个特性进行内部测试，然后在四天后为您的“beta”用户群启用它。

```
**scheduled-flag**:
  true: true
  false: false
  default: false
  percentage: 0
  **rollout**:
    **scheduled**:
      steps:
        - **date: 2020-04-10T00:00:00.00+02:00**
          rule: internal eq true
          percentage: 100 - **date: 2020-04-14T00:00:00.00+02:00**
          rule: internal eq true and beta eq true - **date: 2020-04-18T00:00:00.00+02:00**
          rule: ""
```

在本例中，您可以看到我们正在多次更新该标志，以便在将来执行操作。让我们详细描述一下在这种配置下会发生什么:

1.  在`2020–04–10`之前，不提供标志。
2.  在第一次更新标志(`2020–04–10`)，*，*之后，我们有 100%的内部用户收到了标志`true`。
3.  四天后，我们将带有`beta` 标志的用户添加为`true`。
4.  最后，四天后，我们向所有用户开放该功能。

正如您所看到的，这真的很强大，因为您的发布管理现在已经准备好了，不需要做任何手动部署/操作，并且这个调度可以由非技术用户(也就是您的产品经理)来完成。

# 实验展示

有时，您还希望进行试验，收集数据，然后决定是否要向所有用户推广该功能。

要正确地做到这一点，您可以为您的用户子集配置带有开始日期和结束日期的标志。

```
**experimentation-flag**:
  **percentage**: 50
  true: true
  false: false
  default: false
  rule: userId sw "9"
  **rollout**:
    **experimentation**:
      **start**: 2021-03-20T00:00:00.10-05:00
      **end**: 2021-03-21T00:00:00.10-05:00
```

在这个例子中，50%拥有以`9`开始的`userId`的用户会受到实验的`start`和`end`日期之间的标记的影响。

使用该模块，您还可以收集您的变化的数据(更多信息参见[文档](https://thomaspoignant.github.io/go-feature-flag/data_collection/))*将它们与您正在测试的数据结合起来。*

*所以你可以看到你的实验结果，并决定你是否真的想要推出这面旗帜。*

# *结论*

*使用特性标志确实是一件很棒的事情，但是如果你使用一些高级的展示策略，它会变得更好。*

*如果您开始使用它们，您会喜欢它，因为您将部署和发布解耦，并且您可以对您的用户可以做什么进行细粒度的控制。*

*[](https://github.com/thomaspoignant/go-feature-flag) [## 托马斯辛酸/去功能标志

### 无需维护复杂系统的功能标志！去给 github.com/thomaspoignant/go-feature-flag 拿一个简单的…

github.com](https://github.com/thomaspoignant/go-feature-flag)*