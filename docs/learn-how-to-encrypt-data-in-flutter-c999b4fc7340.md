# 了解如何在 Flutter 中散列数据

> 原文：<https://betterprogramming.pub/learn-how-to-encrypt-data-in-flutter-c999b4fc7340>

## 在 Flutter 中使用加密算法来散列数据

![](img/0ff55b69fe9369ee4398a591cf3f0951.png)

由 [Alexander Sinn](https://unsplash.com/@swimstaralex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在本教程中，我们将致力于让我们的移动应用更加安全可靠。例如，我们可以通过加密敏感的用户数据来实现这一点。我将向您展示这是如何与 Flutter 一起工作的。

我们将仔细研究加密散列算法，看看如何在 Flutter 中使用算法`SHA-1`、`SHA-224`、`SHA-256`、`SHA-384`、`SHA-512`、`SHA-512/224`、`SHA-512/256`、`MD5`、`HMAC`(即 HMAC-MD5、HMAC-SHA1、HMAC-SHA256)来散列数据。

为此，我们将创建一个应用程序，它接收文本，如果需要，还接收密码，并使用前面提到的不同算法将其转换为哈希值。

此外，我将向您展示如何使用`TextEditingController`在运行时更改哈希值，并且只需单击一下就可以复制哈希值本身。

下面是这款应用的运行情况:

Luciano Jung 在 Youtube 上的视频

# 介绍

为了使用一些常见的散列函数加密数据，我们将实现`[crypto](https://pub.dev/packages/crypto)`包，它包含空安全。这支持 Android 和 iOS 应用程序以及 web 和桌面应用程序。

加密产生的散列结果并不意味着被再次解密——或者根本不被解密。它们用于散列重要数据，如密码，以便以后可以检查，但不是以纯文本形式存储。当通过比较两个数据集的哈希值来比较这两个数据集时，也可以应用它们。

在下一节中，我们将通过一步一步的指导来实现上面显示的结果。

# 准备

先说代码准备。要实现下面的项目，你需要将`crypto`包集成到你的 Flutter 代码库中。

在命令行中运行以下命令:

```
**$** flutter pub add crypto
```

这将向您的包的`pubspec.yaml`添加一行(并运行一个隐式的`flutter pub get`):

```
dependencies:
  crypto: ^latest_version
```

现在，在您的 Dart 代码中，您可以使用以下 import 语句来使用该包:

```
import 'package:crypto/crypto.dart';
```

# 履行

让我们从散列数据集的基础知识开始。我们需要一个数据集，比如一个字符串`“some text”`。我们将使用 Dart 内部库`dart:convert`将其转换成一个`utf8`字符串。然后我们可以使用`crypto`包中的散列函数来加密它。

在这个例子中，我使用了`sha1`算法的 convert 函数来生成哈希值:

如果你使用`hmac`它几乎和以前一样简单。您所需要的只是一个额外的`utf8`格式的键，并创建一个`hmac`对象，我们将哈希算法传递给它。在这种情况下，`sha1`和`key`。其余的代码看起来几乎相同。

为了重新创建上面视频中显示的应用程序，我创建了一个新的有状态小部件。下面是它的代码:

上面的片段中发生了很多事情。以下是它们的简要概述:

*   小部件声明了一个`TextEditingController`，它获得一个默认文本。
*   调用状态的`build`方法来创建实际的小部件。我使用了一个`Center`小部件作为根对象，它得到一个`Column`作为它的子对象。
*   在`child`中，指定了一个`TextField`和一个`Card`小部件。`TextField`应该包含我们要加密的文本，因此得到了`TextEditingController`。
*   对于`onChanged`方法，需要调用`setState`方法，这样文本的编辑才能正常工作。`Card`小部件得到一个填充间隔符和一个`ListTile`，其中包含一个`title`(使用的算法)、一个`subtitle`(散列值)和一个`trailing`图标。该图标将在以后用于复制文本。
*   `_hashValue`函数获取将用于加密的算法。我们将在下面使用相同的函数来复制各自的值。因为我们想要创建一个散列值的列表，所以散列值不存储在变量中。

为了通过一次点击复制散列值，我们已经在尾随图标的`onPressed`属性中设置了`onCopyPressed`方法——该方法获取算法。相应的功能如下所示:

在上面的代码中，我们调用了`Clipboard`对象的`setData`方法，并将哈希值传递给它。

为了向用户确认复制成功，我们设置了一个`SnackBar`,显示复制到剪贴板的哈希值。使用`ScaffoldMessenger`我们可以显示小吃店。

为了能够迭代`crypto`包中所有可用的散列函数，并为每个函数显示一个单独的`Card`小部件，我们使用下面的代码部分:

在上面的第一步中，我创建了一个`_algorithmMap`来迭代所有函数。

随后，我对这张地图进行迭代，将文本设置为标题，并使用算法对其进行加密。因此，对于地图中的每个条目，都会创建一个单独的卡片小部件，它具有单独的属性。

散列函数本身不需要改变，因为它已经获得了值。

# 限制

为了展示这个小项目的局限性，我想介绍几件事:

1.  本项目中使用的`crypto`包没有经过任何安全专业人员的审查和评估。在生产系统中使用算法的风险由您自己承担。系统的安全性应该由专业人员或通过扩展测试来确保。
2.  项目中使用的`crypto`包目前只支持提到的`sha`、`md5`和`hmac`算法。因此，不提供其他哈希算法，如`BLAKE`或`RIPEMD`。此外，正如已经提到的，这个包不是为了加密和解密，而只是为了散列值。

完整的源代码可以在我的 [GitHub 资源库](https://github.com/lucianojung/flutter_package_examples/blob/master/lib/packages/crypto/crypto_main.dart)中找到，附带的应用程序可以在[这里](https://flutter-package-examples.netlify.app/#/)找到。感谢阅读。