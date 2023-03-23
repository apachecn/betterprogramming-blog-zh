# 对于 React 本地开发人员来说足够快

> 原文：<https://betterprogramming.pub/just-enough-swift-for-react-native-developers-aeef8bc79010>

## Xcode 和 Swift 的核心概念

![](img/c4260466d8a326b33193fdc57d58b269.png)

由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/reactjs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

那么，你是一个 React 本地开发人员。这意味着你生活在 JavaScript 中。这意味着你不太喜欢 Objective-C。这意味着你爱斯威夫特。

你在这里是因为你想用 Swift 在 React Native 里写原生代码。

但是你对这个新世界没有信心。“var”和“let”关键字看起来很熟悉，但工作方式并不熟悉。感叹号是什么？什么是问号？哇，双问号？参数前加下划线？什么是弱关键词？什么是懒？

我很懒，所以我不想学那些。我就是想学够 Swift 给 React Native 写点原生代码。那么，让我们开始…

首先，Swift 中的“var”是“let”，“let”是“const”。所以你不能这么做。

```
let k = 1
k = 2   // Cannot assign to value: 'k' is a 'let' constant
```

你需要用“var”代替“let”，基本上。

# **类别和功能**

有人说 Swift 是功能性的，有人说是面向对象的，有人说是多范式的。可能最后一个是真的，但是作为一个 JavaScript 开发人员，它看起来像面向对象，因为你将为 iOS 创建本地组件，感觉就像你在编写类并从中创建实例。您创建类似于其他 OO 语言的类，但是创建没有“new”关键字的实例。构造函数方法不是类名，而是所有类的“init”。我们也可以重载构造函数，如果类有一个超类就覆盖它。

```
**class** MyAwesomeClass {
  var k: Int = 0
  init(){
    self.k = 1
  }
  init(newK: Int){
    self.k = newK
  }
}
let a = MyAwesomeClass()
print(a.k) // prints 1
let b= MyAwesomeClass(newK: 12)
print(b.k) // prints 12
```

这是很明显的。不明显的是，为什么我需要将标签“newK”放在参数本身之前。那就是 Swift 3+。您必须将参数名与值一起使用。所以看看下面的代码:

```
**func** sum(a: Int, b: Int) -> Int {
  **return** a+b
}**let** c = sum(3,5)      // Missing argument labels 'a:b:' in calllet d = sum(a:3, b:5) // This will work
print(c)              // prints 8
```

我确信在某个地方，一旦我使用了类似`sum(3,4)`的功能，它就工作了。是的，它有时会起作用——但是什么时候呢？

```
**func** sum(_ a: Int, _ b: Int) -> Int {
  **return** a+b
}
```

*仅当参数名前使用下划线时，*同上。现在使用`sum(3,5)`的函数调用可以工作了。所以我们也揭示了 Swift 的一个奥秘:下划线。以下划线开头的参数名将被忽略，因此可以在没有参数名的情况下调用它们。

顺便说一下“→ Int”在某些语言中不是箭头函数或 lambda。这是函数的返回类型。对于 void 不要使用任何东西。

Swift 还支持可变数量的参数。例如，要编写一个像“sum(3，5，6，8)”这样的函数，你不需要重载很多函数。查看下面的示例:

```
func sum(_ nums: Int...) -> Int {
    var total = 0
    nums.forEach { (val) in
        total += val
    }
    return total
}

let t = sum(1,2,3,4,5,6,7)
print(t)
```

有时在参数名之前，你会看到另一个参数名。起初你可能会认为这是一个有特殊用途的保留词。然后你看到它很多不同的词，所以你认为它不是一个保留词。你会意识到这叫做参数标签。以下示例中的“to”是一个参数标签:

```
**class** Building {
  **var** color: String = "red"
  **func** changeColor(to newColor:String){
    **self**.color = newColor
  }
}**var** building = Building()
print(building.color)              // prints red
building.changeColor(to: "yellow")
print(building.color)              // prints yellow
```

当你调用函数时，你使用“to”作为变量名，但是在函数内部，“to”变成了“newColor”。说实话，我觉得这个没啥用。这看起来像是从 Objective-C 中采用的一个不必要的特性。它应该使函数调用更容易被人理解。我不得不说这听起来确实像“把颜色改成黄色”。相当可读…但那是给人类看的，我是个开发者！我为什么需要这样的东西？这让我很困惑。但这只是我的看法。

# **选项**

Swift 中最引人注目的可能是选件。有两种类型的选项。感叹号和问号。

为了让变量中的值为零，您需要将它们定义为可选变量。问号选项更安全，但需要在访问它们之前解开。感叹号选项可以赋值为 nil，但是当你访问它们的时候，你需要确保它们不为 nil，否则你的应用会崩溃。

下面的代码片段可以编译，但是在运行时会崩溃。因为选项已经被展开了，所以只有当你知道它们有值的时候才可以安全的使用它们。

```
**var** number: Int! = **nil**var result = number * 2// error: Execution was interrupted, reason: EXC_BAD_INSTRUCTION (code=EXC_I386_INVOP, subcode=0x0). 
```

但是这段代码甚至无法编译:

```
**var** number: Int? = **nil**print(number*2)// Value of optional type 'Int?' must be unwrapped to a value of type 'Int'
```

Xcode 会给你提示。使用“？”强制展开或合并。?"

![](img/42afa973db36ce0382683f471f2b9942.png)

如果您强制 unwrap，它将编译，但在运行时崩溃，因为它没有一个值。首先确保它有一个值，然后打开它。像这样:

```
**var** number: Int? = **nil** number= 2print(number*2) // 4
```

