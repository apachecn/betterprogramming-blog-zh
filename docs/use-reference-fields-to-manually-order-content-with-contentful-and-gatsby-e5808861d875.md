# 使用引用字段手动订购 Contentful 和 Gatsby 的内容

> 原文：<https://betterprogramming.pub/use-reference-fields-to-manually-order-content-with-contentful-and-gatsby-e5808861d875>

## 以最简单的方式订购条目

![](img/d44a637400438965079bada921be4c2b.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我最近开始尝试用 [Contentful](https://www.contentful.com/) 作为一个基于盖茨比的网站的无头 CMS。到目前为止，我对这种组合的强大印象深刻。我试图解决的需求之一是手动重新排序条目的能力，因为顺序可能需要随时更新。

# 手动点餐的肮脏方式

最初，我选择在内容模型中使用一个`integer`字段来帮助手动排序。当时，这是我能想到的唯一可行的办法。按照我的要求将每个条目按升序编辑后，我就可以在 GraphQL 查询中按这个字段进行排序了:

正如您可能想象的那样，为了重新排序条目，我需要逐个更新它们，以确保 order 字段是正确的。我想继续探索 Contentful 提供了什么。

## 使用引用字段

我很想知道其他人是如何处理这个问题的，但我最终发现，通过使用一个包含许多引用的引用字段，您可以创建一个条目列表，只需拖动它们就可以重新排序。

现在，我使用引用字段的方式是创建一个名为`page order`的新内容模型，它有两个字段:`name`和`order`。`order`字段利用了内置的引用特性，允许您选择现有条目并重新排序。

多亏了 GraphQL 和片段的工作方式，以期望的顺序查询条目现在变得很简单:

## 结论

我的知识还是相当有限的，因为我才刚刚开始尝试。如果您有任何提示和最佳实践，请告诉我。如果你和我一样是第一次接触 Contentful，我希望你会发现这很有用。