# 使用自定义调试描述使 Swift 中的调试更加容易

> 原文：<https://betterprogramming.pub/use-custom-debug-descriptions-to-make-debugging-easier-in-swift-7762ccf140e4>

## 通过为您的自定义类型实现单一协议来改进“po 对象”描述

![](img/b77fabd084714dc63309d155d10f429b.png)

克里斯托弗·伯恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

自定义调试说明可以帮助您调试自己的自定义对象、结构、错误和其他类型。每当你打印出一个对象，你可能会得到一些基本信息，但这些信息并不能真正帮助你解决问题。打印出的结构显示了所有的值，而在大多数情况下，您可能只对其中的一个属性感兴趣。

Swift 为任何带有`String(reflecting:)`初始值设定项的类型提供了默认的调试文本表示。`debugPrint(_:)`方法用于不提供自身表示的类型。尽管这在大多数情况下已经很好地工作了，但是通过为您经常交互的类型实现您自己的自定义表示，您可以受益更多并加快调试。

在本文中，我们将使用`print`和`debugPrint`方法，稍后将对它们进行更详细的描述。注意，在 LLDB 调试会话中使用`po object`与在代码中使用`debugPrint`是一致的。

# Swift 中提供的默认文本表示

在我们开始添加改进之前，最好了解一下 Swift 默认为我们提供了什么。为此，我们将回顾一些常用的类型，并使用`print()`和`debugPrint`方法。这两种打印方法几乎相同，但是`debugPrint`方法允许您有选择地打印出更详细的信息。我们稍后会复习几个例子。

我们从下面定义为类的`BlogPost`实体开始例子:

一旦我们将它打印出来，我们会得到以下结果:

```
let blogPost = BlogPost(title: "Improved debugging", body: "Help yourself in those hard times.")
print(blogPost) 
// Prints: BlogPost
debugPrint(blogPost) 
// Prints: BlogPost
```

将它更改为 struct 已经改进了 print 语句:

这在大多数情况下可能就足够了。

这不包括打印出的`NSObject`的实例，比如继承自该类型的`UIViewController`。它通常会产生无用的信息:

```
final class BlogPostViewController: UIViewController { }
let viewController = BlogPostViewController()
print(viewController) 
// Prints: <BlogPostViewController: 0x7ff77ac0c720>
debugPrint(viewController) 
// Prints: <BlogPostViewController: 0x7ff77ac0c720>
```

您可以看到这里还有改进的空间，因为我们不知道这个视图控制器代表什么。默认的定制调试描述并不能帮助我们改进调试。

# **使用 CustomDebugStringConvertible 改进调试打印**

`CustomDebugStringConvertible`协议的出现是为了改进定制对象的调试日志。它由`print`和`debugPrint`方法使用，除非使用了`CustomStringConvertible`协议。为了解释这一点，我们为视图控制器实例添加了对两个协议的一致性。

我们首先定义视图控制器，并用一个`BlogPost`实例初始化它:

打印此实例会产生以下日志:

```
print(viewController) 
// Prints: <BlogPostViewController: 0x7fb390c127f0>
debugPrint(viewController) 
// Prints: <BlogPostViewController: 0x7fb390c127f0>
```

我们首先通过返回博客文章的标题和正文来添加对`CustomDebugStringConvertible`协议的一致性:

使用此代码示例将导致以下错误:

```
Redundant conformance of ‘BlogPostViewController’ to protocol ‘CustomDebugStringConvertible’
```

这是因为`NSObject`实例已经符合`CustomDebugStringConvertible`协议。因此，我们可以省去协议定义，只需覆盖`debugDescription`属性:

再次打印出相同的实例将为我们提供更好的信息:

```
print(viewController) 
// Prints: <BlogPostViewController: 0x7fa013406810> represents the post with title "Improved debugging" and body "Help yourself in those hard times."
debugPrint(viewController) 
// Prints: <BlogPostViewController: 0x7fa013406810> represents the post with title "Improved debugging" and body "Help yourself in those hard times."
```

向`NSObject`的实例添加定制的调试描述是额外有价值的，因为默认的表示通常根本没有用。

# 在非调试打印中隐藏敏感数据

有时，您只想在调试会话期间打印出某些数据。如果你的应用程序支持的话，常规的打印语句可能会在用户可见的日志中结束。例如，我们正在使用[诊断框架](https://github.com/WeTransfer/Diagnostics)来帮助我们调试用户会话，在这些会话中，我们的所有日志都可能被阅读该报告的用户看到。

我们可以利用`CustomStringConvertible`来添加日志之间的区别。例如，我们可能希望在常规打印过程中隐藏文章正文:

注意，我们没有直接添加协议继承，因为这已经由`NSObject`再次完成了。再次打印出相同的实例向我们展示了对`print`和`debugPrint`的两种不同描述:

```
print(viewController) 
// Prints: <BlogPostViewController: 0x7fa42fe09bf0> represents the post with title "Improved debugging"
debugPrint(viewController) 
// Prints: <BlogPostViewController: 0x7fa42fe09bf0> represents the post with title "Improved debugging" and body "Help yourself in those hard times."
```

如您所见，`print`方法使用了`description`属性，而`debugPrint`使用了`debugDescription`属性。在使用这些协议创建定制调试描述时，了解这一区别既重要又有用。

我们在这里使用`super.description`，因为使用`self`会导致使用反射 API，而反射 API 使用`description`，反射 API 使用我们的自定义描述，这是一个无限循环。打印出实例仍然很有用，这样你就可以知道对象的类型和内存地址。

上面的例子可以通过重用`description`值得到更好的改进:

# 改进了 Swift 中错误类型的记录

相同的协议可用于 Swift 中的任何类型，如错误。我们可以为自定义错误类型添加改进的日志记录支持，并显示解释失败的更详细的描述:

# **向结构类型添加改进的日志记录**

尽管结构在默认情况下有很好的表示，但我们可能还是想通过添加自定义的调试描述来改进这些表示，以便以更有意义的方式打印出值:

# 结论

在大多数情况下，调试已经很耗时了。任何可以帮助我们更快、更容易地解决问题的东西都有很大的价值。我们可以区分调试打印和常规打印，以便只打印出对阅读我们日志的用户不敏感的数据。这样做是非常有价值的，因为 LLDB 会话中的`po object`使用了相同的`debugDescription`。

感谢阅读！