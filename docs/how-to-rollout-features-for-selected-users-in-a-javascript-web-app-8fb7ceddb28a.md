# 如何在 Web 应用程序中为选定的用户展示功能

> 原文：<https://betterprogramming.pub/how-to-rollout-features-for-selected-users-in-a-javascript-web-app-8fb7ceddb28a>

## 只需要一个 if 语句和一些计划

![](img/c84b010b7b3f2eaf5d4f50e56f1d3a7e.png)

图片由 [@derstudi](https://unsplash.com/@derstudi) 在 [Unsplash](https://unsplash.com/photos/ABGaVhJxwDQ) 上

我们大多数人都见过这样的应用程序，其中一些新功能最初只提供给一部分用户，然后逐渐推广到其他用户。

有些应用程序向给定位置的所有用户发布新功能，有些可能向最活跃的用户发布，有些可能向随机选择的用户发布。一些应用程序甚至会询问用户是否愿意尝试新功能。然而，实际上可能只有一些用户有机会选择加入。

以这种方式逐渐发布功能的过程称为功能展示。

# 为什么

能够仅向用户群的子集发布某些功能是一种非常强大的能力，而不仅仅局限于能够逐步推出功能。其他一些优势包括:

*   这使得我们可以通过针对特定用户来试验或测试新功能，而不会影响其他用户群。例如，我们可能希望将一些功能定位于那些更有可能给出反馈和报告错误的用户。
*   我们也可能向一些用户发布一个特性或设计的不同变体，并执行 A/B 测试。

以这种方式发布特性在以下方面对开发人员也是有利的:

*   开发人员可以将新代码部署到生产环境中，但不能提供给用户。这允许开发人员快速合并他们的代码，避免讨厌的合并冲突。
*   如果某个新特性出现问题，可以完全禁用该特性，而无需推送任何代码。这避免了必须紧急回滚部署。

# 怎么做

那么，这怎么可能呢？最简单且广为人知的方法之一是使用一种叫做特性切换的东西。顾名思义，这只是一个可以打开和关闭的开关。简单来说，它只是你代码中的一个简单的`if`语句。然而，事情可能会比这复杂一点。让我们看一个例子来更清楚地说明这一点。

如果您正在为应用程序中的用户资料页面进行新的设计，您将创建一个名为`newUserProfile`的特性切换。这个开关可以被认为是一个可以从你的应用程序中访问的`boolean`变量。

现在，假设您想向加利福尼亚的所有用户发布新的设计。要做到这一点，您需要有某种方法来控制当您的应用程序查询时，toggle `newUserProfile`的值应该是`true`给哪些用户。这是因为如果加州的用户正在访问您的应用程序，您只希望`newUserProfile`具有值`true`。对于所有其他用户，您可能希望它的值是`false`。您可以通过如下方式在后端轻松做到这一点:

这里，我们实现了一个函数`isFeatureEnabled`，在这里我们传递请求的上下文和我们想要查询其值的特性 toggle 的名称。该功能也可以转移到单独的服务中。为了简单起见，我们将假设这个检查是在同一个服务中完成的，并把整个`request`作为确定切换值的上下文。上下文可以从`request`更改为任何需要的内容，例如用户的 id。

然后，您需要以某种方式将`isFeatureEnabled(request, 'newUserProfile')`的值发送到前端。你可以用多种方式做到这一点。做到这一点的一种方法是在登录后或在初始请求时评估并向前端发送所有相关特征的值。另一种方法是，当需要加载用户配置文件页面时，向后端发送一个请求来查询这个特定开关的值。

无论您使用什么方式将特性切换的值发送到前端，您都需要在前端代码中执行类似的操作:

如果这纯粹是一个设计上的改变，那么在前端检查触发器的值就足够了。然而，让我们假设我们的后端为新的用户配置文件发送不同格式的数据。在这种情况下，您可以在后端执行如下操作:

如果触发器背后的特性很关键，那么在后端请求期间检查触发器的值也很重要，而不仅仅是前端。这是因为精通技术的用户可能能够绕过前端的检查。

## 战略

在上面的例子中，我们定义了一个特性切换和一个“策略”来推出这个特性。这里使用的策略是检查请求者的 IP 地址。你也许可以想出多种策略来推出一个特性。一些常见的有:

1.  向一定比例的用户随机推出该功能
2.  向精心挑选的用户(例如，测试帐户)推出该功能
3.  仅向“订户”或付费用户推出一项功能
4.  仅向“选择加入”的用户推出一项功能

在上面的示例中，替代策略是使用用户配置文件中设置的用户位置，或者查询登录日志来确定用户的位置。

## 动态分配策略

假设您已经对加利福尼亚的所有用户测试了该特性。现在，您希望向 50%的用户推广该功能，而不管他们在哪里。上面的实现需要修改`isFeatureEnabled`函数的代码并部署它。

一个显而易见的替代方案是拥有一个系统，允许您动态地影响`isFeatureEnabled`的结果，而无需部署任何代码。这归结为拥有将一个或多个策略动态关联到每个特性切换的能力。您可以很容易地将这种关联存储在数据库中，并创建某种“管理面板”来控制这些关联。在这样的情况下，`isFeatureEnabled`会是这样的:

这里，我们切换到用代码实现策略，并使用配置而不是代码来实现实际的功能切换。这是因为在大多数情况下，您将创建更多的功能切换，而不是创建新的策略。

将特性切换作为配置存储在数据库中，而不是在代码中存储，这有优点也有缺点。使用外部存储的配置最明显的缺点是丢失了切换策略的变化历史。拥有历史记录将允许在出现问题时轻松回滚到以前的应用程序状态。

然而，使用配置的好处是，万一有什么事情不顺利，只需通过更改配置来关闭该特性就可以了。不需要重新部署。如果配置可以通过管理面板更改，那么非技术人员也可以轻松完成。

很明显，您可以自己实现一个基于配置的特性切换系统，并满足您的应用程序的需求。然而，考虑一些已经可用的久经考验的解决方案可能是值得的。这些可以很容易地集成到任何应用程序中。[释放](https://docs.getunleash.io/)、 [flipt](https://flipt.io/) 和 [ffflip](https://github.com/FredKSchott/fflip) 是一些流行的免费和开源解决方案。 [Firebase 远程配置](https://firebase.google.com/docs/remote-config)、[优化](https://www.optimizely.com/rollouts/?ref=nav)、[启动黑暗](https://launchdarkly.com/)、 [CloudBees](https://www.cloudbees.com/products/feature-management) 都是一些付费的解决方案。

在决定使用哪种实现之前，思考并找出最适合您的团队是非常重要的。

# 需要考虑的事项

在加入潮流并将特性切换集成到您的应用程序之前，有一些事情需要考虑。

## 你的应用足够成熟吗？

如果您的应用程序处于开发的开始阶段，并且您很少更新现有的特性或发布新的特性，那么使用特性切换可能没有太多的优势。

## 你愿意做这个计划吗？

特性切换需要团队投入一些时间和精力来正确规划特性。这包括但不限于识别和记录肘节的目的，估计肘节的寿命，以及计划何时以及如何清理肘节。如果做不到这一点，可能会导致功能切换堆积起来，使跟踪和管理这些切换变得非常困难。

## 你能处理债务吗？

在你的系统中引入特性切换是引入技术债务。在您决定将特性发布给每个人之后，您需要清理为促进特性切换而创建的条件分支。您必须愿意并准备好在您的功能切换后进行清理。

## 你能应付测试吗？

如果您喜欢由测试来确保，那么您将需要为这两种情况编写测试；标志启用和禁用的时间。功能切换有时也可能最终相互影响。这可能导致一些测试比你希望的要复杂一些。

## 移动应用呢？

如果你的产品是一个移动应用程序，那么大多数应用程序商店都有这样的功能，你可以只向特定位置的用户或随机的目标用户推出新版本的应用程序。这对您的产品来说可能就足够了，您可能根本不需要特性切换。然而，如果需要的话，没有什么可以阻止你使用它们。

# 进一步阅读

*   [Martin Fowler on Feature Toggles](https://martinfowler.com/articles/feature-toggles.html)(也用作本文的参考)

[](https://martinfowler.com/articles/feature-toggles.html) [## 特征切换(又名特征标志)

### Pete Hodgson Pete Hodgson 是位于旧金山湾区的独立软件交付顾问。他…

martinfowler.com](https://martinfowler.com/articles/feature-toggles.html) 

*   [Invoice2go](https://medium.com/u/b06e29086c98?source=post_page-----8fb7ceddb28a--------------------------------) [在 Android 上实现他们的功能标志](https://medium.com/@Invoice2go/how-we-implement-feature-toggle-on-android-86bb1505ec6c)

[](https://medium.com/@Invoice2go/how-we-implement-feature-toggle-on-android-86bb1505ec6c) [## 我们如何在 Android 上实现功能切换

### 据 Martin Flower 所说,“功能切换是一组模式，可以帮助团队将新功能交付给…

medium.com](https://medium.com/@Invoice2go/how-we-implement-feature-toggle-on-android-86bb1505ec6c) 

*   [视觉](https://medium.com/u/206c13695d27?source=post_page-----8fb7ceddb28a--------------------------------) [关于 A/B 测试](https://medium.com/@InVisionApp/a-b-and-see-a-beginner-s-guide-to-a-b-testing-a16406f1a239) (The What)

[](https://medium.com/@InVisionApp/a-b-and-see-a-beginner-s-guide-to-a-b-testing-a16406f1a239) [## A/B 和参见:A/B 测试初学者指南

### 设计中的决策过程一直是讨论的热点。为什么有些设计师会做出…

medium.com](https://medium.com/@InVisionApp/a-b-and-see-a-beginner-s-guide-to-a-b-testing-a16406f1a239) 

*   [Rameez Kakodker](https://medium.com/u/b6decaad7496?source=post_page-----8fb7ceddb28a--------------------------------) 上的[A/B 测试](https://uxdesign.cc/a-b-testing-the-myth-the-legend-the-truth-fb1819cbe781)(什么和分析)

[](https://uxdesign.cc/a-b-testing-the-myth-the-legend-the-truth-fb1819cbe781) [## A/B 测试:神话、传说、真相

### A/B 测试是产品经理的武器库中的一个关键武器。每当有关于用户体验的争论时…

uxdesign.cc](https://uxdesign.cc/a-b-testing-the-myth-the-legend-the-truth-fb1819cbe781)