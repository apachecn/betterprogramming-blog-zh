# PHP 中的电子邮件语法验证和存在性验证

> 原文：<https://betterprogramming.pub/email-syntax-validation-and-existence-verification-in-php-5f7fdad768dd>

## 如何构建一个 PHP 脚本来验证电子邮件

![](img/42352dde48a5cc10c1336d8c20d295f2.png)

本·格里菲斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

让我们假设您在一段时间内从用户那里收集了几封电子邮件，但没有向他们发送验证电子邮件。现在，您希望使用这些电子邮件来联系您的用户，发送简讯，或者只是确保您收集的电子邮件有效并且存在。在这种情况下，你应该找到一种方法来检测无效和虚假的电子邮件，并要求这些用户向你提供一个有效的电子邮件。最简单的方法是向每个用户发送一封验证电子邮件。

这种后验方法的问题是，你要求你的用户主动执行一个动作，如果你在他们注册后发送了一封验证邮件，这个动作就没有必要了。此外，许多用户可能不活跃，懒惰，或怀疑他们收到的验证链接。换句话说，给所有人发一封验证邮件可能不是最好的方法。

相反，您应该考虑运行一个脚本来验证您的电子邮件，然后只联系电子邮件未通过测试的用户。因此，让我们看看如何构建一个脚本来验证电子邮件是否有效以及是否存在于 PHP 中。

# 如何在 PHP 中验证电子邮件

有几种在线服务可以让你验证一封电子邮件在语法上是否有效以及是否存在。在这里，您将学习如何用两种方法来验证和确认电子邮件。第一个不涉及 [Composer](https://getcomposer.org/) ，而第二个使用了 Composer 依赖。

注意，这些方法都不是完美的。尽管它们很少失败，但它们仍然是验证你的大部分邮件的有效方法。

我们开始吧！

## 1.不使用编写器验证电子邮件

第一种方法不需要 Composer，并且基于 SMTP 验证。点击了解更多关于这种电子邮件存在验证方法的工作原理[。](https://mailtrap.io/blog/verify-email-address-without-sending/)

具体来说，您可以使用下面 GitHub repo 中的`SMTP_validateEmail`类，根据 SMTP 方法验证电子邮件是否存在:

```
[https://github.com/semplon/php-smtp-email-validation](https://github.com/semplon/php-smtp-email-validation)
```

尽管这是一个老项目，但它仍然非常有效。因为这个类不区分语法失败和存在失败，所以您需要定义一个函数来验证电子邮件在 PHP 中的语法是否正确。您可以通过以下方式实现这一点:

上面的`validateEmail()`函数使用`[filter_var()](https://www.php.net/manual/en/function.filter-var.php)` PHP 函数来验证电子邮件地址。如果您将`FILTER_VALIDATE_EMAIL`选项过滤器传递给它，`filter_var()`将从语法角度验证存储在`$email`中的电子邮件地址，如果成功则返回过滤后的数据，如果失败则返回`false`。然后`filter_var()`返回的值被转换成[布尔](https://www.php.net/manual/en/language.types.boolean.php)，最后由`validateEmail()`函数返回。

现在，您已经拥有了在 PHP 中不使用 Composer 来验证和发送电子邮件所需的一切。您可以实现这一点，如下所示:

注意，在上面的例子中，`SMTP_validateEmail`类及其子文件夹和文件被放置在`vendor`文件夹中。

## 2.使用 Composer 验证电子邮件

在这里，你将学习如何使用 [VerifyEmail](https://github.com/masroore/verifyemail) PHP 库。如官方 GitHub 页面所述，VerifyEmail 允许您验证电子邮件地址的语法是否正确以及是否存在。

您可以使用以下 Composer 命令安装它:

```
composer require "masroore/verifyemail"
```

该库支持 4 种不同级别的验证:

*   `SyntaxCheck`:通过执行语法检查来验证电子邮件地址。
*   `DnsQuery`:通过使用电子邮件地址字符串的域部分检查负责电子邮件传送的 SMTP MX 主机是否存在来验证电子邮件地址。
*   `SmtpConnection`:通过测试与 SMTP MX 主机的连接来验证电子邮件地址。
*   `SendAttempt`:通过测试 SMTP 连接，发送`EHLO/HELO`和`MAIL FROM`命令，并在`RCPT TO`命令中提交给定的电子邮件地址字符串来验证电子邮件地址。这是最完整的方法，也是默认使用的方法。

它们中的每一个都可以用在`AddressValidationLevel`类中定义的`EmailAddressVerifier::validationLevel`属性来设置。

您可以使用`EmailAddressVerifier`验证电子邮件，如下所示:

同样，几行代码足以验证你的邮件。

# 结论

在这里，我们看了如何实现一个 PHP 脚本来验证电子邮件列表，并验证它们是否存在。这可以通过多种方式实现。在本文中，我们学习了如何使用外部库和 Composer 库来实现。在这两种情况下，几行代码就足够了。

感谢阅读！我希望这篇文章对你有所帮助。