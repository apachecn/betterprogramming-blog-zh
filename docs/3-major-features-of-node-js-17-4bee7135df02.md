# Node.js 17 的 3 个主要特性

> 原文：<https://betterprogramming.pub/3-major-features-of-node-js-17-4bee7135df02>

## Node.js 17 新特性的详细信息，包括 OpenSSL 3.0 支持和 V8 JavaScript engine 9.5 特性

![](img/5cce43c8d4e4b219602fb18789a5fe37.png)

照片由[弗朗西斯科·加拉罗蒂](https://unsplash.com/@gallarotti?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Node.js 主版本每六个月更新一次。新版本变成了为期六个月的`Current`版本，这给了库作者时间来添加对他们的支持。

六个月后，奇数版本(如 17)变得不受支持，偶数版本(如 16)进入`Active LTS`(长期支持)状态，并准备好供一般使用。`LTS`发布通常保证关键缺陷将在总共 30 个月内得到修复。生产应用程序应该只使用`Active LTS`或`Maintenance LTS`版本。

[Node.js 17](https://nodejs.org/en/blog/release/v17.0.0/) 发布于 2021 年 10 月 19 日。它变成了`Current`版本。它有许多主要特性:

*   OpenSSL 3.0 支持
*   V8 JavaScript 引擎已更新至 V8 9.5
*   Readline Promise API

让我们来探索它们是什么以及如何使用它们。

# 使用 NVM 浏览节点

在[之前的一篇文章](/how-to-use-nvm-to-manage-node-js-17-and-npm-8-2da8bf3ca5e9)中，我们提供了关于使用 [NVM(节点版本管理器)](https://github.com/nvm-sh/nvm)管理 Node.js 和 NPM 版本的说明。

运行命令安装节点 17.0.0:

在任何窗口上，运行命令以使用节点 17:

现在我们准备探索:

# OpenSSL 3.0 支持

Node.js 17 包含 [OpenSSL 3.0](https://www.openssl.org/blog/blog/2021/09/07/OpenSSL3.Final/) 。OpenSSL 是一个用于通用加密和安全通信的全功能工具包。它可用于 HTTPS 来保护 web 服务器和应用程序。OpenSSL 3.0 提供了 QUIC 支持。

正如我们在[HTTP/3 文章](/whats-new-in-http-3-10c2455a4f8c)中提到的，谷歌在 2012 年开发的 QUIC 运行在 UDP(用户数据报协议)之上。它提供本机多路复用，丢失的数据包只会影响数据丢失的数据流。这提高了网站加载性能。

OpenSSL 3.0 是一个主要版本，并不完全向后兼容以前的版本。大多数使用 OpenSSL 1.1.1 的应用程序仍然可以正常工作，只需要重新编译。

一些应用程序可能需要进行更改才能正确编译和工作，许多应用程序需要进行更改以避免出现不赞成使用的警告。

OpenSSL 加密库(`libcrypto`)实现了在各种互联网标准中使用的各种加密算法。

这个库提供的服务被 TLS 和 CMS 的 OpenSSL 实现所使用，它们也被用来实现许多其他第三方产品和协议。

该功能包括对称加密、公钥加密、密钥协议、证书处理、加密哈希函数、加密伪随机数生成器、消息认证码(MAC)、密钥派生函数(KDF)和各种实用程序。

OpenSSL 3.0 有一个新的 FIPS 模块。使用新的 FIPS 模块可以像修改一些配置文件一样简单，尽管许多应用程序可能需要进行其他修改。

# V8 JavaScript 引擎已更新至 V8 9.5

在 Node.js 17 中，V8 JavaScript 引擎从 Node.js 16 中的 9.0 版本升级到了 [V8 9.5](https://v8.dev/blog/v8-release-95) 。以下是主要特征:

*   `Intl.DisplayNames` API 支持的其他类型
*   `Intl.DateTimeFormat` API 中扩展的`timeZoneName`选项

## 国际机场。显示名称

`Intl.DisplayNames`对象支持不同语言的一致翻译。

构造函数`Intl.DisplayNames()`创建了一个新的`Intl.DisplayNames`对象。

```
new(locales?: BCP47LanguageTag | BCP47LanguageTag[], options?: Partial<DisplayNamesOptions>): DisplayNames;
```

需要`locales`和`options`。区域设置可以是`'en'`(英语)、`'zh'`(中文)，或者其他值。`type`是选项之一，可以定义为`'language'`、`'region'`、`'script'`、`'currency'`、`'calendar'`(V8 9.5 新增)或`'dateTimeField'`(V8 9.5 新增)。

`Intl.DisplayNames`对象有一个实例方法`of()`，它接收一个代码并返回翻译后的字符串:

```
of(code: string): string;
```

在下面的例子中，我们使用两个实例`displayNamesInEnglish`和`displayNamesInChinese`来说明`Intl.DisplayNames`是如何工作的。

类型被设置为“语言”，而`of`方法翻译一个 [unicode 语言标识符](https://www.science.co.il/language/Locale-codes.php)。

`displayNamesInEnglish`是为区域设置`'en'`和类型`'language'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'language'`创建的。

执行代码，我们可以看到`'fr'`被翻译成英文和中文的`'French'`。

对于`'language'`类型，另外`languageDisplay`可以设置为 `'standard'`或`'dialect'`(默认):`{ type: 'language' , languageDisplay: 'standard'}`或`{ type: 'language' , languageDisplay: 'dialect'}`。

类型被设置为“区域”，并且`of`方法翻译一个 2 个字母的[ISO 3166–1 个区域代码](https://en.wikipedia.org/wiki/ISO_3166-1)。

`displayNamesInEnglish`是为地区`'en'`和类型`'region'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'region'`创建的。

执行代码，我们可以看到`'AU'`被翻译成英文和中文的`'Australia'`。

类型被设置为“脚本”，而`of`方法翻译一个 4 个字母的 [ISO 15924 脚本代码](https://en.wikipedia.org/wiki/ISO_15924)。

`displayNamesInEnglish`是为区域设置`'en'`和类型`'script'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'script'`创建的。

执行代码，我们可以看到`'Xpeo'`被翻译成英文和中文的`'Old Persian'`。

类型被设置为“货币”，并且`of`方法翻译 3 个字母的 ISO 4217 货币代码[。](https://docs.1010data.com/1010dataReferenceManual/DataTypesAndFormats/currencyUnitCodes.html)

`displayNamesInEnglish`是为地区`'en'`和类型`'currency'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'currency'`创建的。

执行代码，我们可以看到`'EUR'`被翻译成英文和中文的`'Euro'`。

类型被设置为“日历”，而`of`方法翻译一个[地区的日历纪元](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Locale/calendar)。

`displayNamesInEnglish`是为区域设置`'en'`和类型`'calendar'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'calendar'`创建的。

执行代码，我们可以看到`'hebrew'`被翻译成英文和中文的`'Hebrew Calendar'`。

类型设置为`dateTimeField`，`of`方法翻译日期/时间项。

`displayNamesInEnglish`是为区域设置`'en'`和类型`'dateTimeField'`创建的。

`displayNamesInChinese`是为区域设置`'zh'`和类型`'dateTimeField'`创建的。

执行代码，我们可以看到`'minute'`被翻译成英文和中文的`'minute'`。

## 国际机场。日期时间格式

`Intl.DateTimeFormat`对象支持区分语言的日期和时间格式。

构造函数`Intl.DateTimeFormat()`创建一个新的`Intl.DateTimeFormat`对象。`Intl.DateTimeFormat`对象有一个实例方法`format()`，它根据地区和格式选项格式化日期。

V8 9.5 增加了一个新的选项`timeZoneName`，来格式化时区显示。可以配置为`'shortGeneric'`(第 25 行)`'longGeneric'`(第 30 行)`'` `shortOffset'`(第 35 行)，或者`'longOffset'`(第 40 行)。

# Readline Promise API

`readline`模块提供了从可读流(如`process.stdin`)中读取数据的接口，一次一行。

以下代码是`readline`模块的一个例子:

以下是该程序的交互用法:

# 结论

Node.js 17 有许多新特性和改进。在 node.js 18 发布之前都是`Current`发布。

如果您想了解其他版本的特性，请阅读以下文章:

*   [node . js 19 的 6 大特色](/6-major-features-of-node-js-19-b98e28b9670c)
*   [node . js 18 的 5 大特色](/5-major-features-of-node-js-18-5f4a164cc9fc)
*   [快速浏览 Node.js 16 特性](/a-quick-look-at-the-node-js-16-features-d616e8b2f29)
*   Node.js 15 的新功能

感谢阅读。我希望这有所帮助。有兴趣的话可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。