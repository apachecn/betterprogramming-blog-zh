# 为什么我喜欢在数据库表上使用 UUIDs

> 原文：<https://betterprogramming.pub/why-i-like-using-uuids-on-database-tables-ccab8c350f8a>

## 无论是为了安全还是营销，你的团队都应该使用它们。

![](img/c81691066596a266b269a3164d587c58.png)

在处理关系数据库时，我们通常给表分配自动递增的整数 ID:第一行的 ID 为 1，第二行的 ID 为 2，依此类推。让我用一个假的 CRM 应用程序来说明这个问题:

今天是这个月的最后一天，我们想找到客户的电子邮件地址，给他发一份账单。我们打开 CRM，浏览用户界面，点击他的名字。然后我们会被重定向到他在系统上的特定页面:

`https://example.com/client/[**1**](#)`。到目前为止，一切都按预期运行！

查看 URL，很容易就能知道我们的客户 ID 是什么: **1** 。我是一个好奇的人，所以我将把数字 1 改为 2，看看会发生什么……正确的行为应该是不显示该客户端(因为`403`未被授权，或者，理想情况下，因为没有找到 HTTP `404`)。

不幸的是，我们假定的 CRM 开发人员忘记了验证这个特定的路由，所以我们能够看到客户端 2 的数据，这是一个巨大的安全问题！

> 这样的漏洞经常发生，因为代码库非常大，很容易忘记一行简单的代码。例如，在 Rails 中，正确的代码行是`currentUser.clients.find(id)`，而易受攻击的代码行是`Client.find(id).`，发现区别了吗？相当小…

此外，由于我的客户 ID 是 1，我想这个 CRM 相当小，因为我是第一个在其中创建客户的人，所以我可能想迁移到一个更流行和更可靠的系统...这样，营销团队就会抱怨开发商失去了他们辛苦赢得的客户！

# 使用 UUIDs 如何解决这个问题？

> 快速提示:如果你从未见过 UUID，它是一个类似`34e80691-9878-4e78-a1f2-eea8981984ef`的随机字符串，所以它的前面和后面都没有数字；相比之下，对于整数，我们都知道数字 2 的前面是 1，后面是 3。

当我们使用 UUIDs 时，我们将 URL 的 ID 从连续的整数变为随机的字符串。所以用户 1 的页面看起来像是`[https://example.com/client/34e80691-9878-4e78-a1f2-eea8981984ef](https://example.com/client/34e80691-9878-4e78-a1f2-eea8981984ef)`而不是`[https://example.com/client/](https://example.com/client/34e80691-9878-4e78-a1f2-eea8981984ef)1`。

在那个巨大的东西后面(或前面)没有数字！这是一个随机的无序的字符串。因此，即使 CRM 开发人员忘记验证路由，也很难有人利用这个漏洞:没有人知道用什么来替换 UUID！还有，没人知道我们平台有多少用户，所以……营销团队快乐！

# 这是否意味着我们应该将主键从整数 id 改为 UUIDs？

关于这一点我听到过一些争论，但是，在我看来，**没有！**

如果我们将主键更改为整数以外的值，我们可能会损失查询性能，因为整数比 SQL 查询中的字符串更快；相反，我创建了一个名为 UUID 的单独列，并在所有用户公开的路线上使用它。

要了解如何在 Rails 上[实现 UUIDs】，请阅读我的文章。](https://fuchs.dev.br/adding-uuids-to-rails-apps-using-dry-code-and-attr-readonly-d62734d00800)

我还听说过使用整数主键的其他一些好处，比如加速 RDBMSs 的索引过程；然而，这超出了本文的范围，也超出了我对 RDBMSs 内部工作原理的了解。

# 快速常见问题

**问:如果我在 URL 上使用 UUIDs，而这个 URL 在某种程度上被转换为 SQL 查询，那么在数据库上维护这个 ID 对索引有什么帮助呢？”**

**答:**您使用 UUID 执行第一个 SQL 查询，并获得客户端的 ID！然后，您使用该 ID 进行所有其他查询…

例如，一个用户发出一个请求`GET [https://example.com/client/34e80691-9878-4e78-a1f2-eea8981984ef](https://example.com/client/34e80691-9878-4e78-a1f2-eea8981984ef).`，我们的系统接收到这个请求，对 UUID 为`34e80691-9878-4e78-a1f2-eea8981984ef`的客户端进行 SQL 查询，我们得到他的 ID 是 1。现在我们对所有其他关系查询使用主键！例如，我们可以使用它来查找该客户的所有订单。

# 总结

同时使用 id 和 UUIDs 是很好的做法。您应该将 UUIDs 用于系统之外的所有东西(即 HTTP 请求)，但继续将 id 用于内部处理(SQL 查询)。

希望这是一个有见地的帖子，如果它太令人困惑，请让我知道。

感谢您读到这里！

```
**Want to Connect?**If you ever have a great startup idea but not a software team, please, don’t hesitate to contact my company [AlmostHackers](https://almosthackers.net)! We’ll develop your software without charging money, getting equity instead.
```