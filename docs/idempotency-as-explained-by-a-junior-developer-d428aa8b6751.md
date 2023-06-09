# 一个初级开发人员解释的幂等性

> 原文：<https://betterprogramming.pub/idempotency-as-explained-by-a-junior-developer-d428aa8b6751>

## 揭开“幂等”这个术语的神秘面纱，并讨论它在现实世界中的应用

![](img/d7d1ad57971b3778f907774f6d1e0b29.png)

本·欧布罗在 [Unsplash](https://unsplash.com/s/photos/city?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Web 开发人员不可能几个星期不在办公室听到“幂等”这个术语。这个术语在旨在构建高质量 API 服务的团队中已经变得无处不在。

许多博客帖子和文章过度复杂化了幂等性真正为应用程序*做了什么*。陷入技术术语是*而不是*我们来这里的目的。

我发现幂等性的技术定义让初级开发人员感到困惑。相反，让我们关注幂等设计对 API 真正意味着什么。

# 幂等的一个例子

我们有一个包，里面有两个红色和两个蓝色的弹珠:

💰 → 🔴 🔴 🔵 🔵

然后我们有一个朋友，吉尔，她想让我们帮她一个忙。吉尔问，“你能拿出所有的蓝色弹珠吗？”

所以我们从袋子里拿出两个蓝色的弹珠，“是的！我们取出了所有的蓝色弹珠。成功！”

现在我们有一个包，里面有两个红色的弹珠和两个蓝色的弹珠。

💰→ 🔴 🔴

下午晚些时候，吉尔回来了，想让我们再帮她一个忙:“我知道我之前问了这个问题，但是你能再把所有的蓝色弹珠拿出来吗？”

我们看了看袋子，发现里面没有蓝色的弹珠，然后回答:“是的！我们取出了所有的蓝色弹珠。成功！”

这就是幂等性在起作用。即使我们没有*移除*更多的蓝色弹珠，最终结果还是和我们移除的一样。袋子里剩下零颗蓝色弹珠。

💰→ 🔴 🔴

吉尔只关心这个。她不想知道我们移除所有蓝色弹珠的来龙去脉。她只是想知道包里的蓝色弹珠是否被清空了。

# 如果我们不采用幂等哲学会发生什么？

假设吉尔在晚上接近我们。

“哎。我知道我问了好几次，但是我小时候在一个蓝色的大理石上滑倒了，现在我想把它们都扔掉！我在想，你能不能再一次把袋子里所有的蓝色弹珠都拿出来？”

我们打开袋子，看了看，发现没有蓝色的弹珠。

💰→ 🔴 🔴

我们说，“啊，真倒霉！对不起，吉尔，我找不到要移除的蓝色弹珠。失败！”

这时吉尔发火了，“你确定吗？我想我看见你在吃饭的时候偷偷带了一个进来！让我看看包？我要去找你妈妈！你怎么能让*没能让*移走任何弹珠呢？”

这是非等幂 API 的最坏情况。因为我们找不到任何要移除的弹珠，所以我们告诉吉尔我们找不到任何弹珠，她大发雷霆。

这就是为什么考虑让您的 API 端点行为幂等很重要。

# **什么时候应该幂等？**

![](img/45e7f3fb41a81c46467eaadea4b16c7c.png)

照片由 [Gert Boers](https://unsplash.com/@geboers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/national-park?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

幂等性的正式定义是“不改变最终结果的运算”

这是幂等 API 的一大好处:不管 Jill 一天中请求多少次帮助，我们都会返回相同的结果:“成功”

想象一下，如果我们有一个非等幂的设计哲学。吉尔第一次请我们帮忙时，我们实际上已经移除了弹珠并报告成功了。但是第二次 Jill 让我们移除弹珠时，我们找不到任何要移除的弹珠，我们会报告失败。

这种不一致会让您的客户感到困惑。尽管袋子里已经没有蓝色弹珠了，但你有时会报告成功，有时会报告失败。

一般来说，要使 REST API 符合[最佳实践](https://restfulapi.net/idempotent-rest-apis/)，`GET`，`PUT`，`DELETE`，`HEAD`，`OPTIONS`和`TRACE`端点应该是等幂的。所有的`POST`端点不应该是等幂的。

除此之外，如果您的客户关心他们数据的最终状态，但是不太关心*的内部工作方式，那么幂等性是一个很好的想法。*

我发现幂等性对于一天中多次调用您的服务的客户来说非常好，特别是对于*批量添加*和*批量删除*操作。

我的理由是，在这些场景中，4xx 错误会减慢速度，并使客户最困惑。世界上的 Jills*可以*进行错误检查，以确认“404 错误-找不到任何要移除的蓝色弹珠”对他们来说是一个令人高兴的案例——但请记住，你有眼睛盯着你的包，但你的客户没有。你的客户比你更难判断什么是“快乐”的案子，什么是“悲伤”的案子。

# **什么时候不应该幂等？**

根据我的例子，幂等性似乎是一个银弹，所有的 API 端点都应该是幂等的。这与事实相去甚远。

实际上，每种情况都是不同的，有时非幂等解决方案会更好地服务于开发人员。其中包括:

*   真正关心状态和过程的客户。这些客户需要“关注”你的包。他们确实关心`Conflict`错误、`Not found`错误，并且足够聪明来决定什么是他们自己可以接受的状态。
*   内部服务。如果你已经构建了一个只与你的团队拥有的其他服务对话的内部服务，那么不要让内部服务幂等。这里的基本原理是业务逻辑发生了变化。通过创建一个对自己显式隐瞒信息的内部服务，您可以很容易地将自己设计到一个角落，并注定要进行一次艰巨的重构！
*   “沉默寡言”的客户这是一个严肃的问题，不是开玩笑的事。许多团队在他们的设计中没有考虑幂等性，因此期望每个团队都采用“哑缺省”解决方案。如果客户预计会出现错误，当他们发现通话成功时，他们可能会感到困惑和担心。在创建这些服务时，考虑客户的需求并传达您打算实现的逻辑是非常重要的。

这就是幂等性的要点。享受构建一些令人敬畏的幂等 API 的乐趣吧！

## *资源*

【restfulapi.net】T5[T6](http://restfulapi.net)