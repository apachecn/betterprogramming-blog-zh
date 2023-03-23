# 您应该遵循的密码验证模式

> 原文：<https://betterprogramming.pub/patterns-for-password-authentication-you-should-follow-d83a95b8486a>

## 和一个要避免的常见错误

![](img/93f5f0c48983fb5fb245f1c18e9f1c2d.png)

Divya Agrawal 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 认证时

为了在现实世界中表明自己的身份，我们通常会出示身份证、护照，或者——当与朋友和家人在一起时——依靠其他认识我们的人。然而，数字世界的工作方式——去中心化、分布式和数字化——我们需要提供其他信息来识别自己。

在过去的 20 年里，我们已经习惯了使用用户名和密码登录在线服务。虽然推荐的密码规则有所改变，但安全和敏感信息的存储和访问模式没有改变。

我们仍然需要以一种简单、合法用户直观、攻击者难以利用的方式来创建、存储和检索用户信息。

# 关于密码创建和密码复杂性

使用密码的挑战之一是我们不能强迫用户使用唯一的密码。虽然这被认为是一种不好的做法，而且由于密码管理器的兴起，这种做法可能会变得不那么普遍，但用户仍有可能在各种在线服务中重复使用密码。

因此，通过保护我们用户的密码，我们不仅保护了我们的服务，还潜在地保护了我们客户使用的其他服务。

