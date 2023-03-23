# 宣布 Adoptoposs.org

> 原文：<https://betterprogramming.pub/how-to-keep-open-source-software-maintained-74c1cd5df5d5>

## 将开源维护者与愿意提供帮助的人联系起来

![](img/bf1be7e76f8835e1adeda5052427a2bc.png)

我很高兴地宣布 [Adoptoposs](https://adoptoposs.org) ，这是一个开源应用程序，它将开源软件维护者与希望帮助项目和维护者长期保持健康的人联系在一起。

Adoptoposs 提供了一个平台，让你的开源项目被采用，并与潜在的合作维护者进行初步接触。

GitHub 上提供了源代码:

[](https://github.com/adoptoposs/adoptoposs) [## 收养通道/收养通道

### 为你的开源软件项目寻找合作维护者。Adoptoposs 提供了一个平台来放置您的开源…

github.com](https://github.com/adoptoposs/adoptoposs) 

如果你想了解更多关于我创建 Adoptoposs 的原因，无人维护的开源软件的问题，以及解决方案的一部分，那么请继续阅读。

开源软件(OSS)无可争议地主宰着我们的世界。企业开源被认为将在 2020 年继续增长。

互联网的大部分运行在开源软件上，如今数百万开发者用 250 多种编程语言构建和维护成千上万的开源软件包。

# 无人维护的开放源码软件项目的问题

尽管大多数开发人员在工作中使用开源软件并为之做出贡献，但大量 OSS 项目的维护是由志愿者在业余时间完成的。

许多项目都是从个人的副业发展成广受欢迎和广泛使用的库。有了大量的吸引，更多的问题和拉动请求由社区打开。维护人员越来越多的责任是协调到来的请求和变更，并指导整个项目。

有很多关于 OSS 维护者的挑战和斗争的故事。它们包括令人精疲力尽的社区管理，以及必须投入大量精力对问题和拉式请求进行分类。

当听到这些大部分是自愿努力的故事时，维护人员感到工作量过大而放弃他们的项目就不足为奇了。

在开源项目上投入较少时间甚至放弃的原因有很多。维护人员离开他们的公司或失去兴趣。

他们个人生活的变化使他们没有时间去关心项目，或者他们完全因为精疲力尽或生病而停止了他们在开源领域的活动。最坏的情况，他们已经去世了。

所有这些情况都留下了一些项目，这些项目通常只有原作者拥有管理权限或访问发布帐户的权限。

错误报告越积越多，拉取请求无法合并，与周围生态系统的兼容性问题出现了。然后，在某个时候，社区中的某个人站出来打开了臭名昭著的“[这个项目被放弃了吗？](https://github.com/search?q=Is+this+project+abandoned%3F&type=Issues)“问题。

最终，项目可能会分叉，由来自社区的新志愿者进行修复。由于缺乏发布权限，项目的新版本以新名称发布。

所有这些导致了对项目状态的混淆，以及它是否能够并且应该仍然被使用并且被认为是一个稳定的软件。

# 那么，有什么解决办法呢？

简单来说，每一个相当受欢迎的开源项目都应该有一个维护人员团队。

「相当受欢迎」是什么意思？你什么时候知道你的项目是其他人的重要依赖？你可能很快就会知道它的下载率和它的直接依赖者的受欢迎程度。

然而，如果它已经流行起来，并且您仍然在单一维护者的策略上操作项目，那么维护问题很可能迟早会出现。

然而，拥有一个由多个维护人员组成的团队很可能会产生一个维护得更好的项目，原因有几个。

## **1。工作量可以在所有维护人员之间分配**

我们每个人都有不同的偏好，不同的兴趣和技能。可能我们中没有一个人有很高的技能，也没有兴趣去做*所有*开源维护要求的任务。

因此，建立一个团队并拆分整个工作量，让每个维护人员从事他们喜欢并能做得最好的部分是有意义的。

更少的工作量也将意味着更少的风险，并允许维护人员更好地照顾自己。就像一个公司的团队是为了什么而建立的一样。

## **2。团队合作更容易做出决定**

它不仅减轻了每个维护者在决策时的压力。它还可能带来更大的稳定性和更明智的决策。

默认情况下会考虑不同的观点。古语有云:“一双新鲜的眼睛阻止了许多虫子的滋生。"

## **3。不会出现单点故障**

在我们软件开发的日常生活中，我们的目标是通过引入冗余来达到可用性和容错。我们的集群由冗余服务器组成，我们使用 Git 来分散存储库，并且我们(希望)创建个人文档的备份。

所有这些措施都试图防止单点故障。通过在共同维护者的团队中工作，其中多人可以完全访问项目，这个单点故障可以被消除。

可以与社区进行交流，可以合并拉取请求，可以发布版本，即使有时一个或多个共同维护者不在，也可以授权访问。

## **4。有可能卸任维修工**

当维护人员决定离开项目时，允许他们离开是很重要的。如果你离开你的项目作为一个单独的维护者，那么它就被抛弃了。一接到通知就没人维护。

社区必须付出额外的努力来保持它的活力或让它重新焕发生机。如果没有人拥有管理和发布权，这个过程会变得更加困难。

拥有一个共同维护者团队可以让你考虑如何处理离开项目的人，以及如何接纳新的共同维护者。

当有多个维护者就位时，如果有人想离开项目，他们可以这样做——而不会不可避免地将项目置于无人维护的状态。

# 我能怎么做呢？

作为开源软件的维护者，你应该通过建立一个共同维护者的团队来消除项目的单点故障。授予多人对项目的完全访问权限将确保总有不止一个人可以管理项目。

但是，它并不仅限于提供对知识库或 GitHub 组织的访问。在项目的发布过程中，你也应该给你的合作维护者推送访问包注册和所有其他相关账户的权限。

共同维护者不一定需要在项目中做很多工作。他们可能只是在紧急情况下的负责人。这也包括当以前的维护者想退出时，给新的维护者访问权。类似于托管人。

如果你想离开一个项目，你应该清楚地表达出来，并通知相关人员和社区。试着让人们了解你的理由，以及他们如何帮助维持这个项目。

在进行项目时，不要忘记分享和记录知识，这样如果有人离开，团队中的其他人就没有什么不知道的了。

你可以使用社区驱动的项目来帮助维护开源软件，比如精彩的 [CodeTriage](https://codetriage.com) 项目，像 [Jazzband](https://jazzband.co) 这样的协作社区(阅读[对 opensource.com](https://opensource.com/article/20/2/python-maintained)的采访)，以及像[采用 Ember 插件](https://github.com/adopted-ember-addons/program-guidelines)这样的程序。

现在还有 [Adoptoposs，](https://adoptoposs.org)也有，希望有助于保持你的项目和你自己的健康，同时维护开源软件。
检查一下，保持快乐。

顺便说一下——adopt oposs 正在寻找合作维护者。它由[仙丹](https://elixir-lang.org/)、[凤凰 LiveView](https://github.com/phoenixframework/phoenix_live_view) 和[顺风 CSS](https://tailwindcss.com/) 打造，目前托管在 [Fly.io](https://fly.io) 上。如果你想帮忙，请告诉我。问题可以在 [GitHub 资源库](https://github.com/adoptoposs/adoptoposs/issues)中报告。

![](img/3ca4583b4b6a9213d50232a12142fb13.png)

# 参考

这里是我在为构建 Adoptoposs 做研究和写这篇文章时遇到的一些有见地的链接和参考资料。

[](https://github.com/nayafia/awesome-maintainers) [## naya FIA/awesome-维护者

### 关于作为开源维护者的经历的演讲、博客帖子和采访@brettcannon，Python“The…

github.com](https://github.com/nayafia/awesome-maintainers) [](https://www.wired.com/story/giving-open-source-projects-life-after-a-developers-death/) [## 在开发者死后赋予开源项目生命

### 你可能从未听说过已故的吉姆·威里奇或他的软件。但你几乎肯定用过基于他的应用程序…

www.wired.com](https://www.wired.com/story/giving-open-source-projects-life-after-a-developers-death/) [](https://opensource.com/article/17/4/how-to-leave-open-source-project) [## 如何应对离开开源项目

### 2015 年初，我决定离开我工作了两年多的工作。我的家人和……

opensource.com](https://opensource.com/article/17/4/how-to-leave-open-source-project) [](https://changelog.com/rfc) [## Nadia Eghbal 和 Mikeal Rogers 的请求提交播客

### 《提交请求》探讨了开源可持续发展的不同视角(已退休)。

changelog.com](https://changelog.com/rfc) [](https://www.redhat.com/en/enterprise-open-source-report/2020) [## 企业开源现状:红帽报告

### 混合云体系结构+企业开源实现数字化转型 63%的 IT 领导者拥有混合云…

www.redhat.com](https://www.redhat.com/en/enterprise-open-source-report/2020) 

*   [https://opensourcesurvey.org/2017](https://opensourcesurvey.org/2017/)
*   [https://opensource.guide/best-practices](https://opensource.guide/best-practices/)

## 项目无法继续维持的情况

一个广泛使用的 Python 包，其中一名前维护人员离开了公司，并脱离了团队，不再具有访问权限，并且不能将现有的 PRs 与错误修复合并:

[](https://github.com/seatgeek/fuzzywuzzy/pull/243) [## h b-alexbotello Pull Request # 243 seatgeek/fuzzywuzzy 对 Python 3.7 的修复

### 根据 PEP 479 修复#233，如果 raise StopIteration 直接发生在生成器中，只需用 return…替换它

github.com](https://github.com/seatgeek/fuzzywuzzy/pull/243) 

另一个 Python 包 flask-security 被多次分支，现在由 Chris Wagner 维护，也以 Flask-Security 的名字存在:

[](https://github.com/jwag956/flask-security) [## jwag 956/flask-安全

### 快速为您的 Flask 应用程序添加安全功能。这是 Flask-Security 的独立维护版本…

github.com](https://github.com/jwag956/flask-security) 

*   [https://github.com/mattupstate/flask-security/issues/854](https://github.com/mattupstate/flask-security/issues/854)
*   [https://github.com/italomaia/flask-empty/issues/35](https://github.com/italomaia/flask-empty/issues/35)

railscasts.com[的创作者瑞安·贝茨因为精疲力竭而暂停了软件开发。他的康康红宝石作为](http://railscasts.com/)[康康康](https://github.com/CanCanCommunity/cancancan)继续存在:

[](https://github.com/ryanb/cancan/issues/994) [## 注意:康康要动了！问题#994 ryanb/cancan

### “离别是如此甜蜜的悲伤”正如上个月在 pull #989 的评论帖子中所讨论的,“是时候离开了……

github.com](https://github.com/ryanb/cancan/issues/994) 

迈克尔·克斯勒(netzpirat)，haml-coffee 红宝石的维护者，去世了:

[](https://github.com/netzpirat/haml-coffee/issues/97) [## 注意:@netzpirat 于 2014 年 4 月去世#97 netzpirat/haml-coffee

### 在移交给 Michael 之前，我和@sebastiandeutsch 是这个项目的第一批提交者…

github.com](https://github.com/netzpirat/haml-coffee/issues/97)