# 极光无服务器值得吗？

> 原文：<https://betterprogramming.pub/is-aurora-serverless-worth-it-3e79eb8f7ad>

## AWS 上流行数据库的按需版本的简要分析

![](img/50472050fa8a65fe3165ec0c1493778c.png)

照片由[像素](https://www.pexels.com/photo/clear-light-bulb-placed-on-chalkboard-355952/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[皮克斯拜](https://www.pexels.com/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

Amazon Aurora 是由 Amazon 构建的关系数据库。它被认为是一个成本效益高、效率高的数据库。极光成功后不久，AWS 宣布极光无服务器。根据[官方文件](https://aws.amazon.com/rds/aurora/serverless/):

> “Amazon Aurora 无服务器是 Amazon Aurora 的按需、自动扩展配置。它会自动启动、关闭，并根据您的应用需求增加或减少容量。它使您能够在云中运行数据库，而无需管理任何数据库容量。”

这些听起来都是可以考虑的选择。我们再也不用担心数据库的扩展和更新了。但是坚持住。我们真的需要这些吗？让我们深入挖掘，了解迁移到 Aurora 无服务器的成本。

# 优势

Aurora 无服务器的卖点包括 Aurora 的所有优势，最重要的是，它在几秒钟内就可以高度扩展。当出现更多不可预测的负载时，数据库将向上和向外扩展以高效地处理事务。

我们什么时候会使用它？这对于按特定时间表工作的应用程序非常有用，例如，在开发环境中。通常，团队在工作日朝九晚五地工作。其余的时间，开发数据库是闲置的，但我们继续支付它。这是一个关闭所有未被利用的东西的好机会。

这正是 Aurora 无服务器为我们提供的服务。如果一段时间内没有任何事务，数据库将脱机。“按使用付费”的模式在这里运行得很顺利。一旦有请求传入，数据库就会再次启动。

除此之外，与非无服务器选项相比，它还受益于简化的数据库管理和配置。这些优势应该推动我们继续这样做。

# 缺点

现在让我们来考虑真正的生产应用程序。我们应该问自己的第一个问题是，我们真的需要它吗？交易数量急剧增加的情况多久发生一次？

通常我们知道需求高低的时代。为数据库提供预定义的计算能力和吞吐量就足够了。在流量稳步增长的情况下，手动扩展数据库似乎不成问题。为小的使用高峰保留一些资源将防止问题。此外，对于永久流量，预留资源总是比按需资源更具成本效益。

Aurora 无服务器的另一个缺点是预热时间长。数据库关闭后，下一个请求可能需要 30 秒。对于一个真正的生产系统来说，这是一段很长的时间。看起来你的应用程序很慢。你会为了节省几美元而牺牲积极的用户体验吗？

不幸的是，与 DynamoDB 不同，Aurora Serverless 中的数据库管理没有被 AWS 完全覆盖。然而，在 Aurora Serverless 中，主要的更新必须手动完成。配置简化了，但是不符合 AWS 生态系统中“无服务器”的定义。例如，您可能会惊讶地发现，Aurora Serverless 不支持像全局表、多主、副本和其他一些特性。这很令人沮丧，不是吗？

# 结论

分析利弊后，才能得出结论。Aurora Serverless 有利于开发或测试环境。它可以为你每月的账单节省一些钱。但是在制作上好吗？不完全是。它的潜在场景在某种程度上是边缘情况。不过，AWS 还有一个未解决的问题。如果不仅仅是为了测试目的，那么将 Aurora Serverless 作为一个具有有限特性的数据库的意图是什么？

2020 年 12 月，AWS 团队[推出了](https://aws.amazon.com/about-aws/whats-new/2020/12/introducing-the-next-version-of-amazon-aurora-serverless-in-preview/) Aurora 无服务器 v2，旨在解决现有的限制。目前它正在预览中。也许他们意识到当前版本的 Aurora Serverless 并不真正适合生产。希望在不久的将来，我们会看到 AWS 的真正的无服务器关系数据库。