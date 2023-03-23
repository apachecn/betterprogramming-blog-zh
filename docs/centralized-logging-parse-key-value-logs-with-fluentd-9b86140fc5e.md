# 集中式日志记录:用 FluentD 解析键值日志

> 原文：<https://betterprogramming.pub/centralized-logging-parse-key-value-logs-with-fluentd-9b86140fc5e>

## 使用 FluentD 解析器从日志行中提取键值对

![](img/034e5ba59e6fe1de766f4ddb5ad34fcc.png)

由[大卫·科瓦连科](https://unsplash.com/@davidkovalenkoo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*“Fluentd 是一个跨平台的开源数据收集软件项目，最初在 Treasure Data 开发。它主要是用 Ruby 编程语言编写的。”*

[*—维基百科*](https://en.wikipedia.org/wiki/Fluentd)

在我以前的博客文章中，我解释了如何使用 FluentD 解析日志并将其发送到一个集中式日志服务器(EFK 堆栈)，解析 Rails 应用程序的多行日志，并概述了一些最常用的使用 FluentD 解析日志的插件。您可以使用下面的链接查看提到的博客帖子:

[](https://levelup.gitconnected.com/centralize-your-docker-logging-with-fluentd-a2b7e0a379ce) [## 使用 FluentD 集中您的 Docker 日志记录

### 使用 FluentD 开始将容器日志传送到集中的日志服务器

levelup.gitconnected.com](https://levelup.gitconnected.com/centralize-your-docker-logging-with-fluentd-a2b7e0a379ce) [](https://medium.com/@wshihadeh/7-tips-for-using-fluentd-for-logs-collection-c9613f7a387c) [## 使用 Fluentd 收集日志的七个技巧

### 通过应用一些最佳实践来改进您的 FluentD 配置和脚本

medium.com](https://medium.com/@wshihadeh/7-tips-for-using-fluentd-for-logs-collection-c9613f7a387c) [](https://medium.com/faun/parse-ruby-on-rails-logs-with-fluentd-9fca4df6ebfe) [## 用 FluentD 解析 Ruby on Rails 日志

### 让我们通过使用 FluentD 解析 rails 多行日志来减少日志量。

medium.com](https://medium.com/faun/parse-ruby-on-rails-logs-with-fluentd-9fca4df6ebfe) 

在这篇文章中，我将关注另一个可以用 Fluentd 解决的问题。这将提高日志的质量，并为基于解析的日志构建可视化提供更好的机会。

# **问题**

软件应用程序以几种不同的日志格式生成日志，比如纯文本日志、JSON 日志、键值以及混合格式，比如纯文本和键值行，如下所示。

默认情况下，FlunetD 会将上面的日志行视为一个长文本，您将无法根据响应的`method`或`format`来过滤 Kibana 中的日志。可以使用[正则表达式解析器](https://docs.fluentd.org/parser/regexp)或[键值解析器](https://github.com/fluent-plugins-nursery/fluent-plugin-kv-parser)从日志行中提取键并将它们添加到事件键中。但是，这些解决方案有一些限制，并且只有在您确切知道日志行中将出现的键或者日志行只包含键-值对(没有纯文本)时才有效。

在我的例子中，日志行的键是动态的，取决于请求或事件。因此，我不能使用正则表达式解析器，也不能使用键值解析器，因为日志行包含事件时间的纯文本前缀😢。

# **解决方案**

因为我找不到现有的库或解析器来帮助我解析键值日志，所以我决定构建自己的 FluentD 解析器来解析日志并提取日志消息中定义的所有键值对。

所需的 FluentD 解析器的要求简述如下:

*   源密钥应该是可配置的。解析器应该允许我不仅从消息键中提取键，而且为 FluentD 事件中定义的任何键提取键。
*   启用/禁用在提取密钥后删除源密钥。
*   启用/禁用忽略找不到关键字的日志行。
*   忽略并且不扫描键值对之前的纯文本。
*   支持两种提取键值对的策略。第一个基于预定义的分隔符，第二个基于定义的正则表达式。
*   支持忽略一些提取的密钥。(我们希望从日志记录系统中隐藏的密钥)

# **实施**

经过一些调查，我发现实现这个解决方案的最好方法是用下面的配置项构建一个 [FluentD 过滤器插件](https://docs.shihadeh.dev/ruby-gems/Key-ValueParser/)来支持所需的功能。

*   `key`:包含待解析消息的源键。默认值是`log`。
*   `remove_key`:提取键值对后删除源键的布尔指示器。默认值为`false`。
*   `filter_out_lines_without_kesy`:一个布尔值，用于在日志行不包含任何已定义的键值对的情况下删除日志行。默认值为`false`。
*   `use_regex`:启用/禁用提取键值对的正则表达式。默认值为`false`。
*   `remove_prefix`:用于删除日志消息前缀的正则表达式。默认情况下，此配置项的值为空。
*   `keys_delimiter`:用于分隔按键的字符。默认值是空格。
*   `kv_delimter_char`:用于将键与其值分开的字符。默认值为`=`。
*   `filtered_keys`:将包含在 FluentD 事件中的白名单键。
*   `filtered_keys_regex`:定义白名单键的正则表达式。

将 FluentD 插件与 FluentD 配置集成起来非常简单，不需要太多努力。以下步骤总结了成功集成 FluentD 过滤器和粘贴脚本所需的操作。

*   安装 gem 并使其可用于您的 FluentD 脚本。如果您在 docker 容器中使用 FluentD，请确保宝石包含在 docker 映像中。下面的例子展示了如何用`fluent-plugin-filter-kv-parser`构建一个 FluentD docker 映像。

*   扩展 FluentD 配置，开始解析和过滤日志消息。可以添加的最小配置如下面的代码片段所示。

使用上面的过滤器定义，gem 将为每个定义的配置使用默认值。你可以在这里找到默认值。这意味着 ***log*** 键将被用作提取键-值对的源键，并且不会从 Fluentd 中删除。将使用默认分隔符提取键，而不使用定义的正则表达式，所有提取的键都将添加到 fluent 事件中。

扩展 FluentD 过滤器的配置以改变键提取的行为可以简单地通过在过滤器定义中添加所需的配置项来完成。例如，下面的定义将过滤日志消息，只批准与给定正则表达式匹配的键。

如上面的代码片段所示，只有匹配定义的正则表达式的键被提取并复制到输出数据中。另一方面，如果您希望将提取的密钥限制为预定义的密钥，您可以使用以下配置。

键值 FluentD 过滤器还支持定义和混合配置。例如，可以将带有正则表达式和精确名称的键同时列入白名单。在这种情况下，过滤器将包括所有匹配正则表达式的键或所提供的精确键。

# **结论**

FlunetD 是一个很棒的工具，可以用来解析日志并将它们发送到一个集中的服务器。有很多插件和库可以改进日志解析，解决很多与日志解析相关的问题。此外，通过实现新的 FluentD 插件或过滤器来扩展日志解析的功能相对简单(你需要一些 Ruby 经验😆).

你可以在 [Github](https://github.com/wshihadeh/fluent-plugin-filter-kv-parser) 和我的[网页](https://docs.shihadeh.dev/ruby-gems/Key-ValueParser/)上找到键值宝石。非常感谢反馈和改进 FluentD 过滤器的想法。