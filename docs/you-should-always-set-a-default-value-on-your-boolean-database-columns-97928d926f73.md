# 您应该始终在布尔数据库列上设置默认值

> 原文：<https://betterprogramming.pub/you-should-always-set-a-default-value-on-your-boolean-database-columns-97928d926f73>

## 为什么 null 不总是正确的解决方案

![](img/79e28912dbe36c78fe57c79cdf07b5e6.png)

克里斯·巴尔巴利斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当我看一个模式时，有一件事总是让我恼火。

活动可以有 3 个值:真、假和零

当我创建一个用户时，我希望填充最重要的列。

`active`是一个重要的值，因为这个信息可以改变我的应用程序的整体行为。

我把`NULL`称为“我不知道”，这个答案对于本专栏来说是不可接受的。

对于布尔值，应该避免使用`NULL`。

在 SQL 中，你不能将`NULL`与`TRUE`和`FALSE`进行比较。

*   `NULL AND TRUE IS NULL`
*   `NULL AND FALSE IS NULL`
*   `NULL AND TRUE OR FALSE IS NULL`

我该怎么做才能避免这个问题？

嗯，你应该给你的布尔值添加一个默认值，并期望它不能有一个`NULL`值。

现在 active 直接设置为 true，只能改成 false。

有了这个解决方案，您的用户将总是拥有最重要的价值，您将不再遇到奇怪的行为。

有关 SQL 的更多信息，您可以查看 [Null (SQL)](https://en.wikipedia.org/wiki/Null_(SQL)#Three-valued_logic_.283VL.29) 或这个关于关系代数的[微软文档](https://docs.microsoft.com/en-us/previous-versions//cc966426(v=technet.10)?redirectedfrom=MSDN)。