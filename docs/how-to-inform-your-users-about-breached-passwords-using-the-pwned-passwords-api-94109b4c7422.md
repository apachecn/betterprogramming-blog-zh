# 如何使用 Pwned 密码 API 通知用户密码被破解

> 原文：<https://betterprogramming.pub/how-to-inform-your-users-about-breached-passwords-using-the-pwned-passwords-api-94109b4c7422>

## 帮助您的用户确保其帐户的安全性

![](img/7043cbdd0897b6095a6439ae8e7609bf.png)

谢恩·艾弗里在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

密码是我们访问数字账户最常见的方式之一。拥有安全的密码是至关重要的，也是我们需要注意的最重要的方面之一。作为开发人员，我们还需要确保为我们的服务提供高水平的安全性。

2017 年， [NIST(国家标准与技术研究所)](https://en.wikipedia.org/wiki/National_Institute_of_Standards_and_Technology)建议检查用户密码，防止现有的公共数据泄露。幸运的是，有一个 API 可以让你非常容易地做到这一点。

Pwned Passwords API 是一种服务，您可以使用它来检查密码是否已经作为过去发生的大量数据泄露的一部分而暴露。这些数据包含了之前使用过的超过 5 亿个密码。

您可以使用下面的 gem 作为包装器将它安装到您的 Ruby 应用程序中:`gem 'pwned'`。

安装后，您可以创建一个新的`Pwned::Password`对象，然后检查它是否被破坏:

您还可以检查密码在数据集中出现的次数:

由于您可能会将此服务作为注册过程的一部分，因此您也应该考虑到此服务有时可能无法正常工作的事实。

您还可以使用以下方法使这个 API 调用变得更加容易:

大多数情况下，您只关心密码之前是否被 pwn 过。您可以使用简化的访问器来检查密码是否已被密码化或密码化的次数:

您还可以验证您的模型:

您甚至可以设置一个阈值，您认为应该使用该阈值来警告用户密码已被破坏。例如，您可能认为只出现两次的密码没有问题:

如果你使用`Devise` 进行身份验证，还有另外一个[宝石](https://github.com/michaelbanfield/devise-pwned_password)专门为此准备。你可以通过访问 Github 页面来了解更多关于这个 gem 及其实现的信息。