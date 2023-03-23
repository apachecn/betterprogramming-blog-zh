# 像专家一样过滤 React 查询中的数据

> 原文：<https://betterprogramming.pub/filtering-data-in-react-query-like-a-pro-ec481b53b515>

## 在本文中，我们将探索一些技巧，以我所知道的最有效的方式过滤和规范化 React Query 中的数据

![](img/c07a3b96dacff1d58e2c7b20a6fc4a7c.png)

照片由[纳吉布·卡利尔](https://unsplash.com/@nkalil?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 动机

本文旨在提供一些在 React Query 中过滤和规范化数据的技巧，这是我在企业应用程序中使用该工具一年多后学到的。

过滤和规范化数据是几乎所有现代 web 应用程序的重要组成部分，它相对容易实现，但当试图以可伸缩的方式完成时，可能会变得棘手。使用 React Query 的应用程序也不例外，幸运的是，该库提供了过滤和规范化数据的很好的方法，但经常被忽略。

# 初步资源

本文假设对 React Query 的工作原理有一些基本的了解。为了入门和更熟悉它，我邀请你看一看我以前的[文章](https://medium.com/p/22ccc49d61c2)。另外，看看他们精彩的[文档](https://react-query.tanstack.com/overview)和 [TkDodo 的博客](https://tkdodo.eu/)，React 查询维护者之一。

如果你对钩子的工作原理有一个非常基本的了解，你就可以开始了。

说完了，我们开始吧。

# 过滤服务器上的数据

**问题陈述—** 让我们假设一个 API 返回一个雇员记录数组，这个 API 接受两个可选的查询参数:一个逗号分隔的字符串列表叫做`postions`和一个字符串叫做`name`，API 将根据提供的查询参数过滤数据，如果没有提供查询参数，那么 API 将返回整个雇员列表。

此外，假设我们想要过滤列表，只获取销售人员，我们将使用`GET /api/v1/employees?positions=sales`查询数据，我们想要*对 UI 中的变化做出反应，根据用户在浏览器中的选择过滤数据。*

解决方案是利用 React 查询自动检索。

您可能已经知道，React Query 使用[查询键](https://react-query.tanstack.com/guides/query-keys)作为缓存从 API 获取的数据的机制:

查询关键字可用于在 API 数据发生变化时自动触发 API 数据的重新提取，因此您可以通过将过滤器添加为查询关键字来利用它们，因此每次它们发生变化时，您的数据都会被再次提取，并将过滤器传递给 API:

注意我们是如何将`filters` 存储为查询键数组的一部分的。

你可以把你的查询键数组看做一个依赖数组，每次它改变时，fetch 函数都会被调用，如果它们不变，这个函数就不会被调用。

# 在客户端过滤数据

**问题陈述—** 让我们想象一个 API 返回与上一节相同的有效载荷，但这次，API 不允许过滤，因此需要在客户端执行，我们还希望最小化 API 请求。

**解决方案，选择器—** [选择器](https://react-query.tanstack.com/guides/migrating-to-react-query-3#query-data-selectors)是 React Query 中一个不太知名但非常强大的工具，它们允许过滤和/或转换查询结果，而无需接触缓存的数据。

因此，我们可以将选择器连接到您的查询挂钩，并根据您在组件中选择的过滤器执行过滤:

注意我们是如何将第三个参数传递给“useQuery”函数的，这个对象可以包含一组`useQuery` [选项](https://react-query.tanstack.com/reference/useQuery)。在本例中，我们通过`select`属性传递一个选择器函数。

在这个 select 函数中，我们将执行组件需要的任何过滤或规范化，过滤中的变化**将不会**触发任何 API 请求

# **结论**

React Query 中的过滤是一项相对简单的任务，重要的是与您的团队保持一致，无论过滤是在客户端还是在服务器上进行，基于这一决定，您可以应用本文中的策略，以便能够以可伸缩和*反应式*的方式进行过滤。