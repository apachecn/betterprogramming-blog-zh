# 使用 Elasticsearch 创建简单的自动完成功能

> 原文：<https://betterprogramming.pub/create-a-simple-autocomplete-with-elasticsearch-852c47109deb>

## 如何使用文本字段数据类型和标准分析器通过 Elasticsearch 创建简单的自动完成功能

![](img/ef6e9e58808cc1ebd6ea7edae431687a.png)

Solen Feyissa 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自动完成是一种预测用户正在键入的单词的剩余部分的功能。这是一个需要实现的重要特性，它可以改善用户对产品的体验。

如果您从未创建过自动完成功能，那么一开始创建它可能会令人望而生畏。但是借助 [Elasticsearch](https://www.elastic.co/elasticsearch/) 中的功能，这其实是一件很简单的事情。

# 你应该知道的事情

如果你对 Elasticsearch 了解不多，建议你先看看我的其他文章。我们不要求这样，但是了解分析器和文本字段的工作方式肯定会帮助您理解本文。

文章“[面向开发者的 Elasticsearch 基础知识](https://medium.com/swlh/basics-of-elasticsearch-for-developer-a23de84c4b6)”**将向您介绍 Elasticsearch。文章“[Elasticsearch:Text vs . Keyword](https://medium.com/better-programming/elasticsearch-text-vs-keyword-2ccb99ec72ae)”**将教你 Elasticsearch 中文本和关键字的区别，也将解释 elastic search 的分析器是如何工作的。****

# ****设置****

## ****创建索引****

****首先，让我们创建一个名为`autocomplete-example`的索引。我们将在本文的例子中使用这个索引。****

## ****定义映射****

****在索引文档之前，让我们首先定义一个映射。我们将只需要一个字段`simple_autocomplete`，字段数据类型为`text`，并将使用一个`standard`分析器。****

****因为 Elasticsearch 默认使用`standard`分析器，所以我们不需要在映射中定义它。****

## ****为文件编制索引****

****让我们给一份文件编个索引。对于本文中的示例，我们只需要一个包含文本“香港”的文档。****

# ****使用匹配查询查询索引****

****先说我们平时用的查询，`match`查询。****

****在将索引文本存储到倒排索引之前,`standard`分析器会将索引文本小写，并将文本拆分为停用词上的标记。****

****默认情况下，`match`查询将使用`index-time`分析器，因此它使用的分析器与索引中索引的分析器相同，即`standard`分析器。****

****让我们用 Elasticsearch 提供的 API 来看看我们的“香港”文本在倒排索引中的样子:****

****当我们使用`match`查询对索引进行搜索查询时，只有当我们键入包含“Hong”或“Kong”的文本时，才会得到结果这是因为只有当分析的查询与倒排索引中的标记完全匹配时，Elasticsearch 才会返回结果。****

****如果用户输入“Ho”、“Kon”或“Hon Kon”，Elasticsearch 不会有任何响应。****

****对于自动完成来说，这对于帮助用户不是很有用，对吗？至少，autocomplete 需要显示一些内容，即使我们没有输入完整的单词。****

****为了解决这个问题，我们可以使用 Elasticsearch 提供的`match_phrase_prefix`查询。****

# ****使用匹配短语前缀查询****

****`match_phrase_prefix`查询将允许用户在不输入所有单词的情况下得到一个结果。通过使用通常的`match`查询，如果我们键入“Hon”或“Kon”，我们不会从 Elasticsearch 得到任何结果，但是使用`match_pharse_prefix`，我们可以得到一个结果。****

****这种自动完成仍然有一个缺点:如果用户键入“Hon Kon”，它仍然不会返回任何结果。这是因为“Hon Kon”不是“香港”的前缀****

# ****事物的利与弊****

****带有`text`字段数据类型和`standard`分析器的自动完成非常简单，但是在使用这种类型的自动完成之前，您可以考虑它的利弊。****

## ****赞成的意见****

*   ******无需设置:**您甚至不必定义任何映射，因为默认情况下，如果您将一个文本文档索引到 Elasticsearch，它将被映射到`text`和`keyword`字段数据类型。****
*   ******快速索引时间**:因为这种类型的自动完成使用的是`standard`分析器，所以在将文本保存到倒排索引中时，它不会对文本进行太多处理，这意味着快速索引时间。****
*   ****大多数时候足够了:大多数时候，你不需要复杂的自动完成功能。这种自动完成类型就足够了。****

## ****骗局****

*   ******不能处理错别字**:这种类型的自动完成不能处理错别字，所以如果用户输入一个错别字，它不会返回任何结果。****
*   ******查询不能从中间词**开始:查询到这类自动完成的文本也不能从中间词开始。在前面的“香港”示例中，如果我们使用文本“ong Kong”进行查询，Elasticsearch 不会返回任何结果。****
*   ******不能处理空格:**如果我们在前面的例子中错误地输入了“HongKong ”,那么 Elasticsearch 就不会用这种类型的自动完成功能返回任何内容。****

# ****何时使用****

****当您只需要一个简单的自动完成时，我推荐只使用`standard`分析器的自动完成。如果要为其创建自动完成的索引已经在生产中，并且已经用文档编制了索引，也可以使用这种类型的自动完成。因为这个自动完成功能使用默认的分析器和默认的文本映射，所以它适用于大多数文本文档。****

# ****结论****

****用`text`字段数据类型和`standard`分析器创建一个自动完成是我们可以用 Elasticsearch 构建的最简单和最容易的自动完成。它几乎不需要设置，通常可以为现有索引创建一个自动完成功能。****

****即使对于大多数用例来说已经足够了，但它仍然有很多弱点，因为它只能处理简单的查询。为了克服这一点，我们可以使用一个自定义的分析器或 Elasticsearch 中的 Suggesters 特性，这是我打算写的。请等一下！****

****最后，我想说谢谢你一直读到最后。希望这篇文章能对你的项目有所帮助。****

# ****参考****

1.  ****[https://op ster . com/elastic search-glossary/elastic search-auto-complete-guide/](https://opster.com/elasticsearch-glossary/elasticsearch-auto-complete-guide/)****
2.  ****[https://www . elastic . co/guide/en/elastic search/reference/current/query-DSL-match-query-phrase-prefix . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query-phrase-prefix.html)****
3.  ****[https://www . elastic . co/guide/en/elastic search/reference/current/query-DSL-match-query . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-match-query.html)****
4.  ****[https://www . elastic . co/guide/en/elastic search/reference/current/analysis-standard-analyzer . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-analyzer.html)****
5.  ****[https://www . elastic . co/guide/en/elastic search/reference/current/analysis-standard-analyzer . html](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-standard-analyzer.html)****