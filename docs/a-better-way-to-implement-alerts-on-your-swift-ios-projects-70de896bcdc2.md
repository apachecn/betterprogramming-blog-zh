# 使用工厂模式实现 iOS 警报

> 原文：<https://betterprogramming.pub/a-better-way-to-implement-alerts-on-your-swift-ios-projects-70de896bcdc2>

## 在 iOS 项目中创建提醒的更好方法

![](img/fc04fcf8c3d8b5eb332858d6b86e9792.png)

照片由 [Amza Andrei](https://unsplash.com/@andreiamza2000?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我会在 SwiftUI 中显示这个吗？

SwiftUI 是下一个最好的东西，但是如果你工作的公司和大多数公司一样，仍然在使用 UIKit。这里有一个我推荐使用的更好的实现。

请注意，这仍然可以与 SwiftUI 一起使用，但是我将在以后的文章中讨论这一点。

# 先决条件

*   工厂模式
*   代表
*   协议
*   Getters 和 Setters
*   `UIAlertController`
*   `UIAlertController.Style`

## 为什么要这样写警报？

你为什么想要重写你的 iOS 提醒？如果你想在你的`ViewControllers`上使用干净的架构模式保存一些代码，请跟我来，因为这将是只有代码部分的第一部分。

我将更深入地探讨以这种方式编写的代码能做什么，以及为什么它对您和下一个 iOS 开发人员来说是有益的。

# 让我们从建造我们的工厂开始

在您的 iOS 项目上实现警告的典型方式可能会非常混乱，并且会占用`ViewControllers`上的空间。

让我们从创建一个文件开始，该文件将保存用于构建警报的工厂。我们想从创建一个协议开始，我们可以用它来使我们的类也符合。

让我们把它命名为`AlertFactoryService`，暂时把它设为黑色。

```
protocol AlertFactoryService {
}
```

# 培养我们的代表

然后，我们将创建一个名为`AlertActionDelegate`的代理，它将包含我们对警报的操作。我们现在也让它空着。

```
protocol AlertActionDelegate {
}
```

# 构建我们的实现类

现在我们有了服务和委托，我们可以开始创建类了。我们将其命名为`AlertImplementation` ，使其符合我们的`AlertFactoryService` *。*

然后开始为我们的委托创建一个弱可选变量以备将来使用。我们将需要一个我们的工厂可以使用的构建函数，所以现在让我们创建一个空的构建函数。因此，我们有:

```
class AlertImplementation: AlertFactoryService {
    weak var delegate: AlertActionDelegate?

    func build() { }
}
```

现在我想创建我的构建函数，但是为了这样做，我们需要考虑我们的警报以及我们想要在它上面显示什么。

等等！我们还希望记住，我们希望将它与我们想要显示的任何警报数据一起重复使用，我们还希望它可以在任何`ViewController`上轻松使用。我们如何做到这一点？好吧，让我们从数据开始。

# 构建我们的数据结构

警报由几个部分组成，因此让我们按照它们各自的类型列出它们。

*   `Title`:字符串
*   `Message`:字符串
*   `OkActionTitle`:字符串
*   `CancelActionTitle`:字符串

看起来我们已经掌握了基本知识，但是如果我们只想要一个取消按钮或者只想要一个确认按钮呢？让我们在我们的列表中增加一些项目来涵盖这些情况。

*   `EnableCancelAction`:布尔
*   `EnableOkAction`:布尔

嗯，如果他们想要不同风格的 a `UIAlertController`和不同风格的`UIAlertAction`井，我们也会添加它们。

*   款式:`UIAlertController.Style`
*   `OkActionStyle` : `UIAlertAction.Style`
*   `CancelActionStyle` : `UIAlertAction.Style`

好了，我想这就够了。让我们开始创建我们的结构，并将其命名为`AlertViewData`,它将保存所有这些内容和一个 init，以备不时之需。

首先，我们将在结构中提到的所有项上创建常数。我们应该有这个:

AlertViewData 的常量

(可选，但将来有助于减少代码)

现在让我们添加一些缺省值。在大多数情况下，我们的样式几乎总是属于类型`.alert`，而我们的`cancelActionStyle`几乎总是属于类型. cancel。

看起来我们可以在这个结构中使用任何数据，甚至可以用一两个按钮创建不同的样式。我们可以添加更多的按钮，但我认为在这之后，你将能够自己做到这一点。现在我们的结构完成了，看起来应该是这样的。

完成警报视图数据

# 完成我们的协议

现在让我们回去完成我们的协议。首先，我们的工厂需要将所有东西都包含在我们的实现类中，并做一些编辑。所以我们需要添加变量委托和构建函数。

之前我们提到我们希望这个构建可以在任何`UIViewController`上运行，所以让我们的构建返回一个`UIViewController`。我们还希望他们能够用他们想要的任何数据来构建它，所以让我们将我们的`AlertViewData` struct 作为参数添加到我们的函数中。好了，我们应该有这个。

```
protocol AlertFactoryService {
    var delegate: AlertActionDelegate? { get set }
    func build(alertData: AlertViewData) -> UIViewController
}
```

完美现在我们的代表，这将是超级容易的。我们只需要一种方法来调用我们的动作，在这种情况下，只有我们的 ok 和 cancel 动作。所以我们只需要做两个函数就可以了。我们的委托协议现在应该是这样的。

```
protocol AlertActionDelegate {
    func okAction()
    func cancelAction()
}
```

# 在实现类中完成构建函数

太棒了。如果你还明白我的意思，我们现在要开始构建函数了。那我就举个小例子，怎么用。

所以让我们从更新我们的函数来匹配我们的`AlertFactoryService`构建签名开始。

很好，现在我们需要创建一个`UIAlertController`，它将要求我们的第一个数据参数 title、message 和 preferredStyle。我们将使用 builds 参数来填写这些内容，如下所示。

```
let vc = UIAlertController(title: alertData.title, message: alertData.message, preferredStyle: alertData.style)
```

太棒了，我们有了`UIAlertController`,现在让我们看看是否有一两个按钮。我们可以使用我们的`alertData` 参数来访问我们的`enableOkAction`和`enableCancelAction`见下文。

```
if alertData.enableOkAction {
    // We have a okay button create a UIAlertAction          
}

if alertData.enableCancelAction {
    // We have a cancel button create a UIAlertAction
}
```

现在让我们开始填写我们的`enableOkAction`案例，首先创建我们的`UIAlertAction`，它将给出一个闭包，允许我们在点击这个按钮时执行一个动作。

这就是我们使用委托变量调用`AlertActionDelegate`*`okAction`的地方。然后剩下的就是将我们的动作添加到我们的`UIAlertController`中。所以现在我们有了这个。*

```
*if alertData.enableOkAction {
   let okAction = UIAlertAction(title: alertData.okActionTitle, 
                                style: alertData.okActionStyle) 
   { (_) in
     self.delegate?.**okAction**()
   } vc.addAction(okAction)
}*
```

*有了这个，我们几乎已经完成了我们的`enableCancelAction`案例，只做了一点小小的改动。看看这个。*

```
*if alertData.enableCancelAction {
   let cancelAction = UIAlertAction(
                             title: alertData.cancelActionTitle, 
                             style: alertData.cancelActionStyle) 
   { (_) in
     self.delegate?.**cancelAction**()
   } vc.addAction(cancelAction)
}*
```

*现在剩下的就是返回我们的符合 UIViewController 的`UIAlertController`,如此处所示，因此无需在此处进行[造型](https://developer.apple.com/documentation/uikit/uialertcontroller)。*

# *最终代码*

*最后，我们来总结一下这个精彩的作品:*

*完全警报实施*

# *示例使用*

*这里有一个如何使用它的例子。如果您有任何问题或者您认为有更好的方法，请告诉我。我来试着回答一下我这个小编的分解版的问题。*

*使用中的警报实施*

*我把这个留给你。学会快速编写 iOS 产品代码！*