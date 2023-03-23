# 通过隐藏过滤器修复包含大型数据集的缓慢 ActiveAdmin 索引页面

> 原文：<https://betterprogramming.pub/fix-slow-activeadmin-index-pages-with-large-datasets-by-hiding-filters-b88a93334a5f>

## 随着我们应用程序的增长，有时我们也必须重构我们的管理界面

![](img/dd42ea9b98283338993341af2dcf1258.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我最近在使用 [Active Admin](https://activeadmin.info/) 时遇到了一个特殊的情况，特定资源的索引页面加载非常慢。在尝试上下搜索，试图找出问题所在后，我终于发现问题出在过滤器上。

为了解释得更清楚一点，让我们假设我们有一个特殊的案例，在您的项目中，一个*客户端*有许多*事务*，而我们有成千上万的事务。

现在，由于我们有一个拥有如此多事务的客户，每次访问客户的索引页面时获取所有这些记录都要花费大量时间。在索引页面上，您可能有许多过滤器，其中一个过滤器可用于根据事务过滤客户机。

由于我们有如此多的记录，我们可以简单地隐藏这个过滤器，这样我们就不再试图根据交易来过滤客户。为此，我们可以简单地写:

现在，我们不必再等那么长时间来加载客户端页面。

您仍然可以通过在事务的索引页面中使用`client` 过滤器来过滤不同客户的事务。

您有相当少的客户端，并且结果的事务也是分页的，所以这仍然可以为您提供一种过滤结果的方法，而不必牺牲这个过滤器。

我希望那有帮助。