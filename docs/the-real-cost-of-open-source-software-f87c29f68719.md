# 开源软件的真正成本

> 原文：<https://betterprogramming.pub/the-real-cost-of-open-source-software-f87c29f68719>

## 它是免费的还是看起来是免费的？不管怎样，它有自己的一系列挑战

![](img/2a9bc15cd0cf480580588f52eb10fa4d.png)

斯科特·罗杰森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

根据维基百科，“**开源软件是根据许可证发布的计算机软件，其中版权所有者授予用户使用、研究、更改和向任何人出于任何目的分发软件及其源代码的权利”。简单地说，您有权访问代码，并有权在您认为合适的时候修改、共享和使用代码。**

*传统上，软件市场是封闭源代码的(专有的),主要由大型组织控制，这些组织发布其产品的新版本而不共享底层代码。组织出售带有许可权和其他有限权利的专有软件，因为它包含他们拥有的专利。*

*开放源码倡议成立于 1998 年 2 月，旨在宣传开放源码原则。作为开源定义的管理者，它为开源生态系统奠定了基础。开放源码软件在 21 世纪初开始获得牵引力，计算机被广泛用于解决商业问题，互联网引起轰动。*

*GitHub 在托管开源项目的事实上扮演了一个重要的角色。截至 2022 年 6 月，它拥有超过 2800 万个公共存储库，显示了开放源码软件的范围及其被开发者社区接受的程度。大型组织，如微软、Meta、谷歌、网飞、优步等。，发布了许多开源项目以造福整个社区，显示了对 OSS 的支持和热爱。*

# *0.00 美元的价格标签*

*谁不喜欢免费的午餐呢？一个每年花费数千美元购买商业许可证的组织发现了一个等价的 OSS 产品，并禁不住要问，既然它是免费的，为什么还要付费？在做出明智的决定之前评估所有的选择没有错。作为一个软件用户，你应该在承诺一个产品之前做一个深入的分析。然而，仅仅因为它可以免费使用，并不能使它成为首选。如果您在这里计算总拥有成本(TCO)是最好的。*

*OSS 最大的风险是每个人都把它和免费联系在一起。我不反对使用 OSS 相反，我是它的超级支持者。在我的职业生涯中，我已经使用了许多 OSS 项目，这些项目使生活变得更容易，并有助于更快地交付价值。迄今为止，我在 GitHub 上研究趋势项目，了解软件行业的发展方向。这篇文章旨在帮助改变软件专业人员的思维模式，以充分理解使用 OSS 的含义，以及他们的组织是否对它感兴趣。*

# *开源的挑战*

*软件开发和部署伴随着风险和假设。组织在尝试采用 OSS 时面临多重挑战。下面重点介绍一些关键挑战。*

## *安全性*

*由于代码的开放性，它更容易受到安全漏洞的影响。是的，许可软件同样容易出现安全问题；然而，代码的开放性只会帮助黑客。最近的 log4j 软件漏洞证明了开源软件包在开源和闭源软件中的渗透。软件社区感到欣慰的是，OSS 由于其透明性而更加安全，许多眼睛都在注视着检测和解决问题。在现实中，有些项目有太多的眼睛，有些太少的眼睛或根本没有。*

*我最近听到的一个令人兴奋的发现是关于一个组织使用 OSS 包来协调工作负载。该软件包非常受欢迎，在大多数情况下可能是事实上的选择。但是，该组织在将该包投入生产之前对其进行了漏洞扫描。令人惊讶的是，许多漏洞被发现，仍然没有被包的维护者关闭。最棒的是，该组织现在正在寻找同一软件的托管服务。归结起来就是你的风险偏好:你准备好发布一个产品中有漏洞的包了吗，或者你想花费精力去修复它们吗？*

## *弃用和更新*

*软件开发是迭代的，所以团队定期发布新版本。对于 OSS 来说也是如此，维护者会发布他们的包/框架/软件的新版本。对于软件包的用户来说，这意味着升级到最新的支持版本，这可能需要更新您的代码、迁移或任何需要软件专业人员干预的事情。*

*即使旧版本能像预期的那样工作，从维护者那里获得任何支持或安全补丁也是不可能的。除非您准备好冒这个风险，否则一年几次将您的安装更新到最新支持的版本需要相当大的努力。在 SaaS 产品中，供应商更新软件的过程对用户是隐藏的。*

## *缺少供应商支持*

*放置一个新的软件不仅仅是将其部署到生产环境中。需要一些操作和支持来确保其顺利运行。当你不考虑支持或运营就将开源软件提升并转移到生产中时，你无疑会遇到障碍。如果供应商支持是为了解决严重程度为 1 的安全问题、解决性能问题，或者只是确保您正确使用该功能，那么它可能是有益的。*

*组织每年支付数千美元用于支持，确保他们可以依靠某人回答问题并提供最佳使用实践。当您在生产中遇到问题时，再多的文档也没有用。社区对许多项目的支持非常好，但是没有附加 SLA。*

*在没有支持的情况下运行生产中的关键应用程序无疑不是一个好主意，并且会在灾难场景中让您陷入困境。因此，许多操作系统现在已经开始提供托管服务，以提供大型组织有效运营其业务所需的支持。*

## *学习曲线和培训*

*大多数操作系统都有优秀和丰富的文档。然而，如果有人花时间阅读并找到使用它的最佳实践，那将是最好的。你可能会很幸运，找到一个对这个包有经验的人，他可以分享他们的经验。*

*相比之下，软件供应商提供免费培训、开发者支持和技术宣传来更多地使用他们的软件。此培训帮助您的员工获得认证，并帮助他们在职业生涯中成长。此外，他们会让你了解即将到来的新特性，如果你是他们的大客户之一，你对软件的发展方向也有发言权。*

# *结论*

*选择 OSS 还是供应商取决于你的目标、团队技能、组织结构、安全性和架构。再次，OSS 不是免费的；总拥有成本可能高于从供应商处购买相同服务的成本。在选择一个 OSS 的时候，我从项目的类型的镜头来看，是框架，工具，还是成熟的套件，然后进行相应的分析。*

*从我的经验来看，许多开源框架都可以用来构建你的产品——从看看它们在你当前生态系统(技术和技能)中的许可和适用性开始。这些往往更受欢迎，通常由科技公司支持来制造他们的产品。*

*全面的开源产品需要从特性和可支持性的角度进行更多的审查。该软件解决了一个业务或技术问题，您需要将其托管在您的基础架构中。许多这类软件的创造者已经开始将这些作为 SaaS 产品提供，以便快速入门，同时，将其货币化，以便他们可以继续构建更多的功能。这对开发者和用户来说都是双赢的。选择使用社区版还是企业版完全是基于您需求的个人选择。没有错误的选择，只有基于你的需求和目标的不同选择。*

> *“在真正的开源中，你有权利掌控自己的命运。”——莱纳斯·托瓦尔兹*