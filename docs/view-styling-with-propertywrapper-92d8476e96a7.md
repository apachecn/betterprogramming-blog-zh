# 使用@propertyWrapper 的 UIView/NSView 样式

> 原文：<https://betterprogramming.pub/view-styling-with-propertywrapper-92d8476e96a7>

## 将样式从布局中分离出来，使代码更具可读性。

![](img/7c95318bbbc3ab3bf6c134e6e3fb0f92.png)

克里斯汀娜·戈塔迪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

感觉很自然地将 UIView 样式代码从布局逻辑中移出，同时使样式可重用。

考虑带有一些嵌入式布局代码的标准`UIViewController`:

我们甚至还没有看到布局代码，我们的视图控制器已经一团糟。

人们经常将这些分解成更小的函数，或者编写扩展 UIView 的助手函数，比如`.configure { }`，但是即使这样，我们还是会遇到不可避免的麻烦。

我也看到有人通过创建每个视图类型的子类来解决这个问题，只有当我们修改行为而不仅仅是为了移动一些样式逻辑时，才应该这样做。

我参与的一些较好的项目已经定义了样式表。因此，每个视图初始化器可能采用一个`Style<UITextField>`或一个`apply(Style<UITextField>)`，这肯定是更好的方法——但我突然明白，为什么不去掉拥有初始化器或自定义函数的需要，而只使用一个`@propertyWrapper`？

> 每个功能可以有自己的`@propertyWrapper`风格，共享的风格可以放在一个公共的包中。

上面的代码可以移动到一个包含三个`@propertyWrappers`的*样式表*文件中。

我们可以在另一个屏幕中重用这个`SubmitStyle`，因为它是从我们的`LoginViewController`中提取出来的。

再来看看我们修改后的`LoginViewController`:

嗯，这当然是一个巨大的进步！

我们的属性包装器甚至可以处理视图的初始化，并且不需要指定类型。

当状态改变，我们想改变风格时怎么办？这就是我在视图或视图扩展的子类中引入属性的地方。

这类似于其他 UI 组件的工作方式，例如 UIButton，您预先为不同的状态定义每种样式，然后设置状态(即`isEnabled = false`)。

## 懒惰的

这个难题还缺少一部分，即视图的惰性初始化，所以它们只在我们的 ViewController 准备好布局时创建，比如在`viewDidLoad`之后。

为了重新添加这部分功能，我们需要使用一个`@Lazy`属性包装器。如 [swift 提案 0258](https://github.com/apple/swift-evolution/blob/main/proposals/0258-property-wrappers.md) 所述。

为了保持语法简洁，我们需要将它的初始化器包装在一个`configure`函数中:

让我们来看看上面的`EmailStyle`用懒惰初始化修改过的。

太好了！现在这个`FormTextField`不会被初始化，直到我们第一次尝试访问它，这通常是在`viewDidLoad`。

我已经创建了一个项目，包括这个和其他概念[在这里](https://github.com/cjnevin/IdealCleanArchitecture)。

感谢阅读！