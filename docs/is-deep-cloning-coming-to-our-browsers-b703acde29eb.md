# 深度克隆会出现在我们的浏览器中吗？

> 原文：<https://betterprogramming.pub/is-deep-cloning-coming-to-our-browsers-b703acde29eb>

## 阅读关于结构化克隆算法在主流浏览器中可能暴露的信息

![](img/33f5a1b65c7772e907ea3912924795d6.png)

菲尔·肖在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

网上有很多关于 JavaScript 深度克隆的讨论。虽然有针对浅层克隆的原生解决方案，如 destructuring 或`Object.assign()` ，但深层克隆必须使用第三方库(如 Lodash)或滥用 API(如 MessageChannel)来创建，以利用 HTML 标准提出的结构化克隆的未公开实现。

根据 Lodash 等第三方库的使用情况、关于堆栈溢出的 JavaScript 深度克隆问题的流行程度以及`JSON.parse(JSON.stringify(...)`的常见(在许多情况下也是有问题的)用法，开发人员似乎对原生解决方案有很大的需求。

# 结构化克隆

结构化克隆算法是由 HTML 标准提出的，已经在普通浏览器中实现，并且在内部使用(例如，用于在 web workers 之间传输数据或者用 IndexedDB 存储数据)。

它递归地遍历要克隆的对象，并维护一个已访问引用的映射，以避免无限循环。因此，例如，与`JSON.parse(JSON.stringify(...))`方法相比，循环结构对于该算法来说不是问题。

在 JavaScript 领域中，这意味着支持大多数类型(如 Date、RegExp、Array、Object、Map 和 Set)。但是，原型链没有考虑，还有一些事情要处理。如果您感兴趣，请查看下面参考资料中的 MDN Web 文档链接。

## 结构化克隆与深度克隆

*深度克隆*(也称为*深度复制*)是对象的副本，以便为任何引用的对象创建新的副本。这对 JavaScript 对象意味着什么？这就变得困难了。虽然有些人可能对结构化数据感到满意，但其他人可能希望复制原型链。所以*深度克隆*是一个定义相当模糊的术语。

另一方面，结构化克隆算法是一种定义明确的算法，因此它很好地定义了什么是结构化克隆。可以说这是深度克隆的具体实现。

# 浏览器中的结构化克隆？

虽然 Node.js 公开了 v8 JS 引擎的结构化克隆实现作为其序列化 API，但许多人仍在等待浏览器的公开实现。如果你是这些人中的一员，有一个好消息:HTML 规范中的一个提议已经存在。

不幸的是，在我写作的时候，似乎只有 Firefox 有兴趣实现它。然而，这个话题仍然被讨论，所以如果你渴望那个特性，不要放弃希望。

即使只是在提议阶段，看起来暴露将以全局函数的形式出现`structuredClone()`,这将使结构克隆变得简单:

如果你想要这个特性，我鼓励你公开结构化克隆算法，这样我们可能很快就会在主流浏览器的积压清单上看到它

# 参考

*   HTML 标准:可串行化对象
*   [MDN 网络文档:结构化克隆算法](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)
*   [https://github.com/whatwg/html/issues/793](https://github.com/whatwg/html/issues/793)