# 在 Swift 中以编程方式执行有效的自动布局

> 原文：<https://betterprogramming.pub/effective-auto-layout-programmatically-in-swift-2e68d1b2f546>

## 以编程方式布局您的视图

![](img/7a045b0d9a1e3c0a53d72f5c5005d69e.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/rocket?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

界面构建器(IB)从任何人能回忆起[【1】](https://en.wikipedia.org/wiki/Interface_Builder)的时候就已经在 Xcode 了。它引发了持续至今的关于用代码还是用 IB 开发用户界面的争论。不过随着 SwiftUI 的发布，苹果已经将程序化布局作为主流方式。

为什么以前没有出现这种情况？民间传说告诉我们，用代码构建 UI 要比用界面构建器慢。在本文中，我将展示当使用有效的编程式自动布局的五种技术时，不仅相反，而且编程式布局更具可伸缩性、可维护性和面向未来性。

*这不是一篇入门文章。为了熟悉题目，我推荐* [*苹果的自动布局指南*](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1) *。*

# 以编程方式自动布局与故事板

让我们来看看关于界面构建器的一些事实:

*   界面构建器的进入门槛很低。
*   变化是可见的。
*   要全面了解 UI 组件，您需要检查界面构建器文件和源代码文件。一个 UI 组件有两个真实的来源会降低可读性，并为错误留下更多的空间。
*   通过 IB 配置一个组件有太多的方法:运行时属性，不同设置的多个选项卡，特定大小级别的覆盖，等等。这使得很难发现哪些设置被覆盖。
*   解决故事板和 xib 中的合并冲突容易出错，因为我们必须处理可读性差的 XML。此外，在修复合并冲突时，接口生成器文件可能会无声地中断。您可能不会注意到丢失的`IBAction`连接，直到您触发那个动作。
*   在复杂的场景中，当您需要对布局进行更多控制时(例如，屏幕旋转、每个屏幕大小或平台的变化、动画、动态字体)，您将需要求助于编程约束甚至手动帧计算。

关于程序布局的一些事实:

*   代码是 UI 组件真实性的单一来源。
*   视图设置是明确的。很清楚配置了哪些属性以及如何配置。
*   合并冲突更容易解决，因为我们处理的是代码而不是 XML。
*   可扩展复杂场景:屏幕旋转、动画、动态字体等。
*   通过编程构建 UI 的一致的[编码风格](https://www.vadimbulavin.com/swift-code-style/)在一个混合的 UIKit 和 SwiftUI 项目中得到维护。随着 SwiftUI 的逐渐采用，这变得越来越重要。
*   代码库中的噪音更少:没有 segues、字符串标识符、来自 storyboard 和 xib 的复杂初始化，或者许多帮助扩展试图隐藏噪音的一部分。

从这些事实，我们可以得出结论，民间传说是错误的。代码布局比故事板和 xib 更快，更经得起未来的考验。将一些视图放在一块画布上似乎又快又容易。然而，从长远来看，界面构建器的所有缺点累积起来，使它成为一个次优的选择。

为了使编程布局在编写速度和可维护性方面更加有效，我们将使用有效编程自动布局的五种技术:

1.  自动布局 DSL。
2.  专注的独立组件。
3.  回调作为一种沟通模式。
4.  自动布局约束上的堆栈视图。
5.  SwiftUI 预览。

# 自动布局 DSL

当您以编程方式构建 UI 已经有一段时间了，您会注意到有些组合会随着时间的推移而重复。为了避免样板代码，我建议设计一个 DSL，在优秀的[布局锚点 API](https://developer.apple.com/documentation/uikit/nslayoutanchor) 之上提供一个薄薄的抽象层。

*我考虑热门库像*[*snap kit*](https://github.com/SnapKit/SnapKit)*和* [*制图学*](https://github.com/robb/Cartography) *次优选择。我从本·桑多夫斯基* *那里学到的一条有价值的建议是，只对你领域之外的问题使用库。*

例如，这是我在项目中使用的 DSL。认为它是信息性的，而不是指令性的。我将简要强调关键点，以便我们可以看到它们的好处。

`LayoutAnchor`代表单个约束，可以是常量(如固定宽度或高度)，也可以是相对于另一个约束(如前导或尾随):

```
import UIKit

enum LayoutAnchor {
    case constant(attribute: NSLayoutConstraint.Attribute,
                  relation: NSLayoutConstraint.Relation,
                  constant: CGFloat)

    case relative(attribute: NSLayoutConstraint.Attribute,
                  relation: NSLayoutConstraint.Relation,
                  relatedTo: NSLayoutConstraint.Attribute,
                  multiplier: CGFloat,
                  constant: CGFloat)
}
```

工厂方法简化了锚点定义:

```
extension LayoutAnchor {
    ...
    static let leading = relative(attribute: .leading, relatedBy: .equal, relatedTo: .leading)
    static let trailing = relative(attribute: .trailing, relatedBy: .equal, relatedTo: .trailing)

    static let width = constant(attribute: .width, relatedBy: .equal)
    static let height = constant(attribute: .height, relatedBy: .equal)
    ...
}
```

`UIView`有一个方便的方法，它应用了一组`LayoutAnchor`:

```
extension UIView {
    func addSubview(_ subview: UIView, anchors: [LayoutAnchor]) {
        translatesAutoresizingMaskIntoConstraints = false
        subview.translatesAutoresizingMaskIntoConstraints = false
        addSubview(subview)
        subview.activate(anchors: anchors, relativeTo: self)
    }

    func activate(anchors: [LayoutAnchor], relativeTo: UIView? = nil) {
        let constraints = anchors.map { NSLayoutConstraint(from: self, relativeTo: relativeTo, anchor: $0) }
        NSLayoutConstraint.activate(constraints)
    }
}

extension NSLayoutConstraint {
    convenience init(from: UIView, to item: UIView?, anchor: LayoutAnchor) {
        ...
    }
}
```

下面是我们如何使用 DSL 将几个视图放在一起:

```
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let yellow = UIView()
        yellow.backgroundColor = .yellow
        view.addSubview(yellow, anchors: [.leading(0), .trailing(0), .bottom(0), .top(0)])

        let redBox = UIView()
        redBox.backgroundColor = .red
        view.addSubview(redBox, anchors: [.centerX(0), .centerY(0), .width(100), .height(100)])
    }
}
```

对比标准[锚 API](https://developer.apple.com/documentation/uikit/nslayoutanchor) :

```
class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let yellow = UIView()
        yellow.backgroundColor = .yellow
        yellow.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(yellow)
        NSLayoutConstraint.activate([
            yellow.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            yellow.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            yellow.topAnchor.constraint(equalTo: view.topAnchor),
            yellow.bottomAnchor.constraint(equalTo: view.bottomAnchor),
        ])

        let redBox = UIView()
        redBox.backgroundColor = .red
        redBox.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(redBox)
        NSLayoutConstraint.activate([
            redBox.widthAnchor.constraint(equalToConstant: 100),
            redBox.heightAnchor.constraint(equalToConstant: 100),
            redBox.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            redBox.centerYAnchor.constraint(equalTo: view.centerYAnchor),
        ])
    }
}
```

如果我们算上`viewDidLoad()`方法，那就是 11 对 24 行代码。

# 专注、独立的组件

界面构建器使得重用组件变得困难。要在画布中呈现一个视图，您需要用`@IBDesignable`属性对其进行标记，并通过`prepareForInterfaceBuilder()`支持另一条生命周期路径。即使你设法做到了这一点，故事板也会加载缓慢，视图只会偶尔出现在画布上(这是 Xcode 6 以来的情况，我不相信它会被修复)。

相反，不需要额外的努力来组成编程编写的组件。以下是在考虑可重用性的情况下设计组件的经验法则:

*   争取专注、独立的观点。
*   传递视图呈现所需的最小数据子集。比起视图模型、业务模型对象，更喜欢原始值——你能想到的。
*   不要将视图与协议或基类耦合，因为过早的抽象可能会损害你的设计。这是因为 UI 组件的变化速度不同，原因也不同。

# 回调作为一种沟通模式

使用回调作为视图之间的主要通信模式。从 iOS 14 开始，你已经可以通过系统 API 来做到这一点。例如，下面是如何将回调附加到`UITextField`文本更改:

```
let textField = UITextField()
textField.addAction(
    UIAction { action in
        let textField = action.sender as! UITextField
        print("Text changed \(textField.text)")
    },
    for: .editingChanged
)
```

或者用回调实例化一个`UIButton`:

```
let button = UIButton(primaryAction: UIAction { action in
    print("Tapped!")
})
```

由于我们大多数人都没有支持 iOS 14+的特权，所以实现自定义的基于回调的控件是微不足道的。这里有一个`UIButton`的例子:

```
class CallbackButton: UIButton {
    var onAction: ((CallbackButton) -> Void)?

    init(onAction: ((CallbackButton) -> Void)? = nil) {
        self.onAction = onAction
        super.init(frame: .zero)
        setup()
    }

    required init?(coder: NSCoder) {
        super.init(coder: coder)
        setup()
    }

    private func setup() {
        addTarget(self, action: #selector(onTap), for: .primaryActionTriggered)
    }

    @objc private func onTap() {
        onAction?(self)
    }
}
```

用法类似于系统 API:

```
let button = CallbackButton { btn in
    print("Tapped!")
}
```

感觉差不多是 SwiftUI 吧？

我在这里描述 [SwiftUI 视图通信模式](https://www.vadimbulavin.com/passing-data-between-swiftui-views/)。

# 自动布局约束上的堆栈视图

堆栈视图在自动布局之上提供了一个抽象层，允许我们从原始的容器视图组成复杂的布局。`UIStackView`所做的一切都是在给我们制造约束。

如果你查看苹果的[视图布局](https://developer.apple.com/documentation/uikit/view_layout)概述，第一句话就是:

> 使用堆栈视图自动布局界面视图。当需要精确放置视图时，请使用自动布局。

甚至苹果的[自动布局指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AutoLayoutWithoutConstraints.html#//apple_ref/doc/uid/TP40010853-CH8-SW1)也在约束本身之前引入了堆栈。

换句话说，如果您只能使用堆栈视图，那么就去使用它。

从我的经验来看，栈可以解决自动布局想要解决的超过 50%的问题。与约束相比，基于堆栈的布局构建速度更快，更简洁，可读性更好，也更灵活。此外，堆栈还有其他一些好处:

*   支持不同的内容大小类别是微不足道的。如果不合适，只需翻转内容的轴。
*   [添加滚动](https://useyourloaf.com/blog/scrolling-stack-views/)很琐碎。这对于表单、短列表和集合来说很方便，不会引入`UITableView`或`UICollectionView`的开销。
*   [对齐](https://developer.apple.com/documentation/uikit/uistackview/1616243-alignment)和[分配](https://developer.apple.com/documentation/uikit/uistackview/1616233-distribution) [排列好的子视图](https://developer.apple.com/documentation/uikit/uistackview/1616232-arrangedsubviews)有多种方式，通过约束来实现并不容易。
*   很容易从视图层次结构中隐藏和显示排列好的子视图，而不会破坏约束。

# SwiftUI 预览

当在代码中构建布局时，通常很难验证您所做的更改是否达到了预期的结果。SwiftUI 预览的目标是最大限度地减少您构建、运行和配置视图以验证您所做的变更的时间[【2】](https://developer.apple.com/videos/play/wwdc2019/233/)。

虽然[预览提供者](https://developer.apple.com/documentation/swiftui/previewprovider)只支持`SwiftUI.View`，但是我们可以通过符合`UIView(Controller)Representable`[【3】](https://gist.github.com/mattt/ff6b58af8576c798485b449269d43607)无缝地使`UIView`和`UIViewController`可供预览:

```
import SwiftUI

@available(iOS 13, *)
struct UIViewControllerPreview<ViewController: UIViewController>: UIViewControllerRepresentable {
    let viewController: ViewController

    init(_ builder: @escaping () -> ViewController) {
        viewController = builder()
    }

    func makeUIViewController(context: Context) -> ViewController { viewController }

    func updateUIViewController(_ uiViewController: ViewController, context: Context) {}
}
```

[*UIKit with swift ui*](https://www.vadimbulavin.com/swift-code-style/)*更详细的介绍了这个主题。*

有了上面的内容，我们现在可以让`UIViewController`的任何实例都符合`UIViewControllerRepresentable`，从而可以预览上一个例子中的视图控制器:

```
struct ViewControllerPreviews: PreviewProvider {
    static var previews: some View {
        UIViewControllerPreview {
            let storyboard = UIStoryboard(name: "Main", bundle: nil)
            return storyboard.instantiateViewController(identifier: "ViewController") as! ViewController
        }
        .previewDevice("iPhone SE (2nd generation)")
    }
}
```

# 摘要

让我们总结一下有效的程序化自动布局的五个技巧:

1.  利用自动布局 DSL 裁剪样板文件。本文中的例子[在这里](https://gist.github.com/V8tr/3d28b3468bb60b02c5134d8d6ad78c43)。
2.  设计组件时要考虑重用:(a)需要最小的数据子集，(b)专注于做一件事，而不是通过继承耦合到其他组件。
3.  通过回调在组件之间通信。
4.  使用堆栈视图来简化布局并使其更加灵活。
5.  使用 SwiftUI 预览实现更快的反馈循环。