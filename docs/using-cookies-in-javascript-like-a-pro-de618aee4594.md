# 像专家一样使用 JavaScript 中的 Cookies

> 原文：<https://betterprogramming.pub/using-cookies-in-javascript-like-a-pro-de618aee4594>

## 关于 JavaScript 中的 cookies、本地存储和会话存储的系列文章的第 1 部分

![](img/a5137f5c77507e5637900195557cec62.png)

照片由[莫丽·西瓦拉姆](https://unsplash.com/@molliesivaram?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

互联网是一个奇妙的地方，它充满了有用的信息，可爱动物的图片，以及各种其他伟大的事情。有时，我们想与这些东西互动，也许在网站上创建一个帐户。

不幸的是，建立网络的协议，HTTP(超文本传输协议)，本质上是无状态的。

HTTP 无状态是什么意思？在 HTTP 中，每个命令都是独立执行的，不知道之前的命令是什么。那么，我们如何创建能记住用户及其行为的交互式网站呢？

# 饼干到底是什么？

*   cookie 是网站发送的一小段信息，存储在用户的计算机上。
*   用户的浏览器可以读取 Cookies 来告诉网站某种信息，比如用户是谁。
*   Cookies 只能存储字符串(我们马上会创建一个解决方法)。

使用 cookies 我们需要完成四项基本任务。

*   创建一个 cookie。
*   读一块饼干。
*   更新 cookie。
*   删除 cookie。

这四个动作的公共集合通常被称为 CRUD。

# 用 JavaScript 创建 Cookie

这篇文章中的所有代码都可以在我的 [GitHub 仓库中找到，这里是](https://github.com/wukrit/js-session-experiments/blob/master/js/main.js)。

# 用 JavaScript 读取 Cookies

# 用 JavaScript 更新 Cookie

# 在 JavaScript 中删除 Cookie

你可能已经注意到，当我们试图读取`document.cookie`时，我们会得到一个分号分隔的 cookies 字符串。我们可以通过用分号分隔字符串来挑选特定的 cookies。

这将返回一个包含所有 cookies 的数组。然后，我们可以遍历这个数组，找到我们需要的特定 cookies。

# 将对象存储为 Cookie

此外，我们可以使用 cookies 来存储更复杂的数据结构，比如一个对象。

cookie 确实有存储容量的限制，所以不要试图将整个 API 都塞进一个 cookie 中。

既然我们已经在 cookie 中存储了一个对象，我们必须把它作为一个对象取出来。我们可以用下面的方法来完成。

# 饼干的缺点

*   Cookies 所能容纳的数据量是有限的。
*   用户很容易阅读 Cookies。
*   精明的用户可以使用浏览器扩展来编辑他们的 cookies。

# 结论

Cookies 是跟踪网站信息的好方法。您可以使用这些信息来跟踪谁登录(如果没有某种加密，请不要这样做)，跟踪用户的操作，甚至限制用户可以执行某个操作的次数。

在我的下一篇文章中，我将介绍另一个工具，我们可以用它来跟踪多种状态下的信息，这就是本地存储。