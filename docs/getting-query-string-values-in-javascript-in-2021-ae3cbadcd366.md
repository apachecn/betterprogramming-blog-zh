# 2021 年用 JavaScript 获取查询字符串值

> 原文：<https://betterprogramming.pub/getting-query-string-values-in-javascript-in-2021-ae3cbadcd366>

## 安全访问 URLSearchParams

![](img/201f258cf78f1bd712fed1831d7b9d68.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

过去，对于用普通 JavaScript 从 URL 的查询字符串中检索键值对这一简单问题，没有简单的解决方案。

我们来看看下面这个网址:`[http://example.com/search?someParam=someValue&anotherParam=anotherValue](http://example.com/search?someParam=someValue&anotherParam=anotherValue)`。我们感兴趣的信息由两个键值对组成:`{someParam: 'someValue', anotherParam: 'anotherValue'}`。令许多开发人员惊讶的是，并不存在具有足够大的浏览器支持的本地解决方案。这就是为什么你不得不使用像 jQuery 这样的库，或者想出自己的解决方案，这些解决方案往往变得非常复杂。

这样一个常见的任务需要一个内置的解决方案。这个解决方案被称为`UrlSearchParams`。它与大多数现代浏览器兼容。如果您需要支持 Internet Explorer，您仍然应该通过提供 polyfill 来使用它。

当您需要此功能时，您通常会发现自己处于以下情况之一。

# 获取当前网站的查询字符串值

首先，我们需要当前 URL 的搜索部分。我们可以通过`window.location.search`获得。

*注意:我们不能用* `*window.location.href*` *。原因将在下一节解释。*

输出:

```
someParam someValue
anotherParam anotherValue
```

# 获取任何 URL 的查询字符串值

这里有一个警告:如果我们想要解析一个完整的 URL，我们需要首先分离搜索参数部分(在`?`之后的所有内容)。对于我们的示例 URL，这部分应该是`?someParam=someValue&anotherParam=anotherValue`。我们使用`split`来实现这一点。

请注意，`?`是否是该字符串的一部分并不重要。`UrlSearchParams`的构造器会处理的。

输出:

```
someParam someValue
anotherParam anotherValue
```

# 结论

摆弄复杂的自制正则表达式的日子已经结束了。我们现在可以放心使用`URLSearchParams`了。对于那些必须支持旧浏览器的人来说，有很好的 polyfills。

# 参考

*   [https://developer . Mozilla . org/en-US/docs/Web/API/URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)
*   [https://开发者. Mozilla . org/en-US/docs/Web/API/Location/search](https://developer.mozilla.org/en-US/docs/Web/API/Location/search)