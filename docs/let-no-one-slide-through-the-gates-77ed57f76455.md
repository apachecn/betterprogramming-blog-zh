# 不要让任何人溜出大门

> 原文：<https://betterprogramming.pub/let-no-one-slide-through-the-gates-77ed57f76455>

## 提高 Node.js 应用程序安全性的详细研究

![](img/baa08cc04dd938c35f524d5c74ba6f08.png)

何塞·丰塔诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

网络上的任何东西都容易受到攻击。没有人能在网络中制作完美的应用程序。但是我们可以尽最大努力减少应用程序中的漏洞、弱点和缺陷，从而将安全漏洞的可能性降至最低。

我们可以采取一般的抽象安全措施来提高 web 应用程序的安全性，也可以采取特定于技术的措施来确保安全性。

由于 Node.js 在世界各地的许多 web 应用程序中使用，所以我想写一些 Node.js 特有的措施，以及我们可以采取的一般安全措施。

[开放网络应用安全项目(OWASP)](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html#nodejs-security-cheat-sheet) 列出了一些我们可以考虑的措施和建议。他们将这些措施分为四个主要领域。

*   应用程序安全性
*   错误和异常观察
*   服务器安全性
*   平台安全性

# 应用程序安全性

应用程序安全性主要关注如何保护应用程序的运行时免受诸如未经授权的访问、修改等威胁。以下是我们可以用来确保应用程序安全性的一些措施。

## ***限制请求尺寸***

当我们的应用程序为用户服务时，它总是需要缓冲和解析用户发送的一些数据。

数据可以是 URL、JSON 主体或多部分数据。如果对这些请求的大小没有限制，攻击者可以向我们的应用程序发送大量数据，这可能会导致服务器耗尽内存、CPU 能力或磁盘空间。

因此，我们需要对用户发送的请求大小设置一个上限。但是 URL 数据非常小，以字节或几千字节来衡量。JSON 数据大约有几千字节，多部分数据可以达到 20-30 兆字节。

这些大小取决于应用程序用例、用途和领域。因此，我们需要为不同的请求类型手动指定不同的请求限制。对于 Express，我们可以通过以下步骤轻松做到这一点。

在 Express 中限制请求大小—摘自 [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html#set-request-size-limits)

但是这种方法是不够的，因为攻击者可以手动更改请求大小头并绕过这种验证。因此，在处理请求时，我们也需要手动检查这个验证。

## ***避免制造影响安全的竞争条件***

对于阻塞事件，Node.js 允许分配回调以最大化吞吐量。对于与 I/O 相关的任务，使用回调将有助于避免阻塞事件循环。

一个非常简单但有缺陷的认证脚本

上面显示的代码是一个使用回调读取文件的例子。

这里，在回调和第 7 行之间出现了竞争情况。比赛的获胜者将完全改变程序流的输出。因此，如果首先执行第 7 行，那么在用户通过身份验证之前，文本文件将被取消链接。这可能会导致抛出严重的错误或异常。

以下是在没有任何竞争条件的情况下执行上述任务的好方法。我们可以在一个非阻塞函数中编写所有相互依赖的操作。这样，我们可以保证重要的操作以正确的顺序执行。

解决上述问题的正确方法

## ***执行输入验证***

在几乎所有的 web 应用程序中，从用户那里获取输入是很常见的。因此，验证输入值至关重要。不进行输入验证或部分验证输入可能会导致许多不同类型的应用程序漏洞。

攻击者可以利用这些漏洞执行 SQL 注入、跨站点脚本、命令注入、本地/远程文件包含、拒绝服务、目录遍历、LDAP 注入和许多其他注入攻击。

验证输入的最佳方式是将接受的输入列入白名单。白名单验证包括准确定义什么是授权的，并阻止其他一切。

但是大多数时候，这有点难。因此，我们可以首先检查预期的输入方案，并对危险的输入进行转义。你可以在这里了解更多关于白名单、黑名单和输入验证[的信息。在 Node.js 中，有多个模块可以简化验证，比如](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html) [validator.js](https://www.npmjs.com/package/validator) 和 [mongo-express-sanitize](https://www.npmjs.com/package/mongo-express-sanitize) 。使用验证器的一个例子是:

验证电子邮件输入

预防 SQL 注入与此有些不同。攻击者可以将隐藏在用户输入中的 SQL 查询传递给程序。这些查询可以从数据库中读取我们的敏感信息，并导致我们的数据库被清除。考虑下面的 SQL 查询。

`select username, password from users where id=$id`

假设用户输入`2`或`1=1?` 作为输入的实例。

`select username, password from users where id=2 or 1=1`

这将向用户显示来自`user`表的所有数据。为了避免这些情况，我们必须使用参数化查询或准备好的语句。所有可用的数据库模块都提供了避免这些注入的功能。以下来自[节点-postgres](https://www.npmjs.com/package/pg) 模块。

在 node-postgres 模块中使用参数化查询

命令注入是另一种可能发生在 web 应用程序上的攻击。这样，攻击者就可以在我们的 web 服务器上运行操作系统命令。假设一个实例使用`child_process.exec`方法调用`gzip`命令，该命令附加一个用户提供的动态文件路径来构造`gzip`命令。

一个调用“gzip”命令的简单函数

在这里，如果攻击者将`; rm -rf /`附加到`file_path`输入中，它将脱离`gzip`命令并执行删除命令，这将彻底清除服务器。攻击者可以通过使用`;`、`&`、`&&`、`|`、`||`、`$()`、`<`、`>`、`>>`等字符来连锁多个命令。

原因是`exec`方法产生了一个新的`bin/sh`进程，并传递命令在系统外壳中执行。这相当于给攻击者一个 Bash 解释器，它拥有与我们的应用程序相同的特权。

因此，防止这种情况的一种方法是降低我们的应用程序的特权。但是避免这些的一个好方法是使用方法`execFile`或`spawn`而不是`exec`。

使用“execFile”方法防止命令注入

附加到`file_path`输入的命令在`execFile`方法的第二个类型为`array`的参数中结束。用户输入中的任何恶意命令都会被忽略，或者如果它们与目标命令不相关，就会导致语法错误。但是这些方法也不是 100%安全的，我们必须将一些用户输入列入白名单，这是我前面提到的，以保证安全性。

## ***执行输出转义***

应该执行此操作以避免另一种类型的注入攻击，即我前面提到的跨站点脚本(XSS)。

在这里，攻击者通过在论坛、留言板和评论中输入恶意脚本，在 web 应用程序中存储恶意代码。当另一个受害者访问网页时，这些脚本被传送到他们那里。恶意代码在受害者不知情的情况下在受害者的浏览器中执行。

这里，除了验证来自用户的输入，我们还必须避开应用程序显示给用户的所有 HTML 和 JavaScript 内容。我们可以使用 [escape-html](https://github.com/component/escape-html) 或 [node-esapi](https://github.com/ESAPI/node-esapi) 库在 Node.js 中执行这项任务。

## ***对应用活动进行日志记录***

日志记录不是防止攻击的方法。但是，如果我们的服务器受到攻击，我们可以使用日志来监控发生了什么，并确定攻击者使用的漏洞。Node.js 有一些模块，像 [winston](https://www.npmjs.com/package/winston) 或 [bunyan、](https://www.npmjs.com/package/bunyan)来执行应用活动日志记录。

## ***监控事件环路的繁忙交通***

当我们的服务器被流量淹没时，我们的应用程序可能会崩溃。攻击者可以利用这一点来加载我们的服务器流量，使其崩溃。这是一种*拒绝服务(DOS)* 攻击。

因此，我们必须监控繁忙的流量，如果它超过了我们的应用程序可以处理的某个阈值，我们必须用`“Server Too Busy”`来响应其余的请求。这样，我们的应用程序就不会崩溃，并在极端负载下保持响应，它将继续为尽可能多的请求提供服务。

我们不需要手动操作。toobusy-js 模块帮助我们轻松做到这一点。

toobusy-js 的用法

## ***防止暴力攻击***

暴力攻击大多发生在登录端点。攻击者可以输入数千个随机密码来猜测正确的密码。他们可能会成功，并在数千次尝试后登录到我们的服务器。

为了防止这种情况，我们可以减少某个用户尝试登录的次数(使用[限速器](https://libraries.io/npm/rate-limiter))。或者我们可以在多次尝试后锁定用户的帐户(使用类似于[猫鼬](https://www.npmjs.com/package/mongoose)的模块)。或者我们可以逐渐增加来自某个用户的每个登录请求的响应时间(使用 [express-bouncer](https://libraries.io/npm/express-bouncer) )。我们也可以使用验证码来阻止这些攻击(使用 [svg-captcha](https://www.npmjs.com/package/svg-captcha) )。

我们可以使用[hydra](https://github.com/vanhauser-thc/thc-hydra)——一个概念验证工具来测试我们的应用程序在这些场景中的行为。

## ***防止 HTTP 参数污染***

提供多个同名的 HTTP 参数可能会导致应用程序以无法预料的方式解释值。攻击者可以利用这些漏洞进行输入验证、触发应用程序错误或修改内部变量值。

有各种模块可用来防止这些类型的攻击。Express 有一个中间件， [HPP](https://www.npmjs.com/package/hpp) ，当使用它时，它会忽略`req.query`和/或`req.body`中提交的所有参数值，只选择最后提交的参数值。

# 错误处理

我们都知道错误处理在任何应用程序中都是必须的，我们都知道如何处理错误和异常。

但是当某些敏感的细节通过堆栈跟踪暴露出来时，问题就来了。堆栈跟踪本身并不被视为漏洞，但是提供调试信息作为产生错误的操作的结果被认为是一种不好的做法。我们应该总是记录错误，但不把它们展示给用户。

# 服务器安全性

顾名思义，服务器安全关注的是服务器的安全。它主要侧重于保护服务器上的数据和资源。它包含有助于防止入侵、黑客攻击和其他恶意行为的工具和技术。

## ***适当设置 cookie 标志***

不能低估安全使用 cookies 的重要性。Cookies 用于在 web 应用程序中传输会话信息。但是不小心使用这些可能最终会向外界泄露敏感信息。我们可以使用内置的 cookie 标志来防止这些问题。

*   `httpOnly`:防止客户端 JavaScript 访问 cookie。这是防止 XSS 袭击的一个很好的安全措施。
*   `Secure`:仅当通信协议为 HTTPS 时，才允许发送 cookie
*   `SameSite`:防止在跨站点请求中发送 cookies，这有助于防止跨站点请求伪造(CSRF)攻击

Cookie 实现—摘自 [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html#monitor-the-event-loop)

## ***使用合适的安全头***

我们可以使用某些 HTTP 响应头来提高应用程序的安全性。一旦它们被设置，它们就可以限制现代浏览器进入容易预防的漏洞。以下是其中的一些。

*   `Strict-Transport-Security`:强制执行与服务器的安全(HTTP over SSL/TLS)连接
*   `X-Frame-Options`:提供
*   `X-XSS-Protection`:启用最新网络浏览器内置的 XSS 过滤器
*   `X-Content-Type-Options`:防止浏览器从声明的内容类型中嗅探响应
*   `Content-Security-Policy`:防止大范围的攻击，包括 XSS 和其他跨站注射

为了实现这些，Node.js 有一个我们可以使用的模块，[头盔，](https://www.npmjs.com/package/helmet)。

头盔的使用

我们可以使用[这个](http://cyh.herokuapp.com/cyh)在线检查器来检查我们的应用程序是否有所有必要的标题。

# 平台安全性

*平台安全*是指确保整个计算平台安全的安全架构、工具和流程。

## ***保持软件包最新***

我们使用的软件包也有它们的漏洞和弱点。一旦这样的漏洞被发现，攻击者利用它来攻击我们的网站只是时间问题。但是软件包供应商会定期更新这些程序，包括错误修复和安全更新。因此，我们必须确保我们的平台保持最新。

此外，我们需要确保我们不使用任何有严重安全问题的第三方模块。我们可以使用 S [nyk](https://snyk.io/) ，一个在开源库和容器中发现并修复安全漏洞的工具。

## ***远离邪恶的正则表达式***

邪恶的正则表达式是一些正则表达式实现，可能会达到极端情况，导致它们工作非常缓慢。`([a-zA-Z]+)*`、`(a+)+`或`(a|a?)+`都是易受攻击的正则表达式，因为像`aaaaaaaaaaaaaaaaaaaaaaaa!`这样的简单输入会导致繁重的计算。

攻击者可以利用上述知识来寻找使用包含恶意正则表达式的正则表达式的应用程序，并发送一个精心制作的输入，使系统挂起。或者，如果正则表达式本身受到用户输入的影响，攻击者可以注入一个邪恶的正则表达式，使系统易受攻击。您可以在“[正则表达式拒绝服务— ReDoS](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS) ”中了解更多信息

为了防止这种情况，我们可以使用名为 [safe-regex](https://www.npmjs.com/package/safe-regex) 的 Node.js 工具。

假设我们有这样一个脚本:

safe.js 文件—取自 [npm](https://www.npmjs.com/package/safe-regex)

我们可以在上面的文件中输入各种类型的正则表达式来检查它们是否安全。

```
$ node safe.js '(x+x+)+y'
false
$ node safe.js '(beep|boop)*'
true
$ node safe.js '(a+){10}'
false
$ node safe.js '\blocation\s*:[^:\n]+\b(Oakland|San Francisco)\b'
true
```

这有时可能会产生假阳性，所以我们必须小心使用。

# 结论

因此，通过实施上述安全措施，我们可以确保我们的应用程序比大多数 web 应用程序更安全。但是我们不能保证安全，因为每天都有新的攻击 web 应用程序的方法出现。所以我们必须跟上时代，小心谨慎。

我的文章快结束了，这是一篇相当长的文章。但是我希望你能从这篇文章中学到一些有价值的东西。感谢您花时间阅读本文。希望以后能给大家带来更多的内容。干杯！

# 资源

*   [OWASP 的 Node.js 安全备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html#nodejs-security-cheat-sheet)
*   [RisingStack 的 Node.js 安全清单](https://blog.risingstack.com/node-js-security-checklist/)
*   [npm](https://www.npmjs.com/) —包含所有节点包
*   [hydra](https://github.com/vanhauser-thc/thc-hydra)——一个 POC 工具，用于测试我们的应用程序如何响应暴力攻击
*   Snyk —一种工具，用于查找和修复开源库和容器中的安全漏洞