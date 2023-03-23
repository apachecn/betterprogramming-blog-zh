# 软件工程中的德摩根定律

> 原文：<https://betterprogramming.pub/de-morgans-laws-in-software-engineering-b08feb897f75>

## 简化你的条件句

![](img/68e2b487e0a0eeea831ee2c6021a16ad.png)

照片由 [Artem Sapegin](https://unsplash.com/@sapegin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 什么是德摩根定律？

德摩根定律指出，特定的布尔语句可以用不同的方式写成，但效果相同。这里可以看到[的精确定义](https://en.wikipedia.org/wiki/De_Morgan%27s_laws)。然而，用我自己的话来说，德·摩根定律如下:

1.  一组被求反的*and*与一组被求反的*or 相同。*

```
(!a && !b && !c) === !(a || b || c)
```

2.一组被求反的*or*与一组被求反的*and 相同。*

```
(!a || !b || !c) === !(a && b && c)
```

这很酷，但是如何在软件中使用呢？

# 检查是否缺少最低要求

第一个规则表示一组否定的 and 与一组否定的 or 相同。当至少一个条件必须满足时，这可以在任何你想采取行动的时候使用。

在下面的例子中，我们将检查是否至少提供了用户名、用户 id 或用户电子邮件。如果没有，我们将抛出一个错误。

这里我们表示为`(!a && !b && !c)`:

```
const userName = null;
const userId = null;
const userEmail = null;
if(!userName && !userId && !userEmail)) {
  throw new Error('At least one user identifier must be passed');
}
```

这里我们表示为`!(a || b || c)`:

```
const userName = null;
const userId = null;
const userAlias = null;
if(!(userName || userId || userAlias)) {
  throw new Error('At least one user identifier must be passed');
}
```

通过使用第二种方法，我们可以简单地将它理解为“至少需要其中之一”

# 检查需求

第二个规则表示一组否定的 or 与一组否定的 and 相同。当有一系列必须满足的条件时，这可以在你想采取行动的任何时候使用。

在下面的例子中，我们将检查是否提供了一系列的需求。如果没有，我们将抛出一个错误。

这里我们表示为`(!a || !b || !c)`:

```
const requirementA = true;
const requirementB = false;
const requirementC = true;
if(!requirementA || !requirementB || !requirementC) {
  throw new Error('All of the requirements must be met');
}
```

这里我们表示为`!(a && b && c)`:

```
const requirementA = true;
const requirementB = false;
const requirementC = true;
if(!(requirementA && requirementB && requirementC)) {
  throw new Error('All of the requirements must be met');
}
```

通过使用第二种方法，我们可以简单地理解为“所有这些都是必需的”

# 摘要

德摩根定律可以帮助简化你的代码，使其更具可读性。

您可以将一系列取反的 and 改为“至少需要其中一个”，将一系列取反的 or 改为“所有这些都需要”。

# 参考

*   [德摩根定律](https://en.wikipedia.org/wiki/De_Morgan%27s_laws)