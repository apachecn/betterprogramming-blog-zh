# 如何让 iOS 应用免受越狱设备的攻击

> 原文：<https://betterprogramming.pub/how-to-make-ios-app-secure-from-jailbroken-device-1efccc736d9b>

## 在你的应用中检测越狱的 iOS 设备

![](img/34c5f630708089ff0b424827724289d2.png)

[自由股](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

多亏了移动时代，如今我们有了一切移动应用。从理发店到大型零售商，每家企业都有应用程序，这样他们就能更接近客户。一方面，我们真正利用了这种便利。另一方面，在使用这些应用程序时，存在暴露大量机密信息的风险。这在处理支付和其他敏感信息时变得至关重要。

作为这些应用程序的开发者，我们有责任进行检查，以确保隐私和安全不受损害。这里有一个[全面的安全最佳实践列表](https://www.shashankthakur.dev/2020/09/how-to-make-ios-app-secure.html)。也就是说，本文的重点是检测 iOS 设备是否越狱。

越狱检测技术基本上分为以下几类。

# 1.文件扩展名检查

如果一个设备越狱了，系统中会存在一些文件。一个常见的文件是`Cydia`。让我们看看下面的代码，看看我们如何检查这些文件的存在:

# 2.URI 计划

使用 iOS 应用程序，您可以注册不同的方案，这些方案允许您直接从网页链接打开应用程序。如果一个设备越狱了，我们可以检查是否存在一个`cydia://`方案。下面是检查 URI 方案是否存在的代码:

# 3.沙盒检查

越狱设备改变了应用程序的沙盒行为。基本上，应用程序可以访问或影响其沙箱之外的东西。所以我们通过在我们的沙箱之外写一个文件来检查，如果我们成功了，那么设备就被越狱了。这是它在代码中的样子:

# 4.动态链接器检查

动态链接为可执行文件提供了一种利用其他库提供的代码而无需编译的方式，并且能够在可执行文件中提供该代码。这极大地帮助了可执行文件重用代码，而无需包含这些不同库的副本。另一方面，静态链接将包含可执行文件所需的所有代码。

通常，反越狱检测工具是作为动态库加载的。iOS 动态链接器 dyld 加载所有的动态库。因此，我们可以通过查看加载到当前进程中的库的名称和数量来检查反越狱检测工具的存在。如果一个反越狱检测工具正在运行，我们知道该设备是越狱。

# 结论

上面提到的所有方法都有助于我们检测越狱设备。一旦我们察觉到这一点，我们就可以决定采取我们想要的任何行动。越狱检测无疑为你的应用增加了一层安全保障，但本文中的列表绝不是足够的。最终，开发者需要遵循所有好的[安全实践来保护他们的 iOS 应用](https://www.shashankthakur.dev/2020/09/how-to-make-ios-app-secure.html)。

感谢阅读。

寻找更多的安全实践

[](https://www.shashankthakur.dev/2020/09/how-to-make-ios-app-secure.html) [## 如何让一个 iOS App 安全？

### 这是一个移动时代，现在几乎所有的事情都可以在我们的智能手机上发生。感谢数百万的应用程序…

www.shashankthakur.dev](https://www.shashankthakur.dev/2020/09/how-to-make-ios-app-secure.html)