另一种选择是使用“？?"。这是 JavaScript 开发人员非常熟悉的。它与“||”非常相似。以下片段几乎完全相同:

```
// JavaScript code below
let number = undefined
const newNum = number || 0// Swift Code below
let number: Int? = nil
let newNum = number ?? 0
```

## **可选链接**

如果你在 JavaScript 中使用过 babel，那么你很有可能使用了“tc39/proposal-optional-chaining ”,它很有可能在未来成为 JavaScript 的标准。Swift 已经内置了这一功能。

看看下面的例子。这可能会导致 Xcode 给出一些(可忽略的)警告，但完全没问题。它会编译，不会抛出任何运行时错误。

```
class City {
    var name = "BigCity"       
}class Town{
    var name = "BigTown"
    var city: City?   
}let town = Town()print(town.city?.name) // Ignored
town.city = City()
print(town.city?.name) // Prints BigCity
```

# **目标-C**

我知道你不喜欢 Objective-C，但是在 React Native 中这是无法避免的。然而，你*可以*最小化它。我说的不是 AppDelegate.m 文件—在大多数情况下，您甚至不需要接触它。我说的是你自己的原生代码。

你不需要用 Objective-C 写完整的类或函数，你可以用 Swift 全部写出来。但是，您需要用 Objective-C 将它们导出到 JavaScript。因此，您可以将它们视为接口。在 Swift 中，你只需要将它们标记为可导出到 Objective-C，并从那里导出到 JavaScript。您可以通过使用“@objc”属性来标记可导出的函数或类——这不是 React 本机特定的属性:任何同时使用 Swift 和 Objective-C 的 Xcode 项目都可以使用它。

有两种不同的本机模块。其中之一是 UI 组件，我们在其中实现了从 UIView 派生的可视化组件。另一种是我们所说的无头组件，它没有 UI，但可以执行播放音频、管理通知、处理 GPS 等任务。

## **UI 原生模块**

假设我们想要实现一个地图(Google maps，MapBox，TomTom 等)SDK。我们想在地图上添加标记，当它准备好时，我们在本机端调用一个直接/命令函数。大概是这样的:

```
/// JavaScript
import { NativeModules} from 'react-native'
const locateUser = () => {
  NativeModules.MapViewController.locateUser([0,0])}<Map
    markers={[{lat: 100, lng:100}, {lat: -50, lng:50}]}
    onMapReady={ this.locateUser }
/>
```

通常我们需要这些组件:

**视图控制器:**(或视图管理器)从 RCTViewManager 派生的类，标记为“@objc”。使用 RCT 外部模块导出视图控制器。

**视图:**由 UIView 派生的类，由视图控制器中的 View()函数返回。视图未被导出，因为它们已经随控制器一起提供。

**查看属性:**变量或函数(用 objc 标记)。这些是从 JavaScript 发出的 React 道具。函数意味着它是一个回调函数，并且它们的类型总是 RCTDirectEventBlock。如果它是一个变量，它可以是一个简单的对象(如字符串或整数)，数组(NSArray)或 JSON 对象(NSDictionary)。

**直接方法:**以声明方式对道具工作做出反应。这意味着您不能直接调用它们。你改变道具，它就会触发反应采取行动。有时您可能希望直接调用本机函数。您使用 RCT 外部方法来导出这些方法。看看下面的 locateUser 函数:

```
**// Objective-C** @interface RCT_EXTERN_MODULE(MapViewController, RCTViewManager) RCT_EXPORT_VIEW_PROPERTY(mapMarkers, NSArray)
  RCT_EXPORT_VIEW_PROPERTY(onMapReady, RCTDirectEventBlock)
  RCT_EXTERN_METHOD(locateUser:(nonnull NSArray *)location resolve:(RCTPromiseResolveBlock)resolve reject:(RCTPromiseRejectBlock)reject)@end**// Swift**// View Controller
[@objc](http://twitter.com/objc)(MyViewController)
class MapViewController: RCTViewManager { [@objc](http://twitter.com/objc) func locateUser(_ location:NSArray, resolve:RCTPromiseResolveBlock, reject:RCTPromiseRejectBlock){

    } override func view() -> UIView? {
      let mapView = MapView()
      return mapView }}//Viewclass GenericMap: UIView{
    @objc var mapMarkers: NSArray = [] }
```

## **无头本机模块**

我不确定它们是否被称为无头本机模块，我指的是非 UI 本机模块。它们通常不是 JSX 模块。它们是你可以直接调用的。所以它们的使用和定义与 UI 模块中的“locateUser”方法完全一样。他们使用 RCT 外部模块导出模块，使用 RCT 外部方法导出方法。无头本机模块的一个例子是音频管理器或通知管理器。它们是使用“react-native”库的 NativeModules 对象调用的。

因为您直接调用函数，并且它们在本机执行，所以您希望这是异步完成的。所以我们需要使用回调或承诺。如果你看看“locateUser”函数有 2 个 React(如果你没注意到的话，RCT 是 React 的前缀)对象 RCTPromiseResolveBlock 和 RCTPromiseRejectBlock。它们必须是这些函数的最后两个参数，按照这个顺序。因此，当您完成本机执行时，您将调用这些函数中的一个来触发它的 JavaScript 对应物。

这只是 React 本地开发者进入 Swift 世界的一小步。在我对原生模块感到相对自信之前，这里介绍的概念是我必须学习的主要概念。我仍在学习 Swift，并使用 Android 和 IOS 的原生模块实现 React 原生库。但是当我使用 Xcode 和 Swift 时，我不觉得我不知道我在做什么，因为我已经学会了一些核心概念。我们开发人员可以在途中解决剩下的问题！

快乐学习。