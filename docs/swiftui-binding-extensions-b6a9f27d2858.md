# SwiftUI 绑定扩展

> 原文：<https://betterprogramming.pub/swiftui-binding-extensions-b6a9f27d2858>

## 使 SwiftUI 视图和预览中的绑定像 ABC 一样简单

![](img/f9d0f1cca649a630e9e04ee83e4daf0c.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/easy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

以下是一些常见的 SwiftUI 问题，属于“必须有更好的方法”一类。

# 可选值

让我们从期权开始。由于 API 需求，在我们的代码中有可选值是很常见的，但是在 SwiftUI 中使用它们可能有点痛苦。考虑一下。

```
class SomeViewModel: ObservableObject {
    @Published var name: String?
}

struct SomeView: View {
    @StateObject var viewModel = SomeViewModel()
    var body: some View{
        TextField("Name", text: $viewModel.name) // does not compile
    }
}
```

这里我们有一个带有可选名称字符串的视图模型，我们希望能够编辑该值。不幸的是，TextField 绑定没有可选的初始化器，Swift 编译器会给我们一个错误。*"无法转换类型'绑定<字符串的值？>'到预期参数类型'绑定<字符串>'。*

怎么办？我的意思是，如果它是一个`Text`视图，我们只需使用一个 nil 合并操作符来为所讨论的字符串提供一个默认值。

但是我们如何为绑定提供默认值呢？

# 绑定扩展

根据文章标题，您可能已经猜到了部分解决方案。是的，我们将创建绑定的扩展。

这是代码。

```
extension Binding {
    public func defaultValue<T>(_ value: T) -> Binding<T> where Value == Optional<T> {
        Binding<T> {
            wrappedValue ?? value
        } set: {
            wrappedValue = $0
        }
    }
}
```

现在，有了这些，我们就可以做了。

```
struct SomeView: View {
    @StateObject var viewModel = SomeViewModel()
    var body: some View{
        TextField("Name", text: $viewModel.name.defaultValue(""))
    }
}
```

我们需要做的就是给我们的字符串一个默认值，然后我们就可以开始了。如果存在，扩展将提供 name 的值，如果不存在，则提供默认值。无论哪种方式，它看起来都像是对文本字段的非可选绑定，所以我们的代码现在像预期的那样工作。

由于我们的`defaultValue`扩展是通用的，它可以处理字符串、整数、布尔…我们需要的任何东西。

# 空字符串

如果我们处理大量的字符串，如果我们总是将它们默认为空，我们可以借用 RxSwift 在 *it* 需要进行可选文本绑定时使用的概念。

让我们再增加一个节目。

```
extension Binding where Value == Optional<String> {
    public var orEmpty: Binding<String> {
        Binding<String> {
            wrappedValue ?? ""
        } set: {
            wrappedValue = $0
        }
    }
}
```

这允许…

```
struct SomeView: View {
    @StateObject var viewModel = SomeViewModel()
    var body: some View{
        TextField("Name", text: $viewModel.name.orEmpty)
    }
}
```

和`defaultValue("")`一样，只是要写的代码少一点。

# SwiftUI 预览中的绑定

让我们继续，看看下面令人惊讶的复杂 SwiftUI 视图。

```
struct AmazinglyComplexView: View {

    @Binding var value: Bool

    var body: some View {
        Toggle(isOn: $value) {
            Text("Toggle Me")
        }
        .padding()
    }
}
```

这里我们有一个带有`Toggle`的视图，它绑定到某个值。现在我们想预览一下我们的视图。简单对吗？

```
struct AmazinglyComplexView_Previews: PreviewProvider {
    static var previews: some View {
        AmazinglyComplexView(value: .constant(true))
    }
}
```

因为我们有一个绑定，所以我们遵循 SwiftUI 约定，并将`.constant(true)`传递给视图的绑定参数。一旦我们这样做了，我们看到了我们的观点预览。

![](img/8d167d01b89fa7251fe7edadfd20bca1.png)

样本预览

然而，只有一个问题。在 Xcode 14 中，预览现在是活动的…但是我们不能切换我们的视图并看到我们的开关改变，因为我们为我们的绑定传入了一个常量值。按下开关没有任何作用。

怎么办？

# 查看包装

嗯，如果你检查一下 StackOverflow，你会看到普遍的共识是用另一个定义了`@State`变量的*视图来包装我们的视图，然后将那个*值的绑定传递给我们的视图。

你知道，我们首先想要测试的观点。看起来是这样的。

```
struct AmazinglyComplexView_Previews: PreviewProvider {
    struct Wrapper: View {
        @State var value: Bool
        var body: some View {
            AmazinglyComplexView(value: $value)
        }
    }
    static var previews: some View {
        Wrapper(value: true)
    }
}
```

事实上，这实际上是苹果在 [WWDC 2020 开发者视频](https://developer.apple.com/wwdc20/10149)中推荐的方法。

无论哪种方式，结果都是一样的，我们的预览是有效的。

但是……真的吗？每当我们想要预览一个包含绑定的视图时，我们都必须经历这种繁琐的过程吗？

# 仿制药来拯救！

我想，我们可以展示一下我们的 SwiftUI-fun，并编写一些通用视图。将我们的预览视图包装在一个`ViewBuilder`中，并创建一个适当类型的状态对象。也许类似于…

```
struct StateWrapper<Value, Content:View>: View {
    @State var value: Value
    let content: (_ value: Binding<Value>) -> Content
    init(value: Value, @ViewBuilder content: @escaping (_ value: Binding<Value>) -> Content) {
        _value = .init(initialValue: value)
        self.content = content
    }
    var body: some View {
        content($value)
    }
}

struct AmazinglyComplexView_Previews: PreviewProvider {
    static var previews: some View {
        StateWrapper(value: true) {
            AmazinglyComplexView(value: $0)
        }
    }
}
```

答对了。我们有另一个解决方案。算是吧。我的意思是，至少现在我们不必每次想用单个绑定测试某个视图时都创建一个包装器…

哦。见鬼。

如果我们需要用两个绑定测试一个视图呢？还是三个？我们开始用一个、两个、三个和四个绑定来编写 StateWrappers 吗？

应该有更简单的方法。

## 可变的

SwiftUI 非常非常接近于提供我们所需要的`.constant`。

但是你可能知道，只有在马蹄铁、手榴弹和你的除臭剂不起作用的时候，亲密才算数。记住这一点，请允许我介绍`.variable`。

将以下扩展添加到您的代码中。

```
extension Binding {
    public static func variable(_ value: Value) -> Binding<Value> {
        var state = value
        return Binding<Value> {
            state
        } set: {
            state = $0
        }
    }
}
```

这里我们创建一个扩展函数，它将初始值赋给一个内部状态变量。绑定的 get 和 set 闭包捕获该状态变量，并根据需要对其进行变异。

就这样。现在，当您想在预览中测试您的视图时，只需这样做。

```
struct AmazinglyComplexView_Previews: PreviewProvider {
    static var previews: some View {
        AmazinglyComplexView(value: .variable(true))
    }
}
```

现在我们可以随心所欲地切换视图的`Toggle`。

![](img/72a734d9ad9699c5a72953f64fe22fa7.png)

切换 2 预览

更好的是，有了`.variable`，我们可以根据需要随时使用它们。

*请注意，这项技术并不完美。最大的缺点是它不会触发一个完整的视图刷新，所以依赖于我们的 toggle 值的任何其他视图都不会更新。*

我知道，对吧？无论如何，variable 仍然是一个有用的扩展，当你做原型的时候，如果需要的话，你总是可以使用`StateWrapper`。

# 更多手榴弹

鉴于`.variable`在呈现视图时的上述缺点，我为需要多达四个不同绑定的视图添加了一些额外的通用绑定包装器。

```
struct Bindings<Value, Content:View>: View {
    @State var value: Value
    let content: (_ value: Binding<Value>) -> Content
    init(value: Value, @ViewBuilder content: @escaping (_ value: Binding<Value>) -> Content) {
        _value = .init(initialValue: value)
        self.content = content
    }
    var body: some View {
        content($value)
    }
}

struct Bindings2<V0, V1, Content:View>: View {
    @State var v0: V0
    @State var v1: V1
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>) -> Content
    init(_ v0: V0, _ v1: V1, @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        self.content = content
    }
    var body: some View {
        content($v0, $v1)
    }
}

struct Bindings3<V0, V1, V2, Content: View>: View {
    @State var v0: V0
    @State var v1: V1
    @State var v2: V2
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>) -> Content
    init(_ v0: V0, _ v1: V1, _ v2: V2,
         @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        _v2 = .init(initialValue: v2)
        self.content = content
    }
    var body: some View {
        content($v0, $v1, $v2)
    }
}

struct Bindings4<V0, V1, V2, V3, Content: View>: View {
    @State var v0: V0
    @State var v1: V1
    @State var v2: V2
    @State var v3: V3
    let content: (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>, _ v3: Binding<V3>) -> Content
    init(_ v0: V0, _ v1: V1, _ v2: V2, _ v3: V3,
         @ViewBuilder content: @escaping (_ v0: Binding<V0>, _ v1: Binding<V1>, _ v2: Binding<V2>, _ v3: Binding<V3>) -> Content) {
        _v0 = .init(initialValue: v0)
        _v1 = .init(initialValue: v1)
        _v2 = .init(initialValue: v2)
        _v3 = .init(initialValue: v3)
        self.content = content
    }
    var body: some View {
        content($v0, $v1, $v2, $v3)
    }
}
```

挑一个你需要的。

```
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        Bindings2(true, "Mike") {
            AnotherComplexView(value: $0, name: $1)
        }
    }
}
```

如果你要传递超过四个不同的绑定…你可能要考虑重构一些代码。

随便说说。

# 完成块

所以你有它。今天没有什么惊天动地的事情，只是我们在代码中经常遇到的一些常见的 SwiftUI 绑定问题，以及一些我最喜欢的绑定扩展和视图包装器，它们让我们可以精简它们。

有自己喜欢的吗？请在下面的评论中告诉我。

下次见。

*本文是*[*SwiftUI 系列*](https://michaellong.medium.com/the-swiftui-series-abc180690a9d) *的一部分。*