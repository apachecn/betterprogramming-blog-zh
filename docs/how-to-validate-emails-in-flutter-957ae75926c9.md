# 如何在 Flutter 中验证电子邮件

> 原文：<https://betterprogramming.pub/how-to-validate-emails-in-flutter-957ae75926c9>

## 使用这个 Flutter 包使验证变得简单

![](img/93bfd3a0cfa14e3b6d5599bcdc86f79e.png)

Solen Feyissa 在 [Unsplash](https://unsplash.com/s/photos/email-valdation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片。

欢迎阅读我的第一篇关于颤振包测试的文章。每周，我都会测试另一个发布在 [pub.dev](https://pub.dev/) 下的包。

## 电子邮件验证程序

本周的软件包为您提供了一个简单易用的功能来验证电子邮件输入。作者提到这是一个简单(但“正确”)的 Dart 类，用于电子邮件验证，不使用正则表达式。我们稍后会回到为什么他称之为正确。

## 目录

> [*用途*](#fbc0) [*范围*](#8131) [*举例*](#d662) [*局限性*](#05dc)
> [*安装*](#ca6b)[*版本*](#9859)[*分数*](#8530)

## 目的

我认为这个方案的目的非常明确。例如，如果您想用电子邮件地址和密码注册一个用户，您必须验证该电子邮件地址是有效的。如果不是，你可以给你的用户一个提示，让他们再次检查他们的电子邮件地址是否有拼写错误。创建新用户时，并非所有后端系统都支持电子邮件验证。根据你的应用程序的用户体验，当用户完成电子邮件地址输入并想要开始填写密码时，帮助他们也会好得多。

大多数时候，当你需要的时候，你就开始搜索电子邮件验证。看看堆栈溢出，这个场景中投票最多的响应是一个复杂的正则表达式。

## 范围

除了`EmailValidator.validate(value)`方法之外，这个包在整个验证过程中只有私有的、不可用的函数。

在 [GitHub 库](https://github.com/fredeil/email-validator.dart)里面看，只有一个类叫做`email_validator.dart`。事实上，没有使用正则表达式来验证输入。相反，作者利用类型参数并将其与输入进行比较。通过使用这一点，他使得算法对想要理解黑盒的人来说更加清晰。

仔细看看，我明白作者为什么强调他的包是正确的:验证器不仅验证“name@email.de”，还验证包含 IPv4 或 IPv6 域的电子邮件地址(例如 name@[123.123.123.123])。

## 例子

pub.dev 上给出的例子简单地展示了如何使用来自`EmailValidator`类的`validate`方法来验证硬编码的字符串:

当使用变化的输入测试电子邮件验证时，您可以使用如下例所示的`TextField`。用户在`TextField`中所做的任何更改都会得到验证。在`TextField`下面，我放了一个简单的`Text`显示当前输入是否有效。

Luciano Jung 在 Youtube 上的视频

## 限制

通过测试可能的电子邮件地址，我发现了一个 GitHub gist 显示有效和无效的电子邮件地址:

使用这个列表，我发现电子邮件验证器的算法非常好，但是它有一个限制。当输入电子邮件地址 email@example.web 时，它第一眼看上去并没有提到它是无效的。看看顶级域名的维基百科页面。网络”不是其中之一。从技术上来说，这是一个错误的验证，但这是一个不值得的努力。要检查这一点，你还必须及时更新所有列出的顶级域名，并在验证电子邮件地址时检查它们。

## 装置

要安装这个包，您可以在命令行中使用 Dart 或 Flutter 命令，或者简单地在依赖项下的`pubspec.yaml`中添加最新版本的`email_validator`。

带省道:

```
**$** dart pub add email_validator
```

带颤振:

```
**$** flutter pub add email_validator
```

这将向您的包的`pubspec.yaml`添加一行代码(并运行一个隐式的`dart pub get`):

```
dependencies:
  email_validator: ^latest_version
```

或者，您的编辑器可能支持`dart pub get`或`flutter pub get`。查看您的编辑器的文档以了解更多信息。

**导入它**

现在，在您的 Dart 代码中，您可以使用:

```
**import** 'package:email_validator/email_validator.dart';
```

## 版本

撰写本文时的最新版本是 2.0.1。最新版本支持 Flutter 2 的零安全。查看历史记录，您可以看到发行者在这个包中加入了一些工作来保持它的最新。他还清理了代码，修复了社区提到的 bug。

## 得分

这个包的受欢迎程度说明了一切:325 个赞，来自 Flutter 的 130 个 pub 积分，以及 98%的受欢迎程度。

Furthermore, the package supportsnull safety and all current platforms (Android, iOS, Linux, macOS, web, Windows). It was first published in August 2018 and has supported null safety since March of this year. It’s under the MIT License and uses AppVeyor to validate the building process. The tests of this project test several email addresses themselves, including email addresses in other languages like Chinese, Hindi, and Greek (e.g. 伊昭傑@郵件.商務).

## 评级

总而言之，我会给这个包打 9/10 分，因为它简单易用。前面提到的一个限制和这个包中只有一个函数的开销是我只给出 9 分的原因。

## 链接

你可以在 [pub.dev](https://pub.dev/packages/email_validator) 找到这个包。

如果你想看看我写的每个包的应用程序，请前往[我的 GitHub](https://github.com/lucianojung/flutter_package_examples) 。

如果你对这个包的源代码感到好奇，可以在 GitHub 上查看一下[。](https://github.com/fredeil/email-validator.dart)

[喜欢支持这类内容的话给我买个寿司卷](http://buymeacoffee.com/lucianojung)。