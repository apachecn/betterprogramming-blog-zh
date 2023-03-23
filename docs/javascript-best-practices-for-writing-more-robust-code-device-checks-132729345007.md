# 编写更健壮代码的 JavaScript 最佳实践

> 原文：<https://betterprogramming.pub/javascript-best-practices-for-writing-more-robust-code-device-checks-132729345007>

## 设备检查、网络连接状态和功能可用性

![](img/d18fbe7fc7ed33dfc82409f487212775.png)

由 [David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

JavaScript 是一种容易学习的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有用例并编写健壮的 JavaScript 代码。

在这篇文章中，我们将看看如何检查设备类型和状态，以确保我们的应用程序降级。

# 设备类型检查

我们应该检查设备的类型，看看应该启用哪些功能。这很重要，因为网络应用不再只在台式机和笔记本电脑上运行。

现在我们不得不担心各种操作系统的移动设备。

我们可以从浏览器的用户代理检查设备类型。全局`navigator`对象具有包含用户代理的`userAgent`属性，该属性将操作系统信息作为用户代理字符串的一部分。

例如，我们可以如下使用它:

```
const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)
```

在上面的代码中，我们检查了移动操作系统的用户代理字符串中所有可能的子字符串。我们最关心的是 Android、iPad 和 iPhone，但是测试其他的并不难。

很容易欺骗用户代理字符串，使我们的浏览器伪装成其他东西。然而，大多数人不会这样做，也没有太多的理由让其他人这样做，所以检查设备是否是移动设备并采取相应行动仍然是一个好方法。

# 检查网络连接状态

像检查用户代理一样，检查网络连接状态也很重要，因为很多设备随时都可能离线。

移动设备在快速和慢速网络之间移动，或者移动到没有手机信号接收的地方。因此，我们必须检查这一点，以便当设备离线时，我们的应用程序能够优雅地失败。

现在大多数浏览器都支持网络信息 API，所以我们可以很容易地用它来检查网络状态。

我们可以按如下方式检查网络连接类型:

在上面的代码中，我们只是用`connection`、`mozConnection`或`webkitConnection`属性检索连接类型，以获得初始连接对象。

然后我们使用来自`connection`对象的`effectType`属性来获取有效的网络类型。

接下来，我们向`connection`对象添加一个`change`事件监听器来监视连接类型的变化。

正如我们所看到的，我们不需要做太多就可以观察到互联网连接类型的变化。所以，我们绝对应该这么做。

如果我们从 Chrome 中的 network 选项卡更改网络连接类型，以模拟网络连接类型的更改或在现实生活中更改网络连接类型，我们应该会看到如下控制台日志输出:

```
4g to 2g
2g to 4g
4g to 4g
```

# 检查功能可用性

默认情况下，不同的浏览器可能有不同的功能。在实现任何功能之前，我们应该检查浏览器是否支持我们想要的功能。

在使用可能不支持的库之前，我们可以使用[来检查浏览器是否支持某个功能。](https://caniuse.com/)

还有 [Modernizr 库](https://github.com/Modernizr/Modernizr)来检测给定浏览器中可用的特性。

我们可以通过添加一个脚本标签来安装 Modernizr:

```
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/modernizr/2.8.3/modernizr.min.js"></script>
```

它附带了一些类，我们可以使用这些类根据某些东西是否可用来打开或关闭 HTML 元素。

例如，它附带了`cssgradients`和`no-cssgradients`类，让我们分别为 CSS 渐变可用和不可用的情况添加不同的样式。

安装 Modernizr 后，我们可以在 CSS 代码中执行以下操作:

在上面的代码中，我们为 CSS 渐变功能不可用的情况提供了一个后备图像，就像在`.no-cssgradients header`选择器中的情况一样。

在`.cssgradients header`选择器中我们有正常的 CSS 渐变。

然后，我们可以添加自己的测试功能，比如在添加上面的脚本标记后检查 jQuery 是否可用:

```
window.Modernizr.addTest("hasJquery", "jQuery" in window);
```

# 结论

我们应该使用用户代理来检查设备类型。尽管它很容易被欺骗，但大多数人不会这样做，因为它没有提供多少好处。

此外，我们应该使用许多现代浏览器支持的网络信息 API 来检查设备的连接状态。

最后，我们应该在使用 Modernizr 之前检查是否有特定的库或特性可用，以及我是否可以使用它。