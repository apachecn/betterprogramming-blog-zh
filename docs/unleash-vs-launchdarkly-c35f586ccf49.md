# 释放与黑暗启动:功能切换解决方案一览

> 原文：<https://betterprogramming.pub/unleash-vs-launchdarkly-c35f586ccf49>

## 介绍释放和启动黑暗，以了解他们的能力

![](img/9f366fb1bd78744205f8ff4d8910dd9d.png)

阿瑟·马齐在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的照片

在[之前的文章](/feature-toggle-introduction-68d58f5c709)中，我们介绍了特性切换的时机和使用场景。在本文中，我们将挑选两个比较知名的解决方案， [Unleash](https://www.getunleash.io/) 和[launch darky](https://launchdarkly.com/)，提供一个基本的介绍和我的经验。

在开始之前，让我简单描述一下特性切换解决方案必须具备的基本要求。

1.  易于使用的网站。
2.  易于集成 SDK。
3.  能够实现 4 种类型的功能切换。

*   释放切换:您可以百分比卷展，以实现金丝雀部署。
*   操作开关:类似于释放开关，也需要百分比调节功能。
*   实验切换:能够在切换中携带额外的信息，而不仅仅是真或假。
*   权限切换:在进行切换判断时，能够使用额外的动态参数(如用户 id)是很重要的。

Unleash 和 LaunchDarkly 都可以满足这三个标准，尽管在能力上有一些差异，但功能切换的基本操作完全不是问题。

# [释放](https://www.getunleash.io/)

Unleash 是 feature toggle 的成熟解决方案，不仅提供在线付费解决方案，还提供自托管开源解决方案。所以我相信非常适合在机构内部实验，毕竟是免费的。如果您对功能切换有良好的效果，那么您可以考虑升级到 Unleash 的企业解决方案或切换到另一个解决方案。

Unleash 的开源解决方案在架构上非常简单，只需要一个 API 和一个 PostgreSQL。系统架构中没有缓存，所以你可以理解，每次你得到一个特性 toggle，它都是直接在数据库上运行的，但是如果你只把 Unleash 应用到后端环境，只使用服务器端的 SDK，那么这个使用量确实不是什么大事。

另一方面，由 Unleash 提供的 SDK 使用轮询机制，每 15 秒请求结果，并将结果存储在每个实例的内存中。这也有效地减少了实际接触数据库的频率，但代价是更改要花 15 秒才能生效。从我的角度来看，15 秒并不是不可忍受的时期，所以完全可以接受。

使用起来也很简单，先初始化 Unleash 实例，然后就能正常工作了。以下所有示例都使用 Node.js 作为演示。

```
const unleash = require('unleash-client');unleash.initialize({
  url: 'https://YOUR-API-URL',
  appName: 'my-node-name',
  environment: process.env.APP_ENV,
  customHeaders: { Authorization: 'SOME-SECRET' },
});
```

初始化过程需要设置`environment`，但是在开源解决方案中，这个参数是无关紧要的，因为开源解决方案只提供一套环境。理想情况下，应该可以在各种在线环境下生成一组相应的设置，例如:试运行和生产。

在开源解决方案中，区分环境的唯一方法是使用如下的开关名称。

```
const stgToggle = unleash.isEnabled('featureA-stg');
const prodToggle = unleash.isEnabled('featureA-prod');
```

当触发器的数量很少时，这非常有用，但是当触发器的数量变大时，管理起来会很困难。

如何使用 Unleash 进行实验切换？我们可以通过使用 Unleash 的`unleash.getVariant`来做到这一点，它是附加到功能切换的附加信息，可以在 Unleash 网页上轻松配置。

```
const variant = unleash.getVariant('featureA');
```

另外，完成权限切换非常简单，只需在`isEnabled`时带入上下文即可。

```
const context = {
  userId: '123',
  sessionId: '123123-123-123',
  remoteAddress: '127.0.0.1',
};const enabled = isEnabled('featureA', context);
```

此外，Unleash 提供了多种不同的部署策略。

1.  标准:每一次，结果都会一样。
2.  渐进卷展:它可以设置为一个特定的比率，这样每次切换的结果都是随机确定的。
3.  UserIDs:在上下文中使用`userId`来启用满足特定`userId`的目标。
4.  IPs:在上下文中使用`sessionId`来启用满足特定`sessionId`的目标。
5.  主机:在上下文中使用`remoteAddress`来启用满足特定`remoteAddress`的目标。

到目前为止，在介绍中，我们应该能够满足释放功能切换的基本用例。然而，就切换而言,“释放”的功能非常简单，并且在使用“释放”时有几个不容易克服的挑战。

1.  只有三种特殊的上下文可用于部署策略，并且只有等于或不等于的判断，没有为大于或小于提供运算符。如果需要实现[自定义策略](https://github.com/Unleash/unleash-client-node#custom-strategies)，就得继承 SDK 的基类，自己实现。
2.  Unleash 支持在单个开关上使用多个策略，但是策略之间的关系是 OR。例如，不可能在特定位置启用对特定用户的切换，因为`userId`和`sessionId`不能是 and。
3.  当选择渐进卷展栏时，它只能设置一个特定的规则。例如，您不能按`userId`分组并启用 40%的用户，因为您不能混合多个条件。否则，你只能使用完全随机分布。

# [黑暗发射](https://launchdarkly.com/)

黑暗启动是另一种常见的解决方案。它不提供免费的开源解决方案，换句话说，它只提供付费的商业解决方案。

它非常类似于使用中的 Unleash，并且首先需要初始化。

```
const ld = require('launchdarkly-node-server-sdk');const client = ld.init('YOUR_SDK_KEY');
```

与 Unleash 不同，它不需要在初始化时设置环境参数，因为 key 已经定义了在哪个环境中。下一步是挑选相应的功能切换。

```
const user = {
  firstName: 'Bob',
  lastName: 'Loblaw',
  key: 'example-user-key',
};
const value = await client.variation('YOUR_FLAG_KEY', user, false);
```

这里有两点值得注意:launch crystally 的用户相当于 Unleash 的上下文，但 launch crystally 的用户更灵活；Unleash 的上下文只适用于那些预定义的特定属性，但 launch crystally 可以使用任何属性，只需要在管理页面上进行配置。

另一点是，Unleash 将切换和额外信息分为两种方法，`isEnabled`和`getVariant`，但在 LaunchDarkly 世界中，切换和额外信息是同一个。也就是说，你通过`variation`得到的`value`已经包含了额外的信息。根据设置，`value`可以是布尔值、整数、字符串或 JSON。

至于 Unleash 提供的百分比卷展(percentage rollout)，LaunchDarkly 也提供，而且更厉害。不仅仅是设置一个比例，LaunchDarkly 是一个复合属性 toggle，所以可以同时存在两种以上的可能性，所以可以直接在百分比设置中调整每种可能性的百分比。

此外，LanuchDarkly 提供了一个非常强大的规则引擎。与 Unleash 单调的部署策略不同，LauchDarkly 可以自由设置 AND、or、IN 等匹配操作的规则。此外，在百分比卷展期间，它还可以混合百分比和各种规则，以实现非常强的条件匹配。

由于 launch crystally 的完全自由式`user`和强大的规则引擎，基本上，launch crystally 可以实现任何你想要的应用场景。

# 结论

目前，切换更新依赖于轮询机制，因此网站上的设置需要一段时间才能真正反映系统行为。新版本的 LaunchDarkly 也提出了这种解决方案，它提供了一种流媒体机制来获得即时反馈。但是对于系统来说，还必须考虑网络环境是否能容忍这么多的持久连接。这超出了本文的范围，我就不做进一步解释了。

事实上，功能切换解决方案提供的不仅仅是切换本身，如何进行访问控制、审计日志和 SSO 以及其他附加功能也同样重要。然而，不同的支付解决方案提供了不同的支付功能，而这些功能实际上已经脱离了功能切换的需求，因此本文不介绍 Unleash 和 LaunchDarkly 的那些附加功能。

就功能切换本身而言，Unleash 提供了一个基本的实验环境，在这个环境中，您可以用很少的开销在您的组织中构建一个功能切换系统。“释放”还提供了功能切换所需的所有基本功能。对于一个刚刚起步的组织，Unleash 提供了一个很好的实验工具，可以作为一个温暖的开端。

一旦特性切换被集成到组织的开发过程中，对于需要为更多情况定制切换使用的组织来说，launch crystally 是一个很好的选择。LaunchDarkly 为设置和匹配定制规则提供了简单直观的体验。

根据我的经验，小型组织使用 Unleash 就足够了，自托管解决方案很简单，但对大多数用例来说足够了。只要没有复杂的规则匹配，就没有必要花钱购买其他的商业解决方案，但是如果你正在用开源的 Unleash 产生越来越多的管理需求，那么就有必要考虑商业解决方案并仔细评估。在我看来，黑暗地发射也是好的。