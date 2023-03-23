# 如何找到并修复 Java 代码中的计时攻击

> 原文：<https://betterprogramming.pub/how-to-find-and-fix-timing-attacks-in-your-java-code-11291a4f7cd>

## 使用 CodeQL 防止计时攻击

![](img/9aefc1d8993ea730cecec6cbde839c12.png)

照片由 [Jaelynn Castillo](https://unsplash.com/@jaelynnalexis?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

消息认证码(MAC)或数字签名可用于认证消息并保护其完整性。

检查签名时，最好使用常数时间算法。否则，攻击者可以通过运行定时攻击来伪造任意消息的有效签名。

尽管这是一种相当复杂的攻击，但有时它可能是一种真正的威胁。让我们看看如何在 Java 应用程序中使用 CodeQL 来检测这些问题。

# 签署消息

下面是一个展示如何使用签名的示例场景。

发送方和接收方共享密钥。发送者使用密钥计算消息上的签名。接下来，发送者发送消息和签名。然后，接收者计算接收到的消息的签名，并将计算出的签名与接收到的签名进行比较。如果签名匹配，接收者知道消息是由知道密钥的发送者创建的，因此接受消息。如果签名不匹配，接收者拒绝该消息。

如果攻击者不知道密钥，他们就不能为任意消息创建签名。因此，假设重放攻击超出范围，攻击者的消息将被拒绝。

# 对签名的计时攻击

当应用程序不使用常数时间算法来验证签名时，就会出现问题。下面是一个易受攻击的代码示例:

当方法`Arrays.equals()`发现输入的一个字节不同时，它立即返回`false`。这意味着比较时间取决于数组的内容。这个小东西可能允许攻击者一个字节一个字节地伪造任意消息的有效签名。如果你想了解更多，请查看丹·博纳的视频。对了，如果你对密码学了解不多，但是有兴趣了解更多，我推荐他在 Coursera 上的[免费课程。](https://www.coursera.org/learn/crypto)

# 防止计时攻击

它通常是一行代码:只需使用`MessageDigest.isEqual()`来验证签名。将`Arrays.equals()`替换为`MessageDigest.isEqual()`即可修复上述代码:

尽管计时攻击很难运行，但为了安全起见，最好还是应用这样的单行修复程序来确保计时攻击是不可能的。看起来这样的修复不太可能引入严重的问题。也许您会注意到一些性能下降，因为`MessageDigest.isEqual()`实际上检查了所有的字节，但是影响应该很小。

除了`Arrays.equals()`之外，还有许多其他方法不使用常数时间算法。例如，`String.equals()`。这里 CodeQL 开始发挥作用。

# 用 CodeQL 检测计时攻击

如果你不知道， [CodeQL](https://securitylab.github.com/tools/codeql) 是一个代码分析引擎。它允许您为代码编写查询来检测各种问题，包括安全问题。

最近，我写了几个 [CodeQL 查询，](https://github.com/github/codeql/pull/6006)可以检测 Java 应用程序中的计时攻击。

第一个查询`PossibleTimingAttackAgainstSignature.ql`非常简单:

它查找以下数据流:

1.  数据流的来源是可以产生签名的`MAC`、`Signature`和`Cipher`类的方法。严格地说，`Cipher`用于加密/解密，但是它也可以用于实现定制的 MAC。所以这里也考虑了。此类源在`CryptoOperationSource`类及其子类中定义。
2.  接收器是不使用常数时间算法来比较输入的方法之一。它不仅包括`Array.equals()`，还包括许多其他方法。`NonConstantTimeComparisonSink`定义了这样的水槽。

以上在`NonConstantTimeCryptoComparisonConfig`中实现，用于使用 CodeQL 跟踪数据流:

严格地说，对于一个成功的计时攻击来说，仅仅使用一个非常数时间算法来验证签名是不够的。此外，攻击者必须能够向接收者发送消息和签名。查询`PossibleTimingAttackAgainstSignature.ql`没有检查这一点。但是第二个查询`TimingAttackAgainstSignature.ql`会:

不同之处在于，它在源和接收器上都调用了`includesUserInput()`谓词:

1.  `CryptoOperationSource.includesUserInput()`检查不可信数据是否用于计算具有`MAC`、`Signature`或`Cipher`类的签名。
2.  `NonConstantTimeComparisonSink.includesUserInput()`检查在比较过程中是否使用了不可信的数据。

这使得查询比第一个查询更严格，因此它应该产生更少的误报。但当然，它更有可能跳过一些真正的积极因素。

# 参考

*   [MAC](https://en.wikipedia.org/wiki/Message_authentication_code)
*   [数字签名](https://en.wikipedia.org/wiki/Digital_signature)
*   [定时攻击](https://en.wikipedia.org/wiki/Timing_attack)