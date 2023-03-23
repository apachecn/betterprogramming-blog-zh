# 从 Node.js 中的 API 返回 CSV 内容

> 原文：<https://betterprogramming.pub/returning-csv-content-from-an-api-in-node-js-3dafe19208e5>

## 传输内容的简要指南

![](img/f73219f103be59ed76a164e3263fde41.png)

照片由[内森·达席尔瓦](https://unsplash.com/@silvawebdesigns?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

一些系统和平台允许您从 CSV ( [逗号分隔值](https://en.wikipedia.org/wiki/Comma-separated_values))文件导入数据。这是因为 CSV 是最流行的人类可读格式之一，尤其是在数据科学方面。

通常的情况是，将 API 或查询返回的数据转换成有效的 CSV 文件。这涉及样板代码或手工工作。幸运的是，你可以避免这一切。

这是因为您可以直接使用 Node.js 生成 CSV 作为输出，允许用户下载一个 CSV 文件。这是一个自动化且更实用的解决方案。

现在让我们学习如何构建一个 API 来返回 Node.js 中的 CSV 内容。

# 先决条件

您不需要任何额外的库来将 CSV 内容返回到 Express 服务器。同时，您可以采用`csv-generate` npm 库来简化 CSV 的生成。

您可以使用以下命令安装`csv-generate`:

```
npm instal csv-generate
```

这不是必需的；在这里，您将看到如何在 Node.js 中生成和返回 CSV 内容，而无需额外的依赖。

# 构建在 Node.js 中返回 CSV 文件的 API

`[Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)` HTTP 头用于指定返回内容的[媒体类型](https://developer.mozilla.org/en-US/docs/Glossary/MIME_type)。如这里的[所述](https://tools.ietf.org/html/rfc7111)，返回 CSV 的 HTTP 请求的推荐媒体类型是`text/csv`。

此外，您可以使用`Content-Disposition` HTTP 头来指定 API 返回的内容是应该下载并保存在本地的附件。

可以在 Node.js 中设置这两个 HTTP 头，如下所示:

将`<YOUR_FILE_NAME>`替换为 API 返回的 CSV 文件的名称。

还要记住，CSV 文件通常非常不同。您可能需要使用不同的分隔符、引号、转义字符、行分隔符、编码等来生成文件。注意`csv-generate`允许您自定义 CSV 文件的输出格式。

现在，让我们看看在 Node.js 中返回 CSV 内容的 API 是什么样子的:

为了简单起见，用于生成 CSV 内容的数据是硬编码的。通过执行查询或调用 API，您可以在现实场景中生成这些数据。

然后，`csvData`字符串变量用 CSV 头初始化。这不是强制性的，取决于您希望输出的内容是什么样子。此外，这里生成的 CSV 内容使用分隔符“，”分隔符，但任何其他有效的分隔符也可以。

请注意，`[join()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)` JavaScript 函数会自动将`null`和`undefined`值转换为`""`空字符串。这正是`null`或缺失内容以 CSV 格式表示的方式。

现在，调用`/users/getCSV` API，您将获得以下内容:

```
name,surname,age
Patricia,Smith,
John,,56
Maria,Brown,37
```

如您所见，API 生成的输出是有效的 CSV 内容。在浏览器中调用时，会自动下载`users.csv`文件。

瞧啊！您刚刚学习了如何在 Node.js 中返回 CSV 内容。

# 结论

从 API 返回 CSV 内容比检索所需的数据然后将其转换成所需的输出要高效得多。您可以使用 Node.js 直接返回一个 CSV 文件。

感谢阅读！我希望这篇文章对你有所帮助。请随意留下任何问题、评论或建议。