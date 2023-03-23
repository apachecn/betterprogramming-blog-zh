# 如何修复“无法从 CSS 样式表读取 CSSRules 属性”错误

> 原文：<https://betterprogramming.pub/how-to-fix-the-failed-to-read-the-cssrules-property-from-cssstylesheet-error-431d84e4a139>

## 在您的 web 应用程序中使用样式表时，请避免此问题

![](img/109b9543734315227ea78ed290581a2e.png)

Graham Ruttan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

最近，一些客户在 Angular web 应用程序中遇到了一个问题，其中抛出了一个异常。因为这个问题，你不能使用应用程序的主要部分。作为一名开发人员，我希望有关于如何重现问题的明确说明，以免浪费时间。良好的客户支持或愿意分享更多细节而不是“它对我不起作用”的客户可以提供有意义的信息，使开发人员的生活更轻松。客户支持人员无法在他们的设备上重现该问题，但少数客户却一直遇到这种情况。

当这个问题被提出时，我做了我遇到类似问题时通常会做的事情。这篇文章是关于这个特殊的问题以及调试关键问题。

# 找到根本原因

*   我用的是 [Instana](https://www.instana.com/product-overview#web-browser2/) ，这是一个很棒的监控微服务和网站的 SaaS。通过我们的监控工具，我能够确认少数客户出现了错误，以及导致问题的用户操作。即使有了这些信息，我一开始也无法重现这个问题。
*   多亏了我们的监控工具，我有了错误堆栈跟踪。这样，我就找到了导致异常抛出的代码。正如您在下面看到的，这段代码查询 DOM 中的所有样式表，并对每个样式表的 CSS 规则做一些事情。

```
Uncaught DOMException: Failed to read the 'cssRules' property from 'CSSStyleSheet': Cannot access rules
```

*   我们的监控工具还提供浏览器数据，所以我检查了哪些浏览器受影响最大。原来主要是基于 Chromium 的浏览器的最新版本，比如谷歌 Chrome。由于我的许多同事使用 Google Chrome 作为开发的主要浏览器，我发现奇怪的是还没有其他开发人员偶然发现这一点。
*   没有针对此功能的测试。我试图用 Jest 的单元测试来重现这个问题，但是测试成功地通过了。
*   我在网上搜索了这个错误信息，找到了一些关于 Chrome 最近变化的有用帖子。在最新版本的 Chrome 中，CORS 安全规则也适用于样式表(类似于 Iframe 规则)。您可以加载和呈现它们，但是不能通过 JavaScript 访问从另一个域加载的内容样式表。虽然这些信息很有帮助，但我仍然不明白为什么这会是个问题。

# 如何修复错误

有了上面提到的所有知识，我脑海中形成了一个理论:如果以某种方式加载到 DOM 中的样式表不是我们的，会怎么样？这是我能想到的唯一可能导致这个问题的东西，所以我开始思考，“不是来自我们的样式表是如何加载到 DOM 中的？”

答案是:浏览器扩展。你可能知道网页扩展可以删除网站上的广告，或者提供你的浏览器所没有的功能。浏览器扩展通常构建在 HTML、CSS 和 JavaScript 等 web 技术之上。这意味着 web 扩展完全有可能从另一个域加载样式表——除非您没有使用这样做的扩展。

通过注入指向来自另一个域(例如 CDN)的 CSS 文件的样式(例如通过使用 Chrome 开发者工具),可以容易地再现该问题。如果您的 CSS 样式表与 HTML 来自同一个域，您将能够访问`document.[styleSheets](https://developer.mozilla.org/en-US/docs/Web/API/DocumentOrShadowRoot/styleSheets).get(index).[cssRules](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet/cssRules)`属性。否则，它将抛出以下错误:

```
Uncaught DOMException: Failed to read the 'cssRules' property from 'CSSStyleSheet': Cannot access rules
```

既然我们已经理解并找到了根本原因，那么解决这个问题就很容易了:只保留从同一个域加载的样式表或者其中的`href`为空(如果样式表是内联的，比如在使用 Angular 时，就可能出现这种情况):

修复发布后，我们联系了受影响的客户，他们可以确认问题不再发生。此外，我们的监控工具也没有报告这个问题的更多发生，所以我们知道我们是安全的。

# 结论

感谢您阅读这篇短文。如您所见，问题的修复可以在几分钟内实现，但是了解问题的根本原因通常比我们预期的要长。

这对你有帮助吗？请在评论中告诉我。