> “随着计算变得越来越普遍，用户在多个系统上使用同一个密码变得越来越普遍，因为记住大量密码很难。”—[Star Lab 软件](https://www.starlab.io/blog/why-enforced-password-complexity-is-worse-for-security-and-what-to-do-about-it)为什么强制密码复杂性对安全性更不利(以及如何应对)

过去，常见的做法是通过对用户施加密码约束来加强密码强度。这些规则通常会强制用户使用

*   小写字符
*   大写字符
*   至少一位数字
*   至少一个特殊字符

> “从理论上讲，密码复杂性规则的主要好处是，它们强制使用更难破解的唯一密码。实施的要求越多，字母、数字和字符的可能组合数量就越多。”—[Enzoic 制定的密码复杂性规则的优点和缺点](https://www.enzoic.com/the-benefits-and-drawbacks-of-password-complexity-rules/)

尽管这类密码规则不允许使用像`password`这样的简单密码，但它们现在被认为是不好的做法。这些规则可能有良好的意图，但如果没有正确实施，它们也会严重影响安全性。

基于[的一篇文章](https://www.starlab.io/blog/why-enforced-password-complexity-is-worse-for-security-and-what-to-do-about-it)，让我们做一些简单的数学计算，展示密码复杂性规则在可枚举性方面对安全性的影响:

典型的移动电话 PIN 码有四个数字。这就是 10⁴，或者说总共有一万个可能的图钉。然而，如果我们将允许的位数从 1 限制到 5，这将减少 5⁴的可能 pin 数或 625。那只是第一个结果的十六分之一。

更实际的情况是，假设我们想要一个长度为 4 的密码，包含任意数量的字母和数字。将有(26 + 26 + 10)⁴大写和小写字符加上数字的可能性。总数将近 1500 万:

(26 + 26 + 10)⁴ = 14,776,336

一旦我们添加了一个额外的约束条件:密码必须包含至少一个数字，我们就可以大大减少可能性的数量。

因此，在我们的四个可能的字符中，一个必须是数字，而其余三个字符可以是数字或字母。这使得所有只包含字母的密码都被排除在外。

然后，我们可以减去只包含大写或小写字符的密码的数量。

(26 + 26 + 10)⁴ — (26+26)⁴= 7,464,720

这又是一个显著的减少，大约 50%的可能性是由一个应该增加密码安全性的规则引起的。

当然，这只是一个简单的例子。然而，虽然这些规则禁止所有密码中最简单的密码，但它们也限制了密码的总数，使它们更容易被猜到。

在猜测密码方面，下图清楚地表明，破解超过 11 个字符的密码仍然需要相当长的时间。然而，黑客可以在相对不重要的几毫秒、几小时或几天内破解少于十个字符的密码。

破解密码的时间

![](img/985f22f1f6c2fd70dcb8d70957a95251.png)

图片来自[估算密码破解次数(betterbuys.com)](https://www.betterbuys.com/estimating-password-cracking-times/)

总而言之，密码复杂性规则并不是密码安全的灵丹妙药。就连 NIST 现在也建议不要制定严格的密码规则来防止密码被重复使用和被记下来。

> “高度复杂的记忆秘密带来了新的潜在弱点:它们不太可能被记住，更有可能以不安全的方式被写下来或以电子方式存储。虽然这些做法不一定容易受到攻击，但从统计上看，一些记录此类秘密的方法会受到攻击。这是一个额外的动机，不需要太长或复杂的记忆秘密。”— [NIST 特刊 800–63B](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

对于 web 应用程序，像[check-password-strength——NPM(npmjs.com)](https://www.npmjs.com/package/check-password-strength)或[https://github.com/fega/secure-password-validator](https://github.com/fega/secure-password-validator)这样的库允许验证密码，不是基于严格的规则，而是基于相对于内容本身的复杂性。

# 关于密码存储

对任何公司来说，最糟糕的情况是攻击者能够访问他们的数据库和用户密码。这本身就已经很糟糕了，如果我们考虑用户跨服务重用密码的可能性，情况会变得更糟。请记住，其他用户的帐户也可能受到威胁，这增加了另一个严重级别。

因此，在任何情况下，我们都需要限制攻击者获取用户实际密码的可能性。此外，除了我们的用户自己，没有人需要访问他们的密码，根本没有必要存储它们。

> “即使应用程序或数据库遭到破坏，也要以防止攻击者获取密码的方式存储密码，这一点非常重要。”—
> [密码存储— OWASP 备忘单系列](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#introduction)

然而，我们需要存储一些信息来验证我们的用户。我们必须转换这些信息，使攻击者无法利用它们发动更复杂、更有针对性的攻击。

因此，我们需要一种方法从用户的密码中可靠地导出一个安全值，这样攻击者就无法计算初始值。这样做，我们可以用一些秘密信息来验证用户，而不用在数据库中存储确切的值。

哈希函数提供了这种级别的安全性，因此是[推荐的密码存储方式](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#introduction)。

> " H 灰化是单向函数(即，不可能“解密”散列并获得原始明文值)。哈希适用于密码验证。即使攻击者获得了哈希密码，他们也无法将其输入到应用程序的密码字段中，并作为受害者登录。”— [密码存储— OWASP 备忘单系列](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#introduction)

[说到哈希，我们大多数人都会马上想到沙家的算法。](https://auth0.com/blog/hashing-passwords-one-way-road-to-security/)这是因为它们非常受欢迎，并且被设计成散列值的计算很快。然而，在密码安全和验证的上下文中，我们不一定需要最快速的散列函数，而是需要安全的散列函数。

在这种情况下，安全意味着攻击者需要更多的时间来暴力破解实际密码，以减缓其攻击速度并降低攻击风险。

对于密码存储， [OWASP](https://developer.okta.com/blog/2019/07/29/hashing-techniques-for-password-storage) 推荐两种算法:

*   [Argon2id](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#argon2id) ，首选
*   [Bcrypt](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#bcrypt) ，作为后备

这两种算法都允许调整所谓的工作因子，它粗略地定义了哈希迭代的次数，因此也定义了计算哈希所需的时间。

> “您还可以设置[..]这是一个工作因素，它使得暴力破解变得更加困难。随着计算机越来越快，计算资源变得越来越便宜，您可以简单地增加[..]工作因素增加资源消耗。”
> [密码存储的哈希技术| Okta 开发者](https://developer.okta.com/blog/2019/07/29/hashing-techniques-for-password-storage)

在应用程序中集成这两种算法比以往任何时候都容易。下面是一个使用[bcrypt—NPM(npmjs.com)](https://www.npmjs.com/package/bcrypt)的 NodeJS 示例。

```
const bcrypt = require('bcrypt');
const saltRounds = 10;
const myPlaintextPassword = 's0/\/\P4$$w0rD';bcrypt.hash(myPlaintextPassword, saltRounds, function(err, hash) {
    // Store hash in your password DB.
});
```

还有一个 Java 使用 [GitHub 的例子——Spring-projects/Spring-Security:Spring Security](https://github.com/spring-projects/spring-security)。

```
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();  String result = encoder.encode("password");
```

# 在登录和其他读取请求时

攻击者可能出于两个原因使用我们的登录掩码:

*   查找注册用户/电子邮件地址
*   窃取用户的密码以访问他们的数据

当然，我们需要通过防止攻击者试图猜测他们的电子邮件和密码来保护我们的用户。不幸的是，猜测他们中的任何一个都不是不可能完成的任务；只是时间问题。

但是，我们可以通过在登录失败时不返回详细信息来增加猜测这两种信息的时间。例如，在下面的屏幕截图中，您可以看到 npmjs 在密码无效的情况下返回一个通用错误消息。

因此，在攻击者能够猜出有效的用户名和相应的密码之前，他们会看到一条普通的消息，该消息不提供关于实际原因的详细信息。

![](img/ceee95a09cf28af5da0461aa43b9cf90.png)

在 [npm |登录(npmjs.com)](https://www.npmjs.com/login)上发现无效的用户名或密码消息

这也适用于允许创建新帐户的表单。如果电子邮件地址或用户名已经被使用，我们不得返回该信息。在下面的截图中，我们可以看到【npmjs.com】[NPM | Sign Up(T3)【在撰写本文时】没有这样做，因此将允许攻击者枚举有效的电子邮件地址(Bug 已经通过 BugBounty 程序报告)。](https://www.npmjs.com/signup)

![](img/bd96bc25b4b98ab9c0df87edcc0836ab.png)

尽管如此，即使是一般的错误消息，攻击者也会继续寻找用户名和密码的完美组合。因此，一种常见的做法是记录每个帐户的登录尝试，并在几次尝试失败后锁定帐户—在企业环境中通常是三次。

帐户被锁定后，即使正确的用户名和密码组合也不应该再允许访问。为了可用性和透明度，应该通过电子邮件立即通知相应的用户，让他们知道该帐户受到攻击，现在已被锁定，拒绝所有访问。

有趣的是，攻击者甚至可以利用锁定功能来强制拒绝用户访问他们的帐户。

> “在设计帐户锁定系统时，必须注意防止它被用来通过锁定其他用户的帐户来导致拒绝服务。实现这一点的一种方法是允许使用忘记密码功能登录，即使帐户被锁定。”— [认证— OWASP 小抄系列](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html#protect-against-automated-attacks)

通常情况下，重置密码过程围绕一个需要电子邮件或用户名的表单展开，并允许用户收到一个神奇的链接来重置他们的密码。根据设计，这些请求确实来自匿名用户，因此我们无法验证它是否是合法的请求。

当然，密码重置功能的安全性始终取决于用户的后备邮箱。如果密码重置链接发送到的邮箱遭到破坏，我们几乎无法阻止攻击者重置密码并访问该帐户。

尽管如此，我们还是有办法[减轻这些电子邮件带来的风险](https://www.authgear.com/post/authentication-security-password-reset-best-practices-and-more):

*   例如，将重置链路的寿命减少到一小时。
*   确保重置链接没有可识别的信息，以允许攻击者猜测其他用户的重置链接。
*   使请求密码请求的用户的所有活动会话无效。
*   确保用户只能使用重置链接一次。

# 从全局来看

到目前为止，我们只关注单个表单、流程和用户。然而，攻击者可能对访问特定帐户而不是任何帐户感兴趣。例如，在我们因为太多失败的登录尝试而锁定一个帐户后，攻击者可以继续使用另一个帐户等等。

为了防止这种跨用户级别的[帐户枚举和猜谜游戏](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account)，我们需要使用不同的标准来统计无效请求的数量。一个明显的方法是用户的 IP 地址。然而，它没有听起来那么具体，因为攻击者可以很容易地使用不同的代理服务器进行攻击。

> “利用广泛可用的开放代理列表，攻击者可以轻松规避任何 IP 阻止机制。因为大多数网站不会在一次密码失败后阻止，所以攻击者可以对每个代理使用两到三次尝试。拥有 1，000 个代理列表的攻击者可以尝试 2，000 或 3，000 个密码而不会被阻止。”— [拦截暴力攻击控制| OWASP 基金会](https://owasp.org/www-community/controls/Blocking_Brute_Force_Attacks)

尽管如此，由于源 IP 地址是识别请求来源的唯一方法，这是阻止不太复杂的攻击的一种方式。

更专门和自动化的攻击可能依赖于特定和众所周知的应用程序流，并期望该流是静态的。仅仅是错误信息中的微小变化，要求两次密码，或者要求我们通过电子邮件发送的简单字符序列，就足以阻止黑客。

> “一个更好的解决方案可能是改变行为，足以最终阻止除了最专注的黑客之外的所有人。— [阻止暴力攻击控制| OWASP 基金会](https://owasp.org/www-community/controls/Blocking_Brute_Force_Attacks)

# 摘要

最安全的实现可能是我们不必独立完成的实现。像 [Auth0](https://auth0.com/) 和 [Authgear](https://www.authgear.com/) 这样的服务提供认证即服务，当然是要付费的。此外，像谷歌、脸书或苹果提供的社交登录允许将用户身份委托给第三方。

根据您的目标、要求或资金和法律限制，使用第三方服务可能是可取的，也可能是不可取的。如果您想要或者需要自己存储身份验证数据，那么这个故事中提到的技巧应该会让您知道应该关注哪种事情。

认证是一个广泛而复杂的话题，我们只能在这里分享一些建议。您可以在我们用作参考资料的以下页面中找到更多信息:

*   [为什么强制密码复杂性对安全性不利(以及如何应对)— Star Lab 软件](https://www.starlab.io/blog/why-enforced-password-complexity-is-worse-for-security-and-what-to-do-about-it)
*   [OWASP 认证备忘单](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
*   [身份验证安全性:密码重置最佳实践等— Authgear](https://www.authgear.com/post/authentication-security-password-reset-best-practices-and-more)
*   [密码存储— OWASP 备忘单系列](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html#introduction)
*   [拦截暴力攻击控制| OWASP 基金会](https://owasp.org/www-community/controls/Blocking_Brute_Force_Attacks)
*   [WSTG —最新| OWASP 基金会](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account)
*   [密码存储的哈希技术| Okta 开发者](https://developer.okta.com/blog/2019/07/29/hashing-techniques-for-password-storage)
*   [NIST 特刊 800–63B](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
*   【如何散列密码:增强安全性的单行道(auth0.com)

感谢阅读。如果你有任何反馈或进一步的想法，你可以通过 Twitter [@stfsy 联系我。](https://twitter.com/stfsy)