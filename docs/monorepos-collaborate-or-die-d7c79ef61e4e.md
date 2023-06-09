# Monorepos:合作或死亡

> 原文：<https://betterprogramming.pub/monorepos-collaborate-or-die-d7c79ef61e4e>

## 这就是我喜欢 monorepos 的原因——但它并不适合所有人

![](img/63cc7d35a716e18037ef720d7dc50bdf.png)

一个大的单一报告在起作用。丹尼斯·库默在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我已经照顾 monorepos 好几年了，最近搬到一个新的地方后，我有机会反思很多工作。

我的结论是:

你只需要一件事就能成功完成你的 monorepo。而且和工装无关。

假设你有团队在一起工作。您构建了一个单一的应用程序，无论是单个后端服务还是单个前端应用程序，虽然它在开始时工作得很好，但您现在意识到最好将它的一些部分模块化，以提高您的可维护性以及迭代发布的能力。

你也在想标准化你的一些编码实践会很好，也许一些模板代码或组件库会很好地适合潜在的 monorepo，如果你还没有的话。

# 改变游戏规则的反馈循环

Monorepos 允许您非常容易地确保团队不能破坏 API 或导致其他破坏行为，因为如果底层包破坏了 API，来自消费者包的自动测试将会失败。因此，拒绝让您将任何东西合并到生产中。

通常，当你在不同的回购协议中处理功能时，你会做出一些根本不必要或不值得争论的重大改变。假设您在您的公司中采用测试策略，monorepo 有助于让您了解破损情况，并让您决定是否值得更改您甚至可能不打算更改的 API 更改。

## 使用反馈回路来解决问题

由于大多数包会遵循相同的开发人员命令来执行基本任务，它还会激励您实际修复消费者包中的破损部分(“您破坏了它，您就修复了它”)，从而节省团队不必要的同步时间，因为经典的“其他人需要在[此处插入编造的截止日期]之前实现此更改。”

这是我喜欢 monorepos 的部分，但它并不适合所有人。

# 单一回购的定义

由于我已经看到术语“单一回购”用于各种设置，我将澄清我认为是一个共同的定义。

具有相似技术堆栈和平台的多个包，即多个后端服务和/或多个微前端应用，具有各自的版本和指定的依赖关系。确保对共享依赖项的更改不能部署到生产中的工作流，除非其依赖项已经构建并通过了自动测试。
·某种程度的工具集中化，标准化了像构建一个包或安装第三方包依赖项这样的常见需求。
在 monorepo 工作的多个团队

# 这归结于文化

很多人会同意 monorepo 听起来是个好主意，但他们也容易忘记 monorepo 只是一个协作工作场所的推动者。

在我工作的一家公司，组织中的一部分人相信单向回购的协作能力以及其他好处，但最初也有人怀疑我们是否会将宝贵的自治团队结合在一起。

我们开始使用 monorepo 进行新的开发，随着时间的推移，开发团队增加到了 14 个，大约有 50 万行代码。

每当我们面临挑战时，是否继续在 monorepo 中工作的争论就会浮出水面，但真正的问题不在于 monorepo，而是我们对它的天真实现，以及缺乏改进的动力。

