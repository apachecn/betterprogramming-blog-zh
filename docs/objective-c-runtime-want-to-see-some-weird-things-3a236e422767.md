# Objective-C 运行时——想看看一些奇怪的东西吗？

> 原文：<https://betterprogramming.pub/objective-c-runtime-want-to-see-some-weird-things-3a236e422767>

## 4 样东西和额外的小费

![](img/22848d7f8f11d112339cc8fe150641e3.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

iOS 开发如今更侧重于基于 Swift 的代码。但是 Objective-C 还在，而且很长一段时间都不会移除。您可能已经看过一些 ObjC 代码，许多人在看到这些代码时都会感到震惊:

括号无处不在。我对此并不感到震惊。也许我应该去找个人谈谈。

无论如何，ObjC 可以很强大，ObjC 运行时可以做出一些疯狂的事情。我会在这篇文章中分享一些例子。

# 如何使用 ObjC 运行时

这很简单。导入它:

嗯，Swift 中没有要导入的内容。就用吧。方法很多，我就不一一解释了。就几个。

# 这篇文章将涵盖哪些内容？

*   搅拌方法
*   方法创建和替换方法实现
*   获取所有可用的类
*   我对运行时做的最奇怪的事情
*   额外小费

# 搅拌方法

这是开发人员在使用 ObjC-runtime 时最常做的一件事。事实上，我不确定 Swift 开发者意识到通过使用 swizzling 方法，他们是在使用 ObjC。

但是什么是“swizzling”呢？

Swizzling 方法可以翻译成“交换方法的内容”

*等等，什么？交换方法的内容？这是巫术。*

你是对的。确实是。

好吧，让我们说，你想拦截所有的推送通知之前，给应用程序。乍一看是不行的，因为 iOS 大部分时候是把通知内容传给`UNUserNotificationCenterDelegate`、*、*也就是`AppDelegate`或者`SceneDelegate`。所以你被它卡住了，你只能在`UNUserNotificationCenterDelegate`收到通知后调用你的一个方法。

这就是 ObjC-runtime 与`method_exchangeImplementations`的关系。

事情会变得很奇怪:

1.  获取您想要交换的类(是的，类—不是实例化的对象)的方法实现。
2.  获取要与之交换方法的类的方法实现。
3.  进行方法交换。

就这么简单。

也许用一点代码，你会更容易得到它:

这里最重要的要求是在初始化时继续搅拌。如果之后进行重组，它将无法正常工作或运行。

嘿，没那么简单。嗯，没有。只是看一下这个方法的内容:

```
myClassPushApplication:didReceiveRemoteNotification:fetchCompletionHandler:
```

你刚刚注意到最奇怪的事情，不是吗？

最后，该方法调用自身。嗯，不完全是因为您通过 ObjC-runtime 交换了方法的实现。您在方法中编写的代码:

```
myClassPushApplication:didReceiveRemoteNotification:fetchCompletionHandler:
```

运行中的方法`AppDelegate`:

```
application:didReceiveRemoteNotification:fetchCompletionHandler***:***
```

一开始你不会注意到。你的大脑还没有准备好阅读这样的东西，这很正常。为了简化，假设您在`AppDelegate`中编写代码，但是在`MyClass`中，ObjC 运行时交换方法实现。

但是您应该注意到一些事情:当 swizzling 完成一次时，它将为所有的`MyClass`实例完成。这就是为什么你会看到一个`sharedInstance`。这是为了避免多次痛饮。

# 方法创建和替换方法实现

什么？！

你没看错。我们正在讨论方法创建和替换。对于整个班级来说。

以推送通知为例，我们可以检查原始方法是否存在(在`AppDelegate`中的那个)。

你是怎么变魔术的？

嗯，你可能会想到`respondsToSelector`，但是它需要原始类的一个实例。在我的例子中，我只有`class`——没有实例变量。

这里，我将使用 ObjC 运行时方法`class_addMethod`。

如果结果为假，则说明该方法存在于原`class`中。如果为真，则为`class`创建方法，但不实现。就像一个内部空无一物的方法签名。

考虑到这一点，你会问，“为什么我要添加一个空方法，因为它在任何地方都没有被引用，所以不能被调用？”好吧，你可以用你自己的方法替换创建的方法:`class_replaceMethod`。

所以，让我们改进一下之前的搅酒方法:

它通过检查原始方法是否存在于`AppDelegate`中来提高方法的切换能力。如果您构建一个依赖于`PushNotification`的 SDK，但是应用程序的开发人员并没有在其`AppDelegate`中包含所有的委托方法，那么这可能是有用的。

# 获取所有可用的类

这个技巧非常简单。它列出了所有可用的类。

*全部？*

嗯，是的。你也有 iOS 的。

*但是……为什么？*

假设您的应用程序中有多个组件，但是不知道这些类。但是，您可以确定您想要定位的每个类都遵守一个协议。列出所有可用的类并测试协议符合性将是获得您想要的特定类的最佳方式。

为了做到这一点，ObjC 运行时让我们可以访问一个名为`objc_getClassList`的方法:

在第一次调用中使用参数`NULL`和`0`，您将只得到可用类的总数，而没有类定义。创建一个数组，对象`classList`，大小为数字**是很有用的。**第二次调用`objc_getClassList`**将用类定义填充数组。**

**之后，您可以检查这些类是否响应特定的选择器，并对这些类执行您想要的操作。**

# **我对运行时做的最奇怪的事情**

**假设你想窥探类的道具和值。**

**我现在就阻止你。你为什么要窥探职业的道具和价值？**

**嗯，对于一些用例来说，统计一些屏幕的可见性或来自未知`ViewControllers`的一些数据可能会很棒。我不会进一步详述，但这是我得到的一个用例。**

**为此，我们将使用 ObjC 运行时的两个方法:`class_copyPropertyList`和`property_getName`。**

**第一个是`class_copyPropertyList`，将给出一个类中可用的属性数组。第二个，`property_getName`，将给出属性的名称。**

***就这样？***

**不，没那么简单。当您获得属性的名称时，它不是一个直接可用的字符串。因此，您需要进行一些字符串解析，以便只获得作为可用字符串的属性名。**

**让我们深入一些代码:**

**好的，但是我现在如何使用它呢？**

**不错！有了这段代码，我可以从所有可用的类中提取所有的对象。**

# **结论**

**Objective-C 运行时有一些您可以使用的强大工具。这是一个奇怪的工具，但要小心你能做什么。有人说强大的能力伴随着巨大的责任，所以你要对你在 ObjC 运行时所做的事情负责。**

**你也应该意识到一些工具是危险的。例如，多次使用同一个方法是非常危险的，会导致一些很难调试的崩溃。**

***特别感谢***

# **额外提示:在 Info.plist 中没有声明字体的情况下加载字体**

**嗯，这不使用 ObjC 运行时，但我认为这是伟大的分享。要在 iOS 上使用自定义字体，必须在项目的`Info.plist`中声明字体。**

**这就是理论。您实际上只能加载带有代码的字体。CGFont 和朋友可以做得很好:**

**提供的`NSData`是字体文件内容。**

***好吧，但显然有些事情你不能这么做。***

**你说得对。不能用故事板或者 Xib 的字体。你只能在代码中使用它。**