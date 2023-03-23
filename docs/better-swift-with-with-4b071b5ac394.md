# 用 With()编写更好的 Swift 代码

> 原文：<https://betterprogramming.pub/better-swift-with-with-4b071b5ac394>

## 将类似 Kotlin 的功能模式引入您的 Swift 工具包

![](img/d9aa34c83f7eed0c43ed6055e7f5a793.png)

帕特里克·沃德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们都写过需要一些函数来创建和返回一个配置好的对象的代码。

```
func makeButton(_ title: String?) -> UIButton {
    let button = UIButton()
    button.translatesAutoresizingMaskIntoConstraints = false
    button.titleLabel?.text = title
    button.titleLabel?.font = .headline
    button.setTitleColor(.red, for: .normal)
    return button
}
```

模式是一样的。用我们需要的东西创建一个变量。配置它。然后返回变量。

但是上面代码中的`button`变量和返回语句很大程度上是样板文件，在每一行中指定`button`实际上只是视觉噪音，因为它倾向于隐藏代码实际设置的实际配置选项。

我们能做得更好吗？

# 科特林来救援了。

Kotlin 在语言中有一个`with`构造，可以让你用 lambdas 做以下事情...

```
return with(Obj()) {
   objMethod1()
   objMethod2()
   this
}
```

注意，最后一行中的`this`语句是完成我们想要的任务所必需的，否则，lambda 将返回最后一个执行的语句返回的任何结果，在本例中是`objMethod2`。

所有这些都很酷。而且干净。唯一的问题是，我们不在 Swift 编译器团队中，我们不能简单地到处给 Swift 添加新的语句类型。

# 使用()

我们不能添加自己的语句，但是我们*可以*添加自己的函数。

如果我们定义一个全局函数，它接受我们想要配置的对象，用配置闭包修改它，然后返回结果，会怎么样？

这样的函数会让我们做这样的事情…

```
func makeButton(_ title: String?) -> UIButton {
    with(UIButton()) {
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.titleLabel?.text = title
        $0.titleLabel?.font = .headline
        $0.setTitleColor(.red, for: .normal)
    }
}
```

这相当接近，尽管我们仍然需要未命名的参数`$0`来让 Swift 高兴。

我们可以将参数命名如下，但是出于某种原因，我发现未命名的参数版本更容易阅读。你自己看吧。

```
func makeButton(_ title: String?) -> UIButton {
    with(UIButton()) { b in
        b.translatesAutoresizingMaskIntoConstraints = false
        b.titleLabel?.text = title
        b.titleLabel?.font = .headline
        b.setTitleColor(.red, for: .normal)
    }
}
```

使用按钮名称作为前缀，您必须在思想上将点左侧的文本与点右侧的文本分开，而对于未命名的参数，我似乎在思想上“忽略”了`$0`，这反过来使方法和函数文本名称更加突出。

无论哪种方式，我认为配置块都很突出，并且更容易看到和理解对象本身实际上设置了什么值。

# 该功能

`with()`全局函数本身足够简单。

```
@discardableResult
@inlinable
func with<V>(_ value: V, _ mutate: ((_ v: inout V) -> Void)) -> V {
    var mutableValue = value
    mutate(&mutableValue)
    return mutableValue
}
```

我们接受一个通用值作为参数，将其传递给我们的配置函数，然后返回最终结果，这样用户就可以根据需要分配或返回它。

这个变体使用了一个`inout`参数，所以它同样适用于结构。如果您只打算在类中使用它，那么编写自己的性能更好的版本就足够简单了。

```
@discardableResult
@inlinable
public func With<T:AnyObject>(_ value: T, _ mutate: ((_ v: T) -> Void)) -> T {
    mutate(value)
    return value
}
```

在这里，我将`class`版本大写，以便与基于值的版本区分开来。如果你只是想简单地使用`with`来配置一个现有的对象，那么这个函数的结果也是可以丢弃的。

```
with(myButtonOutlet) {
   $0.titleLabel?.text = title
   $0.titleLabel?.font = .headline
   $0.setTitleColor(.red, for: .normal)
}
```

同样，如果这不是您计划使用的模式，您可以放弃它。

# 作为函数参数内联

您已经看到它被用作函数返回类型，但是当您需要创建并修改函数参数时,`with`也很有用。

```
stackView.addArrangedSubview(with(UIButton()) {
    $0.translatesAutoresizingMaskIntoConstraints = false
    $0.titleLabel?.text = "My Button Title"
    $0.titleLabel?.font = .headline
    $0.setTitleColor(.red, for: .normal)
})
```

同样，不需要创建临时变量，这样就可以在将它作为参数传递之前修改它。

```
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.titleLabel?.text = title
button.titleLabel?.font = .headline
button.setTitleColor(.red, for: .normal)
stackView.addArrangedSubview(button)
```

# 不可变的值

另一个用例是将一个完全配置的值赋给一个字母，这样它就不可改变了。

```
let user = with(User.mock) {
    $0.addPhoneNumber("303-555-8686")
}
```

# 主题变奏曲

前面我提到过为对象编写自己的版本，但事实证明，直通配置函数模式在其他地方也很方便。

考虑以下返回 UITableView 中的单元格的代码。(这些行很长，为了清楚起见，我换成了 gists。)

在这里，我们为`UITableView`添加了一个扩展，使得正确类型的单元出列、适当地配置它们并在需要时返回它们变得容易。不需要临时变量或难看的类型转换来将出队类型转换为正确的配置类型。

这里唯一需要注意的是，单元格必须在单元格配置函数中显式类型化为`MyTableViewCell`。它满足扩展函数本身所需的一般约束。

如下所述。

请注意，如果您的单元格不是传递的类型，您将得到一个未配置的单元格。但是，如果您手动将单元格出队并有条件地将`UITableViewCell`强制转换为特定的单元格类型，您也会遇到同样的问题，在我看来这基本上是一种浪费。

不管怎样效果都一样。

# 建设者

我还发现 with 模式在 [Builder](https://github.com/hmlongco/Builder) 中很有帮助，这是我正在开发的基于 UIKit 的 SwiftUI 的替代方案，用于我还没有向有问题的视图添加特定修饰符的情况。

```
Label("Some text")
    .font(.headline)
    .color(.red)
    .with {
        $0.isUserInteractionEnabled = true
    }
```

# 完成块

如前所述，passthrough 配置函数模式是随手可得的一点代码。

今天就到这里吧。如果你觉得这有用，或者有其他很酷的用例，请告诉我。