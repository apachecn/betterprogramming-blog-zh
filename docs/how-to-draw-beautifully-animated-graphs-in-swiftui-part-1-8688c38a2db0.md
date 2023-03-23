# 如何在 SwiftUI 中绘制精美的动画图形—第 1 部分

> 原文：<https://betterprogramming.pub/how-to-draw-beautifully-animated-graphs-in-swiftui-part-1-8688c38a2db0>

## 首先，创建主界面

![](img/ca56290da0dcd68fba62878b36c34929.png)

这是一系列三个片段中的第一个，它将教你在 [SwiftUI](https://developer.apple.com/xcode/swiftui/) 中创建这个美丽的动画图形所需知道的一切。

我们在第一部分要做的是:

*   创建标题为`Picker`的主界面；
*   创建一个单条形图，组成我们的图表；
*   创建要显示的初始数据集。

# 入门指南

首先，为 iOS 创建一个新的 [Xcode](https://developer.apple.com/xcode/) 项目。您将立即看到一个自动生成的`ContentView`，显示超著名的“`Hello World`”

现在，让我们创建我们的图表将代表的数据集。我们将使用一个简单的字典，根据分段的`Picker`中包含的单词进行索引。在`ContentView`结构中，声明这些变量:

```
private var data: [String: [Int]] = [
     "Data1": [28, 25, 30, 29, 23],
     "Data2": [3, 1, 2, 4, 3],
     "Data3": [2, 6, 8, 3, 4]
]
```

如你所见，我使用的是`Int` s，但你也可以使用任何你喜欢的其他类型，如`Double`或`Float`。

此外，我们需要声明一个`@State`变量，每当您选择不同的选项时，该变量将由选取器自己进行更改。

```
@State private var dataPicker: String = "Data1"
```

接下来，我们可以开始构建我们的主 UI。我们需要一个简单的标题和一个`Picker`，垂直堆叠。

```
VStack { Text("Let's Graph!")
        .foregroundColor(.white)
        .font(.largeTitle)
        .fontWeight(.bold)
        .padding() Picker("", selection: $dataPicker) {
        Text("Data1").tag("Data1")
        Text("Data2").tag("Data2")
        Text("Data3").tag("Data3")
    }.pickerStyle(SegmentedPickerStyle())
    .padding()}
```

上面的代码将以大而粗的白色字符显示我们的标题，并在其周围创建一些填充。

拾取器代码将显示一个`SegmentedPicker`，带有三个标记为`“Data1”`、`“Data2”`和`“Data3”`的选项。每当你点击一个时，`$dataPicker`变量将被改变为相应的`tag`值。

# 创建图表

![](img/59ab7122b8a358edcad34e8877cf4f6c.png)

我们现在准备创建一个单独的条形图来组成我们的大图！

使用 SwiftUI Capsule 视图创建此栏。特别是，我们希望显示分配给条形的值、条形本身及其标签。

我们需要一个`VStack`来显示值，一个`ZStack`来叠加背景`Capsule`和值`Capsule`。

让我们创建一个新结构来创建这个新组件:

```
struct CapsuleBar: View {
    var value: Int
    var maxValue: Int
    var width: CGFloat
    var valueName: String
    var capsuleColor: ColorRGB var body: some View {
        VStack {

            Text("\(value)")
            ZStack(alignment: .bottom) {
                Capsule()
                    .fill(Color.gray)
                    .opacity(0.1)
                    .frame(width: width, height: 400) Capsule()
                    .fill(
                        Color(.sRGB, red: capsuleColor.red, green: capsuleColor.green, blue: capsuleColor.blue)
                    )
                    .frame(width: width, height: CGFloat(value) / CGFloat(maxValue) * 400)
                    .animation(.easeOut(duration: 0.5))
            }

            Text("\(valueName)") }
    }
}
```

这里使用了五个变量:

*   `value`:条绘制的实际值
*   `maxValue`:要显示的数据数组中的最大值(注意:为了在数组中提供完全不同的值，`maxValue`必须始终为≤ `value`)
*   `width`:T4 的边框宽度
*   `valueName`:在`Capsule`下面的标签里会写什么
*   `capsuleColor`:控制`Capsule`的颜色

我已经应用到`Capsule`的动画修改器将会在它改变值的时候激活这个条。

`ColorRGB`结构看起来像这样

```
struct ColorRGB {
    var red: Double
    var green: Double
    var blue: Double
}
```

要预览这个新的`CapsuleBar`，我们唯一需要做的就是在`ContentView_Previews` 结构中编写这段代码:

```
CapsuleBar(value: 20, maxValue: 50, width: 50, valueName: "Val Text", capsuleColor: ColorRGB(red: 44 / 255, green: 54 / 255, blue: 79 / 255))
```

你现在应该看到`CapsuleBar`了！干得好。

在 Part2 中看到你创造了`CapsuleGraphView`！