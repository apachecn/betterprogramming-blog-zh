# 如何在 SwiftUI 中从您的相机或照片库中挑选图像

> 原文：<https://betterprogramming.pub/how-to-pick-an-image-from-camera-or-photo-library-in-swiftui-a596a0a2ece>

## 将手机照片添加到 SwiftUI 应用程序

![](img/3cb008ceb072e75412153794dbe2fb33.png)

SwiftUI 是 WWDC19 的一个精彩预告。由于 SwiftUI 是一个不到一年的框架，我们没有丰富的声明式 API 来满足许多基本需求，如从图片库或相机、地图等中选择图像。

由于苹果总是以这样或那样的方式提供多种选择来实现我们的需求，我们在 SwiftUI 中确实有一些东西来集成我们现有的来自 UIKit 的`UIView`和`UIViewControllers`与 SwiftUI。

这些被称为`[UIViewRepresentable](https://developer.apple.com/documentation/swiftui/uiviewrepresentable)`和`[UIViewControllerRepresentable](https://developer.apple.com/documentation/swiftui/uiviewcontrollerrepresentable)`协议。

*   `UIViewRepresentable`协议用于集成 UIKit 视图和 SwiftUI。
*   `UIViewControllerRepresentable`协议用于将 UIKit `ViewController`与 SwiftUI 集成。

我们今天不打算详细谈论这些协议，WWDC19 上有一个很好的演讲，请查看[这个视频](https://developer.apple.com/videos/play/wwdc2019/231/)了解更多细节。

我们将使用`UIViewControllerRepresentable`，因为我们需要将`UIImagePickerController`(ui kit 中的`ViewController`)与 SwiftUI 集成，以便从照片库中访问照片或从相机中捕捉实时照片。

# 决赛成绩

# 什么是 UIViewControllerRepresentable？

*   我们需要在自定义 SwiftUI 视图中采用一个协议来集成 UIKit `ViewController` s。
*   `UIViewControllerRepresentable`有两个必须遵守的方法，`makeUIViewController(…)`和`updateUIViewController(…)`。
*   `ViewController`初始化逻辑将进入`makeUIViewController(…)`方法。我们需要在这个方法中初始化`UIImagePickerController`，我们一会儿就去做。
*   每当 SwiftUI 请求在屏幕上呈现视图以响应状态变化时，我们都需要更新`updateUIViewController(…)`方法中已初始化的`ViewController`的实例。
*   在这个协议中我们还有一个可选的方法叫做`makeCoordinator()`。协调器管理`ViewController`和 SwiftUI 之间的通信，例如`ViewController`的目标、动作、代理等。
*   我们将很快看到上述所有方法的实际应用。请检查下面的代码，我会解释里面的内容。

SUImagePickerView.swift

# 说明

*   第 11 行:`SUImagePickerView`是自定义的视图结构，有三个属性，采用了`UIViewControllerRepresentable`协议。
*   第 13 行到第 15 行:我已经声明了一个属性来引用`PickerController`的`sourceType`和两个绑定属性来在用户选择图像或取消时通知初始化`SUImagePickerView`的视图。

注意:`@Binding`属性包装器用于在视图之间共享公共数据。

*   第 17 行:`makeCoordinator()`。这里我们已经初始化并返回了我们的自定义协调器类。
*   第 21 行:我们用适当的`sourceType`初始化`UIImagePickerController`，并将代理设置为协调者。这是我们的自定义协调器，它将管理图像拾取器委托，并返回初始化的图像拾取器控制器。
*   第 28 行:由于我们在图像拾取器控制器中没有任何要更新的东西，我们可以让它为空。如果移除此方法，可能会出现错误。
*   第 34 行:这是我们的定制协调器，它处理图像拾取器控制器的委托方法，提取用户选择的图像，并在共享图像绑定属性中设置它。
*   第 44 行到第 53 行:您可能知道图像拾取器控制器委托方法，当用户拾取图像或单击取消按钮时，我们将在这里得到一个回调。
*   第 46 行:我们使用从 picker 控制器返回的`UIImage`创建 SiwftUI 的`Image`，并将其分配给一个共享的`@Binding`属性。
*   第 52 行和第 48 行:重要的是设置`self.isPresented = false`来解除呈现的`SUImagePickerView`。

# 密码

请查看下面的代码片段，了解我们如何从`ContentView`初始化并呈现我们的自定义`SUImagePickerView` 的详细信息。

ContentView.swift(初始化 SUImagePickerView 的视图)

# 说明

在`ContentView.swift`中，我已经添加了逻辑，当用户点击图像以询问相机或照片库选项时，呈现出`ActionSheet`。

一旦用户点击任何选项，我将向`SUImagePickerView`展示相应的`sourceType`。

您可以下载并在您的项目中使用`SUImagePickerView.swift`。完整代码请看[我的 GitHub repo](https://github.com/karthironald/SUImagePickerView) 。

# 结论

你几乎可以将任何`UIViewController`与 SwiftUI 集成。

如果您在不了解绑定、状态和视图修饰符等属性包装器的情况下阅读本文，`ContentView.swift`和`SUImagePickerView.swift`中的代码会有点混乱。

阅读关于这些主题的一些基本细节可能有助于理解这个流程。

谢谢！