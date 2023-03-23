# Instagram (Android)使用的开源库

> 原文：<https://betterprogramming.pub/open-source-libraries-used-by-instagram-android-ed3d86845880>

## 对 FAANG 技术的深入研究

![](img/fdd391af5cd44c082c7c38b00fe04366.png)

卢克·范·济尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

偶尔花一个小时左右的时间来阅读关于库的内容，对于提高一个人的开发人员素质、发现有用的库，以及经历在好的应用程序中发现黄金代码的困难挑战是非常有用的。在这种情况下，我选择了 Instagram，因为它是 FAANG 科技巨头成功创建这些大型应用程序的一部分，这些应用程序不仅仅是一个工具或社交媒体平台。

在本文中，我将给出一个详细的列表，按照许可对我认为对多个项目场景有用的所有开源库进行排序。

# Apache 许可证 2.0

## **ACRA**

*   描述:根据 [its 文档](https://github.com/5l1v3r1/acra-1/blob/master/README.md):

> “ACRA 是一个库，允许 Android 应用程序自动将它们的崩溃报告发布到报告服务器。它针对 Android 应用程序开发人员，帮助他们在应用程序崩溃或出现错误行为时从应用程序中获取数据。”

*   用例:它对几乎任何应用程序都有用。它可以帮助跟踪生产错误，但是您可能希望检查成本，因为必须在库旁边实现后端来检查报告。

## **GSON**

*   描述:根据 [its 文件](https://github.com/google/gson):

> “Gson 是一个 Java 库，可用于将 Java 对象转换成它们的 JSON 表示。它还可以用来将 JSON 字符串转换成等价的 Java 对象。Gson 可以处理任意 Java 对象，包括您没有源代码的预先存在的对象。”

*   用例:将 Java 对象存储到文件系统，存储持久数据，或者通过协议将数据传输到外部设备或服务。

## **泄密者**

*   描述:根据其[官网](https://square.github.io/leakcanary/):

> “LeakCanary 对 Android 框架内部的了解使其有独特的能力缩小每个漏洞的原因，帮助开发人员大幅减少`OutOfMemoryError`崩溃。”

*   用例:在执行内存密集型工作负载时减少错误，同时提高应用程序的性能。

## **手机号码**

*   描述:根据 [its 文件](https://github.com/google/libphonenumber):

> “Google 的通用 Java、C++和 JavaScript 库，用于解析、格式化和验证国际电话号码。Java 版本针对在智能手机上运行进行了优化，从 4.0(冰激凌三明治)开始被 Android 框架使用。”

*   用例:验证与特定国家相关的电话号码格式的用户界面。

## **Math.js**

*   **描述:**据[其文献记载](https://mathjs.org/):

> Math.js 是一个用于 JavaScript 和 Node.js 的扩展数学库。它提供了一个集成的解决方案来处理不同的数据类型，如数字、大数、复数、分数、单位和矩阵

*   用例:它可以用于业务逻辑中的任何数学运算或复杂的图形操作。

## 地震的

*   描述:根据[它的文档](https://github.com/square/seismic)，它提供“Android 设备震动检测”
*   用例:当你想在不需要用户太多工作的情况下启用特定模式时，它可以用于游戏开发、删除和类似 Spotify 驱动程序模式的功能。

## **SQLite JDBC**

*   描述:根据[其文档](https://github.com/xerial/sqlite-jdbc):

> " SQLite JDBC 是一个用 Java 访问和创建数据库文件的库."

*   用例:适合大型应用程序。使用 SQL 语法远程同步和保存本地存储中有组织的数据，方便访问。

## **ZXing**

*   描述:根据 [its 文件](https://github.com/zxing/zxing):

> ZXing(“斑马线”)是一个用 Java 实现的开源、多格式的 1D/2D 条形码图像处理库

*   使用案例:游戏开发、活动通信应用、门票验证应用、第三方应用认证，以及几乎所有希望自动处理商品而不是使用传统条形码扫描仪的超市/商店。

# 麻省理工学院许可证

## asn1crypto

*   描述:根据 [its 文件](https://github.com/wbond/asn1crypto):

> 除了 ASN.1 BER/DER 解码器和 DER 串行器之外，该项目还包括一系列用于各种常见加密标准的 ASN.1 结构

*   用例:由于这是一个 Python 库，我认为 Instagram 使用的是 Java 的一个端口或某种 Python 的绑定。用例可以简单到用单向加密保护密码、安全地与服务器通信，以及用 RSA 等算法保护 E2E 加密。

## 布罗特利

*   描述:根据 [its 文件](https://github.com/google/brotli):

> Brotli 是一种通用无损压缩算法，它使用 LZ77 算法的现代变体、霍夫曼编码和二阶上下文建模的组合来压缩数据

*   用例:向 API 发送和接收数据可能是它的主要用例之一。

## GoogleTest

*   描述:根据 [its 文件](https://github.com/google/googletest):

> “这个存储库是以前独立的 GoogleTest 和 GoogleMock 项目的合并。这些是如此紧密相关，因此将它们一起维护和发布是有意义的。”

*   用例:创建关于单元测试和丰富断言的报告来验证代码。

## 钠

*   描述:根据 [its 文件](https://github.com/jedisct1/libsodium):

> "钠是一个新的，易于使用的软件库，用于加密，解密，签名，密码散列等."

*   用例:这个库在 Android 上受支持，用例与 asn1crypto 相同。

# 结论

Instagram 使用更多的库。你可以去 Instagram App →设置→关于→开源库自己查，不过考虑一下列出来的对很多应用场景有用。

我希望这篇评论是对有趣应用程序的深入研究之一，以了解大型科技公司将什么作为日常开源驱动程序。我们还可以确定一个“凡人”应用程序是否可以使用他们的一些核心库，并展示如何使用它们的实现或示例。

感谢阅读！