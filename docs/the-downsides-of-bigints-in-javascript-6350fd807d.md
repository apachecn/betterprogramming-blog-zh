# JavaScript 中 BigInts 的缺点

> 原文：<https://betterprogramming.pub/the-downsides-of-bigints-in-javascript-6350fd807d>

## 使用 JavaScript 新的 BigInt 原始类型的一些主要区别和缺点

![](img/1cfcfb0ddb48e17892a8ebf033ae1301.png)

约翰·莫塞斯·鲍恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

如果你熟悉新的 [ES2020](https://medium.com/better-programming/javascript-es2020-features-with-simple-examples-d301dbef2c37) 特性`[BigInts](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)`，那么你已经知道它们是 JavaScript 的一个新的整数原语类型。

如果你从未听说过它们， [MDN 网络文档](https://medium.com/u/1a6d3d2733d0#cryptography)

换句话说，`BigInts`不是[区块链](https://medium.com/topic/blockchain)或[加密货币](https://medium.com/topic/cryptocurrency)项目的合适选择。

`BigInts`不适合密码学的原因是对它们的运算不是[常数时间](http://inst.eecs.berkeley.edu/~cs10/labs/cur/programming/algorithms/timing/constant-time.html?topic=berkeley_bjc%2Fareas%2Falgorithm-complexity.topic)，在 [Big-O 符号](https://www.interviewcake.com/article/javascript/big-o-notation-time-and-space-complexity)中也称为 [*O(1)*](https://en.wikipedia.org/wiki/Time_complexity#Constant_time) 。

让我们暂时解开这个谜团。这个想法是，你可能试图比较一个私人的，秘密的密钥与一个密码尝试。

如果密钥是“2343434”，尝试是“343434”，那么 JavaScript 将比较每个中的第一个数字，并立即返回`false`。与尝试输入“2343435”相比，该比较返回所需的时间要少得多，2343435 是一个具有更多匹配数字的值。

为了破解密钥，攻击者可以跟踪比较时间，并了解到“2343435”比“343434”更接近密钥

回到 Big-O 符号，用`BigInts`比较等式是*O(n)*——取决于正确匹配字符的长度(n)。

想要用 JavaScript 实现密码安全的开发者应该去看看 [WebCryptoAPI](https://www.w3.org/TR/WebCryptoAPI/) 、 [SJCL](http://bitwiseshiftleft.github.io/sjcl/) 、 [js-nacl](https://github.com/tonyg/js-nacl) 、 [ursa](https://github.com/JoshKaufman/ursa) 或者 [openpgpjs](https://github.com/openpgpjs/openpgpjs) 。

# 结论

是 JavaScript 的一个很酷、有趣的新特性。但是它们有一些显著的缺点，可能会让你三思。

你不能在你的 JavaScript 代码中混合使用`BigInts`和数字而不产生类型错误，所以你可能想要使用[类型脚本和](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-2.html) `[BigInts](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-2.html)`。

单独使用 JavaScript，你需要知道如何检查一个[数字](https://medium.com/javascript-in-plain-english/how-to-check-for-a-number-in-javascript-8d9024708153)是否真的是一个`[BigInt](https://levelup.gitconnected.com/how-to-check-if-a-number-is-a-bigint-in-javascript-6d658be6e89c)`，正如我在之前的文章中解释的[。](https://medium.com/p/6d658be6e89c)

`BigInts`的其他缺点与性能和加密安全有关。

因为`BigInt`运算是在软件而不是硬件中执行的，`BigInt`性能比使用数字的计算要慢得多。

此外，`BigInts`不适合加密，尽管你可以很容易地使`BigInt`有几千位长，因为`BigInt`值之间的比较不是在常数时间内进行的。

这些缺点使得`BigInts`不适合区块链的项目。

希望这有助于您理解 JavaScript 数字和`BigInt`原始类型之间的区别。编码快乐！

德里克·奥斯汀博士是《职业编程:如何在 6 个月内成为一名成功的 6 位数程序员 》一书的作者，该书现已在亚马逊上架。