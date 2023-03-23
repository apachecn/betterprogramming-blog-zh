# 露比死了。等等，真的吗？

> 原文：<https://betterprogramming.pub/ruby-is-dead-wait-really-60ca85b8dd06>

## 对 Ruby 发展现状的矛盾印象

![](img/8ba7c716dac444946841dce69001530d.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[静止不动](https://unsplash.com/@stillnes_in_motion?utm_source=medium&utm_medium=referral)

对于许多观察家来说，Ruby 似乎在走下坡路。但是，对于许多 ruby 爱好者来说，这门语言将迎来第二个春天。这是什么原因呢？让我们来了解一下！

# 露比死了吗？

近年来，Ruby 的受欢迎程度在各种“流行编程语言”指数上有所下降，如 [Tiobe](https://www.tiobe.com/tiobe-index/) 或 [Pypl](https://pypl.github.io/PYPL.html) 。

Ruby 最著名的软件 Rails 是 web 开发的王者，但它最近也失去了活力。尤其是 node 的异军突起和 Django 的稳步崛起，动摇了 Rails 的地位。

Ruby 社区的另一个问题是这种语言因为“对于网络规模来说太慢”而臭名昭著。(见我对可伸缩性问题的回应[这里](/scalable-ruby-concurrency-and-parallelism-explained-68b09a7aeb53))这种声誉在 Twitter 脱离 Rails 并使用 Scala 来满足他们的需求之后尤其明显。

这一切导致了“露比死了”的谣言。但是，为什么许多 Ruby 爱好者认为 Ruby 确实有一个辉煌的未来呢？

# Ruby 辉煌的未来

## Ruby 3x3 及其未来

Ruby 的创造者 Matz 试图通过发布 Ruby 3x3 目标来阻止这种发展。天哪，这让 Ruby 社区的乐观情绪复活了！许多开发人员满怀希望地期待着这个版本。而随着《圣诞 2020》的上映，终于来了！

它实现了所有的承诺吗？嗯，算是吧。

在某些基准测试中，其性能是 Ruby 2.0 的三倍，但对于大型 Rails 应用程序来说却不是这样。但是对性能改进的投资并没有随着 Ruby 3 的发布而停止，事实上，它们还在不断增加。例如，最近，Ruby/Rails 的最大用户之一 Shopify 雇佣了一些 Ruby 开发人员在 CRuby 实现中实现 JIT 编译器。有了这个，性能应该会继续变得越来越好。此外，在 Euruko 的最后一次主题演讲中，Matz 再次强调了对性能的关注，他说 Ruby 3.x 将比 3.0 快 3 倍。

对于大型 web 应用程序，大多数时候，CPU 限制的性能不是主要问题。问题出在 IO 绑定操作上，而 Ruby 在那里做了一些巨大的改进！我们现在有一个用于纤程的 API 来构建类似绿色线程的东西。

这样，Ruby 解释器就能够同时处理许多请求，并大大提高了吞吐量。有了[异步 gem](https://github.com/socketry/async) ，我们已经有了一个参考实现，它可能成为 Ruby 标准 gem 的一部分。其他 gem 维护者还需要一点时间来使用这个新选项，但这最终将对 Ruby 生态系统产生巨大的推动作用！

在我看来，这将是所有为 3x3 目标而添加到 Ruby 中的特性中影响最大的。

[Samuel Williams](https://twitter.com/ioquatix),`async gemset`的开发者最近在推特上发布了一些[非常](https://twitter.com/ioquatix/status/1407979904071655430/photo/1) [令人印象深刻的](https://twitter.com/ioquatix/status/1407977541940367360)基准测试，展示了这种方法的可扩展性。

作为 Ruby 3x3 目标的另一个好处，Ruby 社区现在有了一些更好的 IDE 工具选择。

Stripe 开源的 [Sorbet](https://sorbet.org/) ，你的 Ruby 程序的类型检查器。如果你不喜欢冰糕，那么你可以用浸泡。这种方法适用于不同类型的独立文件，类似于 TypeScript。

## 替代的高品质宝石

Ruby 社区的另一个重大发展是，我们有了一些真正高质量的知名宝石替代品。

*   [**续作**](https://sequel.jeremyevans.net/) :这种宝石是由来已久的现役战绩的替代品。它有一个插件系统，可以加载许多附加功能。这款创业板的表现在大部分基准测试中也优于`ActiveRecord`。这个创业板的突出之处在于，几乎在任何给定的时间点都没有公开发行。维护者，[杰雷米·埃文斯](https://twitter.com/jeremyevans0)，在保持一切超级干净方面做得非常出色！
*   **Hanami** : Hanami 是另一个历史悠久的网络框架，目前正处于过渡阶段。它已经做出了一些巨大的改进，未来的计划对我来说看起来非常有希望(这里是一个核心开发者的的[演示)。](https://www.youtube.com/watch?v=20BCuKIRzLo)
*   **更小的非常专注的高质量库**:Hanami web 框架的一个基础是`dry-rb gemset`。这些是专门的库，对于几乎所有的 Ruby 应用程序来说都是有价值的补充，在这些应用程序中，任何 gem 都做一件事情并且做得很好。

## 对 Ruby 的持续投资

Ruby 的另一个希望之光是对 Ruby 持续不断的投资。如上所述，Shopify 在 Ruby 上下了很大的赌注，并雇佣了几名 Ruby/Rails 核心开发人员。

他们中的一些人正在研究一种新型的 JIT 技术，叫做 YJIT。这种 JIT 编译器背后的基本思想被称为基本块版本控制(BBV)，从事这项工作的开发人员之一，[Maxime Chevalier-bois vert](https://github.com/maximecb)，已经在她的博士论文中描述/研究过。这个 JIT 的大目标是与 CRuby 集成，并获得 Rails 服务器的性能提升。

Shopify 和 Oracle 的另一项投资是对 TruffleRuby 的研究，这是一个使用 GraalVM 技术的高性能 Ruby 实现。现在 TruffleRuby 还不能运行 Rails，因为一些 C 扩展无法运行。但是对于可以用这种实现运行的较小的脚本和程序来说，性能提升是巨大的。

正如最近在 Ruby Kaigi 2021 [关于对象形状的讨论中所看到的那样，对这种实现的研究也为 CRuby 带来了成果。](https://www.youtube.com/watch?v=RqwVEw-Rd5c)

其他投资于该平台的 Ruby 大用户包括 Stripe，它正在开发 [Sorbet](https://sorbet.org/) ，GitHub，它有几名 Rails 核心开发人员，还有 Heroku、Cookpad 等等。

有了这种对语言的共同开发，Ruby 不太可能很快就倒下了！

# 最后的想法

所以，在看了 Ruby 生态系统的现状之后，我个人认为 Ruby 有一个光明的未来，但是作为一个社区，我们必须继续努力去实现它。这项工作可以是开发和维护我们所依赖的或者期待使用的 gems 特别是使用最近 Ruby 开发所提供的改进，比如 Fiber 改进。

另一个需要改进的地方是这些 gem 的文档。因此，其他人可以开始使用替代品，并尽可能获得最佳体验。还有，让我们传播这个消息:Ruby 没有死，但有一个辉煌的未来！

*不是中等会员？* [*在这里报名*](https://grnt-grdwhl.medium.com/membership) *并支持我的写作过程！*