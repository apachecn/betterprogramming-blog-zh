# 蟒蛇短裤——生日之谜！

> 原文：<https://betterprogramming.pub/python-shorts-birthday-surprise-fe2b90d44e96>

## *你的生日聚会有 23 位客人。其中两个可能同一天生日。但是，等等，有一个奇怪的转折，你可能需要平均让 25！*

![](img/b1d5181017f1bf6f81ebdce1cdc9355e.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [SKYLAKE 工作室](https://unsplash.com/@skylakestudio?utm_source=medium&utm_medium=referral)拍摄

平均有多少人需要参加一个聚会，这样至少有两个人同一天生日？当你用蒙特卡洛模拟来处理这类问题时，Python 会大放异彩。当然，你可以找到一个概率公式(在维基百科中查找)，但模拟这种情况会很有趣，也很有启发性。

## 但是抓紧你的派对帽！

当我编写这个程序的时候，我发现了一个非常奇怪的结果，我至今仍想不明白。让我解释一下。

首先，这里有一个简短的 Python 程序，它符合所有专家的观点，并且很有意义。

```
import randomparties = 10000
hits = 0
days = list(range(365))for i in range(parties):
    guest_birthdays = random.choices(days, k=23)
    if len(set(guest_birthdays)) < 23:
        hits += 1print(hits / parties)
```

变量`days`是一个从 0 到 364 的整数列表，每个整数代表一年 365 天中的一天。对于每一方，从日期列表中随机选择一组 23 天，以创建 23 个`guest_birthdays`的列表。`choices()`函数使用“with replacement ”,这意味着所选择的日期可能会在返回的列表中重复。

通过将这个包含 23 个客人生日的列表转换成一个`set`，任何重复都将被删除，如果任意两个客人同一天生日，那么这个集合的计数将小于 23。计算出在所有 10，000 次聚会中发生这种情况的次数，其平均值略高于一半。这本身就很了不起。

# 现在事情变得奇怪了

与其让 23 个人进门，然后比较生日，不如让参加派对的人一个一个进来。

当每个客人到达时，我们会将他们的生日号码添加到一个列表中，检查他们是否与列表中已有的任何一个客人的生日相匹配。

一旦我们找到一个匹配的，我们就停止让更多的客人进来，并计算让两个客人同一天生日需要多少人。

在一万次聚会之后，你会认为找到匹配的客人的平均数量应该少于 23 个。毕竟，如上所示，有 23 个客人，匹配的几率大于 50%。

*但事实证明，在你找到任何两位生日相同的客人之前，你平均需要让 25 位客人进来！*

这里有一个简短的 Python 程序演示了这个操作。

```
import randomparties = 10000
total_guests = 0for i in range(parties):
    guest_birthdays = []
    while True:
        bd = random.randrange(365)
        guest_birthdays.append(bd)
        if bd in guest_birthdays[:-1]:
            break party_guests = len(guest_birthdays)
    total_guests += party_guestsprint(total_guests / parties)
```

对于 10，000 个聚会中的每一个，我们让客人进来(将他们的生日号码附加到列表中)，直到我们最终有两个生日匹配的客人。请注意，我们使用以下代码检查了刚刚附加的生日，以查看它是否存在于列表中较早的位置… `if bd in guest_birthdays[:-1]:`

在找到相匹配的生日之前，每场派对开门的客人数量被记录并用于计算每场派对的平均客人数量。

根据标准谜题，我完全预计客人的平均人数不到 23 人。但客人的平均数量总是在 24.6 左右，这意味着平均每次需要 25 个客人才能确保一半以上的时间有比赛。奇怪。

# 还在寻找解释！

每当我遇到这样的挑战时，我倾向于将情况推向极端，看看它是否简化了逻辑。

在这种情况下，我为二进制情况设置了代码，而不是 365 个可能的生日。如果参加聚会的人是随机的男性或女性，你需要让多少人进门才能得到两个同性？换个角度看，如果一个聚会上有两个人，他们是同性的可能性有多大？

*提示:答案不一样！*

我很谦虚地承认，对于上述两个程序之间的区别，我仍然没有一个清晰的解释，我欢迎任何即将到来的解释，希望是清晰简洁的。*你有吗？*

```
**Want to Connect?**John is the author of [Python for Numworks](https://amzn.to/3k4O39j) , [Python for OpenSCAD](https://amzn.to/3mHK0Br), [Python for the TI-Nspire CX II](https://read.amazon.com/kp/embed?asin=B09DJ7MRDX&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20), [Python for the TI-84 Plus CE Python calculator](https://read.amazon.com/kp/embed?asin=B09GJMBDMF&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20), [Python for CASIO Calculators](https://amzn.to/3mgX0js), and [many other titles](http://johnclarkcraig.com/).
```