对于一些人来说，单一回购中的问题是一个极端的负担，因为他们在很大程度上要对单一回购负责，加上不愿合作(不[合作](https://medium.com/design-bootcamp/the-difference-between-collaboration-and-cooperation-8d277bc8f598))，单一回购甚至在今天仍在挣扎。

我想回顾一下我们和你一起犯的一些错误，以帮助你做好准备。我还将向您展示，这些问题大多是工程和文化挑战，而不是 monorepos 不起作用的论点。

# 情况 1:一个团队不断破坏主分支

![](img/d9eace6cea3b28b2de49e8f1f21fd49b.png)

哦哦，对你的库的一个无害的改变被一个古怪的依赖阻止了。照片由[杰克逊煨](https://unsplash.com/@simmerdownjpg?utm_source=medium&utm_medium=referral)于[未煨](https://unsplash.com?utm_source=medium&utm_medium=referral)

让我给你举几个例子。毕竟，一个拉请求怎么可能被合并，然后导致主分支失败呢？

如果您在前端使用过静态站点生成，您会知道使用它构建站点有时需要在构建时进行大量后端调用，脆弱的后端服务(或偶尔破坏 API 的服务)很容易影响您的整体构建稳定性。

我们的一些团队在做 SSG，对于一个项目来说，它似乎永远不会停止断断续续地破坏主要分支。最坏的情况是，这阻止了团队将变更发布到应用程序所依赖的共享库中。

在这种情况下，很容易说单一回购没有规模。毕竟，你不希望一个主要分支机构倒闭，而 SSG 似乎总是在引发失败。

我们在这里应该讨论的是弹性和依赖性的消除。如果你有一个你不能控制或影响的不稳定的后端，从静态站点生成步骤中删除它，或者至少为它添加一些重试逻辑。

如果您拥有后端服务，可以考虑将它转移到 monorepo 以加强 API 变更反馈循环，或者如果它因为外部服务而变得脆弱，可以添加一些缓存或其他保护措施来防止这种脆弱性。

# 情况#2:在 Monorepo 中更新外部依赖关系导致生产失败

在 monorepo 中:如果你不写测试，没有人会保证你代码的安全性。如果你依赖 monorepo 之外的东西，你将无法保证它们不会破坏大量的测试套件。

在这种情况下，想象一个设计系统组件库，由于某种原因，它位于自己的存储库中。他们经常发布新的版本。经常出现意外破损(他们怎么可能知道所有东西破损的情况？它们在 monorepo 反馈回路之外)。

虽然对设计系统团队来说，创建某种测试版发布过程可能很诱人，但是您只是延长了发布周期，并冒着在等待发布时引入更多破坏的风险。此外，当紧急修复出现时，有两个发布轨道会有更多的开销。

另一方面，如果你决定为每一个小的和潜在的突破性的变更创建一个测试期，你将很快陷入经典的公告中:“所有的团队在这个(假想的)截止日期之前必须使用最新的版本”，接着是“当我们做出变更时，没有人在测试。”

沟通是一种重要但有限的资源，团队会自动推迟大量的工作，而回报却很少，这让你感到沮丧，可能会试图为这些测试阶段设置更严格的规则，这很可能只会进一步降低积极性和生产力。

## 将您的依赖项移近您的团队和 monorepo。

现在，假设我们将组件库(一个中央依赖项)移动到 monorepo。

帮助团队认识到这一点是非常重要的，如果 monorepo 中的中心依赖发生了变化，他们的消费者应用程序崩溃了，这并不是因为中心依赖的变化！如果您的测试套件发现了重大变更，那么底层的包将会执行那些测试，而做出那些变更的团队将无法将他们的变更合并到生产中。

拥有这种心态并把它带给你的团队，创造了一种对良好的测试覆盖率的重要性和真正的紧迫感，并且我要说，在我多年的开发中，我从来没有见过一种更强大的方式来让那些(大多数是不情愿的)妥协或者甚至不写测试的团队开始写测试。

任何认为编写测试是他们无法承担的成本的人都暗示着他们想要一个不可维护的应用。后来，当他们意识到他们实际上需要可维护性时，就越难从中恢复，因为编写可测试的代码和从一开始就没有考虑测试方面的代码之间有很大的区别。

如果你不习惯编写测试，我在这方面的建议通常是把重点放在单元测试上，不要创建太多大型复杂的一体化测试(它们会变得脆弱和缓慢)。如果你还没有编写一个测试，可以通过增加一些单元测试或者一个非常小的浏览器测试(如果有必要的话，模拟后端)来逐步完成。用柏树很容易)。

认识到您的代码需要在哪里进行更改以使其更具可测试性，不要被您可能错过的大量测试所淹没。

# 情况# 3:mono repo 构建缓慢，影响您的部署和发布

由于远程构建缓存、选择性构建计算和各种其他手段，monorepo 构建性能缓慢的神话早已被揭穿并解决了许多次，但记住在 monorepo 中更改共享依赖关系将为该包的每个消费者触发许多测试套件仍然很重要。当然，您想测试所有受影响的包，但是您能做些什么呢？这里有几件事值得一提。

## 高流失率的共享依赖关系

如果你有一个共享依赖关系经常改变的情况，并且被许多消费者使用，让我们试着理解为什么。

在我参与的 monorepo 中，我们认为拥有一个中央前端 REST 客户端来简化我们跨 monorepo 的网络请求，并在后端 API 发生变化时检测重大变化是一个好主意。它从开放的 API 模式生成前端代码([包链接](https://www.npmjs.com/package/openapi-typescript-codegen))。

我们意识到，由于团队重新生成他们的后端模式，这个包发生了很大的变化，它不可伸缩，并对我们的构建时间产生了负面影响(中央包的变化会触发所有消费者的构建，记得吗？).

然后我们问自己，我们实际上想要集中什么？

我们希望确保团队不会在多个地方将一个微服务生成到前端代码中。如果有人更改了后端模式，一些团队将不会意识到这一点，如果他们已经生成了不再存在的旧后端 API 的代码，他们的测试套件将永远不会针对新的后端 API 执行。

我们还想集中生成脚本。因此，我们最终做的是在 monorepo 中创建一个工具来进行生成，并跟踪每个微服务的生成代码在哪个输出路径中。

然后，我们让团队创建尽可能多的 REST 客户端，在公司的消费者区域之间划分它们(例如，B2B rest 客户端和 B2C rest 客户端)。

长话短说，虽然这听起来很有诱惑力，但如果他们也有很多客户，你最好把它进一步细分到每个消费领域。您也可以将这种思想应用到一个大型组件库中，并考虑您最终会如何做。

## 停止将发布与您的部署耦合

![](img/cd16c10706a18ec57027b4ed9c834b8e.png)

按下按钮后，释放应该需要几秒钟。Diomari Madulara 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是一个大问题，值得重复多次。

如今，在发布时等待部署完成并不是发布特性的最现代的方式。对于回滚来说更是如此，在等待“回滚构建”完成的每一分钟，都可能导致巨大的收入损失。

取而代之的是，加入某种发布管理工具，或者管理发布快照，或者使用特性标志，也称为特性切换。

特性切换(对于不知道它们是什么的人，我有时称之为“美化的 if-conditions ”)是控制发布的一种很好的方式。

您可以使用它来发布您的代码更改，使用一些任意的规则(“只为管理员用户启用这个特性”)并在单击一个按钮时关闭这个特性。部署可以在实际发布前一周完成，因此更长的部署时间变得无关紧要。

如果问题“缓慢的构建影响了我们的发布和回滚”出现在你的公司中，尝试降低构建时间是解决错误的问题，虽然通常这样做很好。相反，问题陈述应该是“我们没有发布和回滚策略”，因为尽管听起来很苛刻，但您不能真正将 CI 管道计算在内，因为它不是发布管理的正确工具。

此外，使用特性切换使您能够使用更少的测试环境，因为代码变更之间的分离发生在代码级别，这在协作方面发挥了很好的作用。

# 摘要

至少需要某种程度的合作文化，才能让 monorepos 真正成为一个可行的选择。对于组织来说，知道在工具和开发人员经验上花费时间从长远来看是值得的也没有坏处。

这些都是我在一个分裂的公司的经验之谈，我希望这可以帮助你找到更好的立足点，如果你想和我做同样的事情，但一开始并不真正了解它——一个由团队组成的公司，他们觉得通过合作获得了力量。

从用户界面的角度来看，拥有一个具有更标准化规则和模板包的前端 monorepo 将导致一个更一致的网站，这也是为什么 monorepo 可以通过强大的协作文化来帮助你的产品的另一个强有力的论据。

## 如何测试自己的协作水平

不要一味追求单一报告，试着创造一些有用但不完整的东西，比如用 yeoman 这样的工具创建一个项目生成器。目标将是尝试标准化并帮助团队更有效地使用它，以及看看他们是否尝试对它做出贡献，并将它视为活的文档，使您的技术方向合法化。如果你得到了不错的牵引和反馈，你可能会有一种可以驱动强大的 monorepo 的文化。

如果你没有获得任何动力，也许你在其他地方会更好，或者你需要在公司内部找到想要让文化更加协作的强大盟友。记住，所有的交流都是从你自己开始的，记下你成功说服他人时使用的修辞——因为你肯定会在出现单边回购辩论时重复很多次。

所有这些都是关于敏捷的，我的好朋友和以前的同事 Henrik sthl[经常](https://medium.com/codex/move-fast-and-break-things-is-sadly-misunderstood-f6684a55661a) [写到](https://bootcamp.uxdesign.cc/7-things-to-do-to-increase-agility-986927a46a56) [关于](https://bootcamp.uxdesign.cc/7-ways-to-fuck-up-your-agile-transformation-d521cb5dee53)。

# 奖励:我最喜欢的 Monorepo 管理工具

我是 rushjs 的强烈支持者。就像 monorepos 的打字稿。

Rushjs 帮助在 monorepo 中构建大型组织。例如，它确保团队不依赖于 10 个不同的 react 版本(尽管对于每个特定的依赖，您总是可以选择不使用它)。它还具有远程构建缓存和许多其他设计良好的特性。

感谢阅读。