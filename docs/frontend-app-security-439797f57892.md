# 前端应用的 13 个安全提示

> 原文：<https://betterprogramming.pub/frontend-app-security-439797f57892>

## 锁定你的应用程序，使其不容易受到不良行为者的影响

![](img/c1c68f8a4c7bfc2a8a94272169525495.png)

由[菲利普·卡森伯格](https://unsplash.com/@fantasyflip?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

无论你是 React.js、Angular、Vue.js，还是单纯的前端开发人员，你的代码都可能成为黑客的邀约之门。

作为前端开发人员，我们最关心的是性能、SEO 和 UI/UX——安全性经常被忽略。

您可能会惊讶地发现，大型框架会让您面临跨站点脚本(XSS)攻击。React 中有危险的操作名，如`dangerouslySetInnerHTML`或 Angular 中的`bypassSecurityTrust`API。

我们应该记住，在安全性方面，前端现在与后端或开发人员分担同等的责任。前端可能会发生数千种恶意攻击。

让我们来了解一下最常见的攻击——它们将涵盖这些攻击类型中的很大一部分。

![](img/518ddc1a856a46c1811085f31c1f7837.png)

照片由 [vipul uthaiah](https://unsplash.com/@vipul_uthaiah?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 1.**无限制文件上传**

这是一种攻击，其中恶意文件被上传到服务器，然后被执行，以攻击系统。攻击可能包括:文件系统或数据库过载、完全系统接管、客户端攻击、向后端系统转发攻击或简单的破坏。

## 2.**点击顶升**

这是一种攻击，用户被诱骗点击不属于该网站的网页或元素。这种攻击可能会导致用户无意中提供凭据或敏感信息、下载恶意软件、访问恶意网页、在线购买产品或转账。

## 3. **XSS 遇袭**

这是一种攻击，恶意脚本以浏览器端脚本的形式注入网页。网站上的缺陷使得这些攻击得以成功并广泛传播。

## 4. **SQL 注入**

这是一种攻击，其中 SQL 语句中的恶意代码通过输入字段被注入以破坏您的数据库。

## **5。拒绝服务攻击** ( **拒绝服务攻击**)

这是一种攻击，通过用流量轰炸您的服务器，使目标用户无法使用服务器或其资源。

## 6.**中间人攻击或会话劫持**

这是一种攻击，在这种攻击中，客户端和服务器之间的通信被拦截，以窃取密码、帐号或任何个人信息。

攻击者总是试图在前端找到一些漏洞来到达服务器并完成他的工作。在本文中，我们将看到一些在编写前端代码时应该牢记的最佳实践。

# 1.严格的用户输入(第一个攻击点)

用户输入本质上应该是严格的，以避免类似 SQL 注入、点击劫持等漏洞。因此，在将用户输入发送到后端之前对其进行验证或净化是非常重要的。

可以通过删除或替换上下文危险的字符来净化数据，比如使用白名单和对输入数据进行转义。

然而，我意识到净化和编码对于所有现有的可能性来说都不是一件容易的事情，所以我们可以使用下面的开源库:

*   `[DOMPurify](https://www.npmjs.com/package/dompurify)`。这是使用起来最简单的，并且有一种方法来净化用户的输入。它有一个定制规则的选项，并且支持 HTML5、SVG 和 MathML。
*   `[secure-filters](https://www.npmjs.com/package/secure-filters)`。Salesforce 库，提供清理 HTML、JavaScript、内联 CSS 样式和其他上下文的方法。如果您想在其他地方使用用户输入，例如生成 CSS 或 JavaScript，这将非常有用。

在文件上传的情况下，始终检查文件类型并使用文件过滤功能，只允许某些文件类型上传。详见[本](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)。

# 2.小心隐藏在浏览器内存中的字段或数据

如果我们添加输入`type="hidden"`来隐藏页面中的敏感数据，或者在浏览器中添加`localStorage`、`sessionStorage`、`cookies`，并认为这是安全的，我们需要再想想。

攻击者可以轻松访问添加到浏览器中的所有内容。攻击者可以打开开发工具并更改所有内存中的变量。如果您根据`localStorage`、`sessionStorage`和`cookies`的值隐藏了验证页面会怎么样？

浏览器中有像 [ZapProxy](https://www.zaproxy.org/) 这样的工具，甚至检查工具，如果攻击者找到注入脚本的方法，就可以将这些值暴露给他们，然后他们可以使用它们进行进一步的攻击。

因此避免使用`type="hidden"`并且尽可能避免在浏览器内存存储中存储密钥、认证令牌等。

# 3.使用强大的内容安全策略(CSP)

永远不要相信服务器发送的所有内容，永远要定义一个强内容安全策略 HTTP 头，它只允许某些可信内容在浏览器上执行或提供更多资源。

有一个白名单是一个好习惯，白名单是一个允许来源的列表。现在，即使攻击者注入脚本，脚本也不会与白名单匹配，不会被执行。

例如:

```
content-security-policy: script-src ‘self’ [https://](https://apis.google.com/)apis.xyz.com
```

在这里，应用程序只信任来自`apis.xyz.com`和我们自己(`self`)的脚本。对于其余的源，控制台中会抛出一个错误。

注意:强大的内容安全策略不能解决内联脚本执行的问题，因此 XSS 攻击仍然有效。

您可以在 [MDN 网站](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)上阅读 CSP 指令的完整列表。

# 4.启用 XSS 保护模式

如果攻击者以某种方式从用户输入中注入恶意代码，我们可以通过提供`"X-XSS-Protection": "1; mode=block"`头来指示浏览器阻止响应。

大多数现代浏览器都默认启用了 XSS 保护模式，但是仍然推荐包含`X-XSS-Protection`标题。这有助于确保不支持 CSP 头的旧浏览器具有更好的安全性。

# 5.避免典型的 XSS 错误

XSS 攻击通常追溯到 DOM API 的`innerHTML.`，例如:

```
document.querySelector('.tagline').innerHTML = nameFromQueryString
```

任何攻击者都可以用上面的代码注入恶意代码。

考虑使用`textContent`而不是`innerHTML`来完全避免生成 HTML 输出。如果不生成 HTML，就没有办法插入 JavaScript——你可以看到内容，但什么也不会发生。

密切关注一个新的[可信类型规范](https://developers.google.com/web/updates/2019/02/trusted-types)，它旨在防止所有基于 DOM 的跨站点脚本攻击。

在 react.js 的情况下，`dangerouslySetInnerHTML`是明确的和警告性的，可以产生与`innerHTML`类似的影响。

*注意:不要基于用户输入设置* `*innerHTML*` *值，尽可能使用 textContent 代替* `*innerHTML*` *。*

此外，HTTP 响应头`Content-Type`和`X-Content-Type-Options`应该按照它们的预期行为正确设置。例如，JSON 数据不应该编码为 text/HTML，以防止意外执行。

# 6.禁用 iframe 嵌入

禁用 iframe 可以使我们免受**点击劫持攻击。**我们应该总是在请求中使用`"X-Frame-Options": "DENY"`头，禁止在框架中呈现网站。

此外，我们可以使用`frame-ancestors` CSP 指令，它提供了对哪些父页面可以嵌入 iframe 的更多控制。

# 7.保持错误的一般性

类似“您的密码不正确”这样的错误可能对用户有帮助，但对攻击者也有帮助。他们可能会从这些错误中找出有助于他们计划下一步行动的信息。

在处理账户、邮件和 PII 时，我们应该尽量使用像“不正确的登录信息”这样的模糊错误

# 8.使用验证码

在面向公众的端点使用验证码(登录、注册、联系)。验证码是一种计算机程序或系统，旨在区分人类和机器人，并可以帮助阻止 DoS(拒绝服务)攻击。

# 9.始终设置`Referrer-Policy`

每当我们使用锚标签或导航到网站之外的链接时，请确保您使用标题策略`"Referrer-Policy": "no-referrer"`，或者在锚标签的情况下，设置`rel = noopener or noreferrer`。

当我们不设置这些头和 rel 时，目标网站可以获得像会话令牌和数据库 id 这样的数据。

# 10.使用有限的浏览器功能和 API

在 CSP 中，有限的域可以连接到网站，同样的原理也可以应用于浏览器功能& APIs。我们可以添加一个`Feature-Policy`头来拒绝对某些特性和 API 的访问。[阅读更多](https://www.smashingmagazine.com/2018/12/feature-policy/)。

*提示:将* `*none*` *设置为所有不使用的功能。*

# 11.定期审核依赖关系

定期运行`npm audit`以获取易受攻击的软件包列表，并升级它们以避免安全问题。

GitHub 现在标记易受攻击的依赖项。我们也可以使用 [Snyk](https://snyk.io/) ，它会自动检查你的源代码，并打开 pull 请求来提升版本。

# 12.划分你的应用程序

在后端，我们有一个微服务架构，其中单片应用程序被分成更小的独立组件，每个组件单独运行。

同样的原理也适用于前端。例如，一个应用程序可以分为公共部分、认证部分和管理部分，每个部分都托管在单独的子域上，如`[https://public.example.com](https://public.example.com/)`、`[https://users.example.com](https://users.example.com/)`和`[https://admin.example.com](https://admin.example.com/)`、T6。

这将确保更少的客户端漏洞。

*注意:适当的划分还可以防止应用程序公共部分中的 XSS 漏洞，防止它自动泄露用户信息。*

# 13.避免第三方服务

一行代码像谷歌分析、谷歌标签管理器、对讲机、Mixpanel 这样的第三方服务会使你的网络应用程序易受攻击。想象一下这些第三方服务受到威胁的情况。

制定强有力的 CSP 政策非常重要。大多数第三方服务都有一个定义好的 CSP 指令，所以一定要添加它们。

另外，在添加脚本标签时，一定要尽可能包含`integrity`属性。[子资源完整性](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)功能可以验证脚本的加密哈希，确保它没有被篡改。

```
<script src= "https://example.com/example-framework.js" integrity= "sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/ux..." crossorigin= "anonymous" ></script>
```

# 仔细考虑自动填充字段

存储在浏览器自动填充中的个人身份信息对用户和攻击者都很方便。

攻击者添加第三方脚本来利用浏览器的内置自动填充功能提取电子邮件地址，以建立跟踪标识符。他们可以利用这些来建立用户浏览历史档案，然后卖给坏人。点击阅读更多关于此[的信息。](https://freedom-to-tinker.com/2017/12/27/no-boundaries-for-user-identities-web-trackers-exploit-browser-login-managers/)

我们中的许多人甚至不知道他们浏览器的自动填充存储了什么信息。

*提示:禁用敏感数据的自动填充表单。*

谢谢你。