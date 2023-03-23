# 我从被黑中学到了什么

> 原文：<https://betterprogramming.pub/what-ive-learned-from-being-hacked-4440dc4bb926>

## 在安全影响到您之前，认真对待它

![](img/f37ff7a2e28c61d88195a10c075afd35.png)

迈克尔盖革在 [Unsplash](https://unsplash.com/s/photos/hacker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

是的，我被黑了。这是那种你认为只会发生在别人身上的情况。但是你猜怎么着？你是别人的别人，这可能会发生在你身上！

当我发现它的那一刻，我开始有一种复杂的感觉——就像悲伤的五个阶段。我处于一种平行的世界，试图逃避现实，希望问题从一开始就不存在，或者它会像魔法一样自行解决。

当然，这并没有发生。在花了几个小时寻找解决方案并弄清楚发生了什么之后，我学到了一些宝贵的经验。

在这篇文章中，我想与你分享它们。

# 背景

我在一家小公司工作，提供预测性维护和资产管理方面的解决方案。简单来说，我们将不同传感器的数据连接到一个网关，然后将所有收集到的信息发送到云系统进行分析。

有一个仪表板，您可以在其中可视化数据和管理系统。所有数据都存储在一个 [MongoDB](https://www.mongodb.com/) 数据库中，并通过 API 访问。

这个 API 是访问数据库的唯一方式，每个用户都有自己的由 API 生成的令牌。所有的连接都是通过 HTTPS 完成的。

几个月前，我们在一家大公司安装了该系统作为概念验证，这样他们就可以对其进行评估并决定是否购买。这对我们来说是一个巨大的机会。

经过两个月的测试，一切运行顺利。所有传感器都在按时收集数据。

但是有一天，我的一个同事打电话给我，说他无法访问仪表板。他得到了一个“无效密码”的错误。起初，我认为这可能是一个错误。可能是服务器坏了之类的。

然而，当我访问服务器时，我意识到问题要严重得多。所有的数据都被删除了。我们被黑了。

# 问题是

这是一个奇怪的时刻。困惑的感觉开始在我体内滋生，就像悲伤的五个阶段:

*   否认:这不可能发生在我身上。也许某个同事改变了一些东西，设置了不同的服务器。也许是恶作剧。但后来我想起来我是唯一能进入服务器的人。
*   愤怒:外面有很多其他公司，为什么是我们？

![](img/4c7ec94707de951de078d2c860fd5a8f.png)

照片由[卡蜜拉·金特罗·弗朗哥](https://unsplash.com/@quinterocamilaa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄。

*   讨价还价:也许我们有能力支付他们要求的价格。无论如何，这是一个小数目。一切都可以回到从前。
*   抑郁:我觉得我不适合这份工作，我在十年的软件开发中没有学到任何东西。我是个骗子。事实上，这只是骗子综合症的又一次重演。
*   接受:就是这样。我就要被解雇了，再也找不到这个领域的工作了。

所有这些感觉持续了大约五分钟，但感觉像是永恒。时间停止了。

![](img/29533b4ad85a6f358e1965f4182fccbe.png)

由 [Ben White](https://unsplash.com/@benwhitephotography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/cry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。

然后情绪平复了，我冷静下来，开始想问题是什么，怎么解决。

# 解决方案

在检查数据库时，所有的表和集合都被删除了。那里有一个名为`READ_ME_TO_RECOVER_YOUR_DATA`的新桌子。

![](img/538a9718ee3f06035404386301ffc9fd.png)

袭击者的勒索信。图片来自[帮网安全](https://www.helpnetsecurity.com/2020/07/08/mongodb-is-subject-to-continual-attacks-when-exposed-to-the-internet/)。

在互联网上做了一点研究后，我了解到这是一种非常常见的针对 MongoDB 数据库的勒索软件攻击，自 2017 年以来一直在发生[。他们攻击没有启用安全功能的数据库，并向外界开放。](https://www.zdnet.com/article/mongodb-databases-still-being-held-for-ransom-two-years-after-attacks-started/)

就在事件发生的几天前，我禁用了防火墙，并打开了 Mongo 服务来接受来自任何 IP 的连接。

我为什么要这么做？

因为我不想输入我的登录凭证来获得数据的备份。

![](img/e56760bcada1461e12bce321c5e8d2d3.png)

由 [Unsplash](https://unsplash.com/s/photos/lazy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[霍尔格链接](https://unsplash.com/@photoholgic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄。

没错。我被黑是因为懒。承认这一点我并不感到自豪。我可以说我甚至为此感到羞耻。但这是事实。

# 经验教训

在得知这次袭击后，我们警告了客户。我们一起努力从上次备份中恢复数据(有一天数据丢失)，实施来自 MongoDB 的官方[安全清单](https://docs.mongodb.com/manual/administration/security-checklist/)，更新所有系统和服务，更改所有密码，以及其他安全最佳实践。

以下是我学到的一些经验。

## 不要偷懒

仅仅为了节省五分钟，我就可能失去一份大合同和一大笔钱。不要仅仅因为不想在每次必须做某事或访问系统时都输入密码而试图节省时间。

因为这种懒惰，我整个周末都在工作，以解决一个一开始就不应该发生的问题。

## 诚实

在这种情况下，你可以考虑的第一个行动是掩盖问题。不要那样做。

我们一发现发生了什么，就警告了我们的委托人。通知所有可能受到这种情况影响的人非常重要，这样他们也可以尽早采取行动，缓解可能出现的任何问题。

通过诚实，客户和其他利益相关方可以相信你没有向他们隐瞒任何信息，并且你正在尽最大努力保护他们的数据。

由于我们没有在数据库中存储任何敏感数据，并且所有架构都是隔离的，因此我们没有受到任何损害。

## 支持

不断备份您的数据。我是说，现在就做。创建一个脚本来实现自动化。去做吧！

如果我们没有任何备份，两个月的数据和辛苦工作就会丢失。

## 默认配置

更改您正在使用的任何系统的默认配置，尤其是管理员用户及其密码。把它放在安全的地方。

## 检查更新

经常检查漏洞和安全更新。像 CVE 这样的网站在系统和程序中有很多漏洞。经常检查它们，确保你的系统是最新的。

# 结论

我希望我的经历可以帮助其他人不要犯和我一样的错误。只有短暂的分心会造成事故。

这可能发生在任何人身上。不管你有多有经验。尽管我从事安全系统工作已经有一段时间了，但我犯了一个可能是毁灭性的错误。