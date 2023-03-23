# 在 iOS 13 中创建不带故事板的应用

> 原文：<https://betterprogramming.pub/creating-apps-without-storyboards-in-ios-13-fc9550bb9c12>

## 创建没有故事板的应用程序的分步指南

![](img/d0ddb0ad6ecaf79aa1b5e87bfa4c2b0f.png)

照片由[威廉·胡克](https://unsplash.com/@williamtm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/iphone-app?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

如果你有兴趣在 iOS 12 或以下版本中创建无故事板的应用，请阅读我之前的文章 [*创建无故事板的 iOS 应用*](https://medium.com/better-programming/creating-ios-apps-without-storyboards-42a63c50756f) 。

许多 iOS 开发文章和教程使用故事板来创建用户界面。这是有意义的，因为它们是创建和修改你的设计的非常直观的方式，并有助于加快学习过程。

但是，除非你发现自己是一个应用程序的唯一开发者，否则这不是团队环境中专业 iOS 开发的完全现实的观点。

当你第一次面对一个只有很少或者没有故事板的应用时，它可能会对系统造成冲击。让我们通过了解在没有故事板的情况下如何设计一个应用程序来为这种可能性做好准备。

# 使用编程布局的情况

我是一个非常视觉化的人，我个人反对这种方法，所以我可以理解你是否也有这种想法。

如果你曾经在团队环境中工作过，你可能会有一些非常困难的尝试来解决故事板的合并冲突。你可能还会被同事阻止，他们需要在你完成特定任务之前修改故事板。

如果这些事情还没有发生在你身上，它们会发生的。有一些方法可以缓解这些问题，比如拆分故事板和[xib](https://docs.elementscompiler.com/Platforms/Cocoa/XIB/)，但它们并不是万无一失的，一旦你习惯了没有故事板，你就会开始看到好处。

到目前为止，最大的好处是比你一直坚持故事板更好地理解 UIKit。而且，正如我们在 [WWDC2019](https://developer.apple.com/wwdc19/) 上看到的 [SwiftUI](https://developer.apple.com/xcode/swiftui/) 的首次亮相，故事板真的将成为过去。

那么，为什么这么多的团队环境趋向于去除故事板和 xib 呢？

除了合并冲突和阻塞问题，团队远离故事板的主要原因之一是大多数属性值不容易被审查。

从故事板 XML 中挑选代码很容易出错，因此您最好重做这项工作，这消除了拥有代码存储库的一个优势。让我们看一个例子。

我有一个简单的单视图应用程序，其表单包含两个文本字段和一个按钮。按钮对其中一个文本字段有一个垂直约束，该约束有一个常量`20`。

![](img/bc6fb8fb633e796794a700b86bbfedf2.png)

设计师要求我们将这个数字增加到`40`。我进行了更改，提交了一份 PR，并指定您为审核人。您可以在 PR 中看到以下变化。

原始代码:

```
<rect key="frame" x="20" y="383" width="374" height="30"/>
<rect key="frame" x="20" y="333" width="374" height="30"/>
<constraint firstItem="pYg-JC-an0" firstAttribute="top" secondItem="Kfc-mn-gVn" secondAttribute="bottom" constant="20" id="r92-HL-ecY"/>
```

修改代码:

```
<rect key="frame" x="20" y="363" width="374" height="30"/>
<rect key="frame" x="20" y="313" width="374" height="30"/>
<constraint firstItem="pYg-JC-an0" firstAttribute="top" secondItem="Kfc-mn-gVn" secondAttribute="bottom" constant="40" id="r92-HL-ecY"/>
```

我在这里改变了什么？你知道它应该是按钮控件和文本字段之间的约束，但是你确定`pYg-JC-an0`和`Kfc-mn-gVn`是正确的控件吗？

您可以浏览 XML 来找到这两个值，但是如果这是一个包含许多控件的复杂视图，并且我修改了所有控件的间距，那该怎么办呢？你真的有时间把他们都找出来吗？

如果代码就在您面前，在视图控制器中，包含所有其他视图逻辑，不是更容易吗？

那么，在这个例子中，编程布局中的这种修改会是什么样子呢？

原始约束:

```
loginButton.topAnchor.constraint(equalTo: passwordTextField.bottomAnchor, constraint: 20)
```

修改的约束:

```
loginButton.topAnchor.constraint(equalTo: passwordTextField.bottomAnchor, constraint: 40)
```

是啊。差别很大。

这不仅会使 PRs 更容易审查，而且追踪与布局相关的 bug 也变得更加容易。所有与布局相关的设置都将在代码中呈现在你面前，而不是隐藏在 Xcode 检查器的几十个地方。

所以，让我们看看没有故事板的情况下需要做些什么。

# 入门指南

为了开始学习编程布局，让我们在 Xcode 中创建新项目。

首先，创建一个单视图应用程序。

![](img/f195740ba221771ef69d5b4a8abb6550.png)

把它命名为`GoodbyeStoryboard`。如果你正在使用 SwiftUI，你不需要担心这些，所以确保用户界面设置为`Storyboard`。将项目保存在适当的位置。

![](img/ab4593a41496eedc1454b41fae3d0231.png)

## 删除故事板

一旦项目被创建并在 Xcode 中打开，选择`Main.storyboard`文件并删除它。从确认对话框中选择*移至垃圾桶*。

![](img/5fa4f883bd0acf4adfb29953ef7b0fc3.png)

拜拜故事板！

在项目导航器中，选择项目。

在*部署信息*部分下的`GoodbyeStoryboard`目标中，从主界面字段中删除单词`Main`并按⌘S 保存更改。

![](img/2d625b1e860b5250667d632928bd8174.png)

接下来，选择`Info.plist`文件，在列表中展开`Application Scene Manifest` → `Scene Configuration` → `Application Session Role` → `Item 0`并移除项目`Storyboard Name`。

# 将 ViewController 分配给窗口

如果您现在构建并运行，项目将成功构建，应用程序将运行，但您只会看到一个黑屏。

这是因为您的应用程序不再有窗口。而且，即使这样，Xcode 为您创建的`ViewController.swift`也不再被设置为窗口的根视图控制器。

这些都是故事板自动处理的事情，而你却没有意识到。虽然这听起来像是积极的，但这也意味着使用 UIKit 的一些方面您还没有意识到，这是一个问题。

只需要少量代码就可以补救这种情况。打开`SceneDelegate.swift`。您将看到一个类型为`UIWindow?`的`window`属性，除了一些空函数，唯一真正的代码在方法`scene(_:willConnectTo:options:)`中，并且只包含一个 guard 语句。

删除方法`scene(_:willConnectTo:options:)`中的所有现有代码，并替换为以下代码:

```
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
  guard let windowScene = (scene as? UIWindowScene) else { return }
  window = UIWindow(frame: UIScreen.main.bounds)
  let viewController = ViewController()
  window?.rootViewController = viewController
  window?.makeKeyAndVisible()
  window?.windowScene = windowScene
}
```

当一个新的场景被添加到应用程序中时，就会执行`scene(_:willConnectTo:options:)`方法，这是实例化我们的应用程序窗口并使`ViewController`成为该窗口的根视图控制器的最佳位置。

让我们一行一行地检查上面的代码:

```
guard let windowScene = (scene as? UIWindowScene) else { return }
```

首先，我们通过将`scene as? UIWindowScene`分配给名为`windowScene`的属性，修改了方法中的原始 guard 语句，使我们可以访问当前场景的`UIWindowScene`对象。

```
window = UIWindow(frame: UIScreen.main.bounds)
```

接下来，我们创建窗口。通过使用`UIWindow(frame: UIScreen.main.bounds)`，我们正在创建一个新窗口，并将其大小设置为设备的屏幕大小。

```
let viewController = ViewController()
window?.rootViewController = viewController
```

我们现在初始化一个新的视图控制器。然后我们将它分配给窗口的`rootViewController`属性。根视图控制器只是一个窗口内容的容器。

```
window?.makeKeyAndVisible()
```

接下来，我们使窗口可见。`makeKeyAndVisible()`方法只是一个方便的方法，它强制窗口成为关键窗口(换句话说，它把它放在所有其他窗口的前面——在这一点上，没有其他窗口),并使它可见。

```
window?.windowScene = windowScene
```

最后，我们将`windowScene`属性分配给新创建的`window`对象。

构建并运行，你应该会看到…一个黑色的视图占据了整个屏幕。这不是很令人印象深刻，我们写代码之前有一个黑屏，所以这甚至工作吗？

打开`ViewController.swift`，在`super.viewDidLoad()`行后的`viewDidLoad()`方法中添加以下内容:

```
view.backgroundColor = .white
```

构建并再次运行，你现在应该看到一个完整的白色屏幕，证明我们现在有一个窗口，而且`ViewController`实际上是可见的。

恭喜你，你现在已经建立了一个没有故事板的应用程序！而且只用了四行代码。

总结一下到目前为止我们所做的，我们移除了故事板，修改了项目，使它不再寻找故事板，创建了我们的主应用程序窗口，并使`ViewController`成为窗口的根视图控制器。

很简单的东西。但是，那些您将拖放到故事板中并连接到`IBOutlets`和`IBActions`的视图和控件呢？我们接下来会看看这些。

# 添加 UI 元素

现在我们有了一个可以再次使用的视图，让我们深入研究如何添加 UI 元素。

在故事板中，我们只需将一个文本字段或按钮拖到视图上，并创建出口和动作，这样我们就可以用代码与它进行交互，但是没有故事板，这是如何实现的呢？

让我们从单个控件开始，慢慢来。

如果`ViewController.swift`尚未打开，请将其打开。在`viewDidLoad()`函数的正上方添加以下内容:

```
var loginButton: UIButton!
```

接下来，通过在`viewDidLoad()`中的`view.backgroundColor = .white`行之后添加以下代码，实例化并配置按钮控件:

```
loginButton = UIButton(type: .system)
loginButton.setTitle("Login", for: .normal)
loginButton.translatesAutoresizingMaskIntoConstraints = false
view.addSubview(loginButton)
```

让我们一行一行地看看这个:

```
loginButton = UIButton(type: .system)
```

这相当于在“身份”检查器中设置`Class`值，在“属性”检查器中设置`Type`值。

![](img/a5027a5210967840ae7ead64885ef8c7.png)![](img/2743960b82fca697d398a87fbc9a4ece.png)

```
loginButton.setTitle("Login", for: .normal)
```

我们将按钮的标题设置为正常状态。这相当于在属性检查器中设定这些值。

![](img/ab468aafe0932f3b453cf6e4efdea8b5.png)

```
loginButton.translatesAutoresizingMaskIntoConstraints = false
```

现在事情变得有趣了，因为这可能是你以前从未见过的。

这里，我们声明我们将使用`AutoLayout`。这看起来不像是我们正在做的事情，而且显式地设置这一点看起来很奇怪，但是这实际上是在你每次向故事板添加视图或控件时为你做的。

所以，不管你知不知道，你一直都在这么做。下面是按钮的脚本 XML 的一部分:

```
<button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" 
   contentHorizontalAlignment="center"     
   contentVerticalAlignment="center" 
   buttonType="roundedRect"
   lineBreakMode="middleTruncation" 
   translatesAutoresizingMaskIntoConstraints="NO" 
   id="DLu-Zl-IKU">
```

你会在最后看到`translatesAutoresizingMaskIntoConstraints="NO"`一直都在。所以，是的，为你添加的每一个视图或控件都这样做是很乏味的，但是你做得越多，它就会变得越自动化。

```
view.addSubview(loginButton)
```

当你把一个控件拖到故事板上时，很明显它已经变成了它的父控件的子视图，即使你从来没有想过它。与代码中的大多数事情一样，我们需要明确地说明这一点。

现在让我们来处理按钮的定位。现在，我们就把它放在中间。在`viewDidLoad()`功能后增加以下功能:

```
func constraintsInit() {
    NSLayoutConstraint.activate([
      loginButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
      loginButton.centerYAnchor.constraint(equalTo: view.centerYAnchor)
    ])
}
```

我们在这里所做的是将`loginButton`的中心 X 和中心 Y 锚点分别与`view`的中心 X 和中心 Y 锚点对齐。

如果你对主播不熟悉，我强烈推荐找一个主播教程。锚点非常易于阅读和理解，但是深入解释它们超出了本文的范围。我们会更多地使用它们，所以，如果它们对你来说是新的，现在就跟着做吧。

回到`viewDidLoad()`，在`view.addSubview(loginButton)`之后添加这一行:

```
constraintsInit()
```

构建并运行，您应该看到您的登录按钮就在视图的中央。

现在让我们加快进度，添加简单登录表单的剩余部分。在`var loginButton: UIButton!`行的正下方添加以下代码:

```
var nameTextField: UITextField!
var passwordTextField: UITextField!
```

接下来，在`viewDidLoad()`行之后`view.addSubview(loginButton)`行之前添加:

```
nameTextField = UITextField(frame: .zero)
nameTextField.placeholder = "Login Name"
nameTextField.borderStyle = .roundedRect
nameTextField.translatesAutoresizingMaskIntoConstraints = false
view.addSubview(nameTextField)passwordTextField = UITextField(frame: .zero)
passwordTextField.placeholder = "Password"
passwordTextField.isSecureTextEntry = true
passwordTextField.borderStyle = .roundedRect
passwordTextField.translatesAutoresizingMaskIntoConstraints = false
view.addSubview(passwordTextField)
```

在这一点上，这里唯一应该是新的东西是在我们将框架设置为`.zero`的实例化行中。

如果这对您来说是新的，它确实如其名:它将控件的大小设置为零。如果保持这种方式，运行应用程序时您将看不到该控件，因为它的高度和宽度都为零。这将通过添加我们的约束来解决，这是我们接下来要做的。

在`constraintsInit()`中，在`loginButton.centerYAnchor.constraint(equalTo: view.centerYAnchor)`行的末尾添加一个逗号，然后添加:

```
passwordTextField.bottomAnchor.constraint(equalTo: loginButton.topAnchor, constant: -20),passwordTextField.leadingAnchor.constraint(equalTo: view.readableContentGuide.leadingAnchor, constant: 20),passwordTextField.trailingAnchor.constraint(equalTo: view.readableContentGuide.trailingAnchor, constant: -20),nameTextField.bottomAnchor.constraint(equalTo: passwordTextField.topAnchor, constant: -20),nameTextField.leadingAnchor.constraint(equalTo: view.readableContentGuide.leadingAnchor, constant: 20),nameTextField.trailingAnchor.constraint(equalTo: view.readableContentGuide.trailingAnchor, constant: -20)
```

除了指出我们使用`readableContentGuide`进行文本字段的水平布局之外，我不打算在此详述。

在使用约束条件时，可以使用一些指南，可读内容指南是一种在视图中定义可读区域的指南，该区域不会被系统控件(如滚动条)遮挡。

它还通过考虑设备宽度和动态文本大小来调整可读宽度。这是一个需要注意的重要指南，尤其是在调整用户界面的可访问性时。

构建并运行，您应该会看到一个非常基本的登录表单，它是以完全编程的方式创建的，没有故事板！

还有最后一件事要处理:行动。

# 添加操作

当使用故事板时，你可能会将登录按钮连接到一个`IBAction`来处理点击事件。因为我们没有使用界面构建器，所以没有`IBAction`可以利用。相反，我们将使用一个目标动作。

在`constraintsInit()`功能后添加这个新功能:

```
@objc func handleLoginTouchUpInside() {
    print("Login has been tapped")
    if nameTextField.isFirstResponder {
      nameTextField.resignFirstResponder()
    }
    if passwordTextField.isFirstResponder {
      passwordTextField.resignFirstResponder()
    }
  }
```

因为用户可能会关注任一文本字段，所以我们将简单地查询每个字段，并询问它是否是第一个响应者。如果是这样，我们将放弃第一响应者的身份。

如果这对你来说是新的，在一个文本字段上重新签名第一个响应者将会隐藏键盘。此外，请注意，还有其他方法可以做到这一点，但这是一个足够好的演示方法。

现在，要为按钮上的点击事件添加一个动作，就在`viewDidLoad()`中的`view.addSubview(loginButton)`行下面添加以下内容:

```
loginButton.addTarget(self, 
                      action: #selector(handleLoginTouchUpInside), 
                      for: .touchUpInside)
```

这将为登录按钮添加一个目标，当检测到内部动作时，该按钮将运行`handleLoginTouchUpInside()`功能。

构建和运行，你应该看到一切工作了！如果您有任何问题，本文底部有完整的代码清单。

# 结论

如你所见，移除故事板并不困难。它揭示了视图和视图控制器的一些内部工作方式，尤其是布局，这是通过使用故事板对您隐藏的。

这种接触是一件好事，因为您将更加熟悉视图、控制器和约束是如何工作的。

现在，有了工具带中的编程布局，所有 UI 代码都将完全公开，并且更容易审查和调试。

# 完整列表

```
class ViewController: UIViewController { var loginButton: UIButton!
  var nameTextField: UITextField!
  var passwordTextField: UITextField! override func viewDidLoad() {
    super.viewDidLoad()
    view.backgroundColor = .white loginButton = UIButton(type: .system)
    loginButton.setTitle("Login", for: .normal)
    loginButton.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(loginButton)
    loginButton.addTarget(self, action: #selector(handleLoginTouchUpInside), for: .touchUpInside) nameTextField = UITextField(frame: .zero)
    nameTextField.placeholder = "Login Name"
    nameTextField.borderStyle = .roundedRect
    nameTextField.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(nameTextField) passwordTextField = UITextField(frame: .zero)
    passwordTextField.placeholder = "Password"
    passwordTextField.isSecureTextEntry = true
    passwordTextField.borderStyle = .roundedRect
    passwordTextField.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(passwordTextField) constraintsInit()
  } func constraintsInit() {
    NSLayoutConstraint.activate([
      loginButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
      loginButton.centerYAnchor.constraint(equalTo: view.centerYAnchor), passwordTextField.bottomAnchor.constraint(equalTo: loginButton.topAnchor, constant: -20),
      passwordTextField.leadingAnchor.constraint(equalTo: view.readableContentGuide.leadingAnchor, constant: 20),
      passwordTextField.trailingAnchor.constraint(equalTo: view.readableContentGuide.trailingAnchor, constant: -20), nameTextField.bottomAnchor.constraint(equalTo: passwordTextField.topAnchor, constant: -20),
      nameTextField.leadingAnchor.constraint(equalTo: view.readableContentGuide.leadingAnchor, constant: 20),
      nameTextField.trailingAnchor.constraint(equalTo: view.readableContentGuide.trailingAnchor, constant: -20)
    ])
  } @objc func handleLoginTouchUpInside() {
    print("Login has been tapped")
    if nameTextField.isFirstResponder {
      nameTextField.resignFirstResponder()
    }
    if passwordTextField.isFirstResponder {
      passwordTextField.resignFirstResponder()
    }
  }
}
```