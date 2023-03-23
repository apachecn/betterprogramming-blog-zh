# 很少使用的酷炫 Swift 功能

> 原文：<https://betterprogramming.pub/cool-swift-features-that-rarely-used-ea8eee870f16>

## 回顾 10 种 Swift 语言结构

![](img/7f6583f8af529242b7ce885ebf9da15f.png)

照片由[micha Kubalczyk](https://unsplash.com/@rev3n?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Swift 是最强大的编程语言之一。同时也相对容易学。了解 Objective-C 之后，我可以在不到一周的时间内切换到它。混合了经典的 C 风格指令如`if`和`for`，现代概念如 ARC 和扩展以及强大的框架，使它成为初学者和专业人士的最佳编程语言之一。

这种简单性产生了一个问题:许多开发人员在开始编写应用程序时，不再深入研究语言特性。我在 2014 年用 Swift 编写了我的第一个应用程序，但我一直在学习 Swift 的新*隐藏*功能。在本文中，我想回顾一下开发人员很少在他们的第一个应用中使用的 10 种 Swift 语言结构。

# 推迟

如果你的函数很混乱，有很多返回或者抛出，使用了非托管资源或者其他最终需要清理的代码，那么`defer`是你的一个很好的选择。

```
openResource()

defer { freeResource() }

// Do whatever you like. Don't free the resource
```

看上面的代码。一开始我们打开一个资源。它可以是文件、数据库或网络套接字。我们知道，在这个函数结束时，需要释放资源，否则会导致内存泄漏或其他问题。

打开资源后立即调用`defer`。反初始化代码应该在`defer`闭包里面。然后使用您的文件、数据库或套接字，而不必担心释放它。无论您有多少条 return 语句，您都可以确保您的非托管资源将被正确地...管理。

# 惰性变量

惰性变量在被请求时被初始化。这是一个很棒的句法糖，它把这个:

```
private var _innerVariable: SomeComplexType?

var visibleVariable: SomeComplexType {
  get() {
    if _innerVariable == nil {
      _innerVariable = SomeComplexType()
    }
    return _innerVariable!
  }
}
```

变成这样:

```
lazy var visibleVariable = SomeComplexType()
```

当你需要一个变量在某一点单独初始化时，这很有用。同时，您希望它只初始化一次，而不是每次都重新创建。

例如，以下代码可用于创建一个 UI 元素:

```
lazy var titleLabel: UILabel = {
  let label = UILabel()
  label.text = "Screen title"
  return label
}()

func createLayout() {
  addSubview(titleLabel)
}
```

# 警戒控制语句

`guard`语句实际上在 Swift 中很常用，但我想在这里提到它，因为它对于所说的编程语言是独一无二的。从另一种语言如 C++、Python 甚至 Objective-C 切换过来后，你可能会觉得很奇怪。

一般来说，`guard`类似于`if`结构，但是逻辑相反。主要区别在于，`guard`应该总是以流终止语句(`return`、`throw`、`break`)结束...).

最常见的用例是获取非可选变量。假设我们正在编写一个使用可选类属性的方法。但是这个方法只有在属性不是`nil`的时候才有意义。

诀窍是:

```
guard let nonOptional = optionalVariable else {
  // Show a message
  return
}

// Use nonOptional
```

类似于`if`语句，`guard`可能有几个用逗号分隔的条件，删除可选性并不是它唯一能做的事情。例如，这是另一个有效的用例:

```
guard let field1 = field1,
  !field1.isEmpty,
  let field2 = field2,
  !field2.isEmpty
else {
  return
}
```

正如你在上面的例子中看到的，`guard`语句可以重定义常量和变量，使它们成为非可选的。

# 协议扩展

一些像 C++这样的老语言有一个有趣的特性叫做*多重继承*。这意味着一个类可以有两个或更多的基类。例如，如果类`A`具有函数`a()`，类`B`具有函数`b()`，则扩展`A`和`B`的类`C`将同时具有函数`a()`和`b()`。这同样适用于类变量、常量和其他所有东西。

这个特性确实非常强大，但是它产生了如此多的问题和不确定性，以至于 it 专家将它列入了不良实践的列表。比如后藤。

所有现代语言都只允许从一个基类(或超类)继承，并在 Swift 术语中添加了接口或协议。协议的缺点是它们不能拥有自己的功能。或者……他们能吗？

一个协议真的不能包含任何数据。这是不可避免的。但是您可以添加一个函数，该函数对继承该协议的所有类都可用。

假设您正在编写一个`File`类，它提供了对文件系统的访问。而且我们有一个函数`open(access: Access)`。`Access`定义请求的文件访问权限。

我们要`ReadableFile`和`WritableFile`。`ReadableFile`会有一个功能`open()`，调用`open(access: .read)`。这将是一个类似的功能`WritableFile`。

现在我们需要一个既可读又可写的文件。这就是多重继承发挥作用的地方。但是斯威夫特没有。我们必须使用协议:

`MyFile`从上面的例子现在可以访问所有的功能:`get() -> String`、`readText() -> String`、`set(text: String)`、`write(text: String)`等。只要被调用的函数在同一个协议或基础协议中声明，这些函数就可以相互调用。

如果没有定义任何函数(没有代码)，则必须在采用该协议的类中定义它。

# 异步函数

异步函数存在于许多语言中，但直到 5.5 版才出现在 Swift 中。你只能从 iOS 13，iPadOS 13，macOS 10.15，tvOS 13，watchOS 6 开始使用它们。如果你的应用需要在 iOS 12 或 macOS 10.14 中运行，你将无法使用并发。

外观:

```
func asyncTask() async throws -> Result {
  ...
}

Task {
  do {
    let result = await asyncTask()
    print(result)
  } catch {
    print(error.localizedDescription)
  }
}
```

工作原理:

`Task`是创建异步上下文的结构。只能从上下文中调用异步函数(`Task`闭包或另一个异步函数)。

异步函数应该用关键字`async`标记。关键字`throws`表示该功能可能会失败。一个标准的 Swift 函数应该是这样的:

```
func task(completion: @escaping (result: Result?, error: Error?) -> Void) {
  ...
}
```

要调用一个异步函数，你需要使用`await`关键字。

你为什么要用它？这使得代码更容易阅读和测试。我们有一个包装在`do-catch`块和`Task`结构中的易读代码，而不是带有选项、可能的保留周期和其他混乱的完成块。

Swift 中的并发很容易实现，但是有一个重要的时刻。异步函数在单独的线程中工作。这意味着你不能更新用户界面，导航，显示弹出窗口等。

要做到这一点，你需要切换到`MainActor`。有三种方法:

```
await MainActor.run {
  // Update UI
}@MainActor func iWillRunInMainThread() {
  // This function will run in UI thread
}Task { @MainActor in
  // Functions here will run on UI thread
}
```

您可以根据自己的情况选择最合适的方式。

# 区间匹配

区间或范围可以是封闭的或开放的。开放区间不包括边缘值，而封闭区间包括整个范围。

例如:

`let range1 = 1...5 // 1, 2, 3, 4, 5`

`let range2 = 1..<5 // 1, 2, 3, 4`

*注意:旧版本的 Swift 有单独的间隔数据类型。后来它们就成了一个共同的类型——*`*Range*`*。*

Swift 允许在`switch` - `case`结构中使用音程。例如:

```
switch age {
  case 0..<18:
    print("You're minor")

  case 18...:
    print("You're adult")

  default:
    print("You weren't born yet")
}
```

间隔可以相互重叠。它们也可以在同一个`switch`中与简单值一起使用。它是这样工作的:

*   一条`switch`语句计算提供的值，或者从内存中读取变量值；
*   从上到下检查`switch`的值是否与`case`的值匹配；
*   如果它与所述值不匹配，它进行到下一个`case`直到它结束。如果没有匹配，它执行一个`default`块；
*   如果匹配，它执行代码块并终止`switch`的执行，除非使用了`fallthrough`关键字；
*   如果使用`fallthrough`,则执行匹配块后的下一个`case`块。然后它不再做任何匹配。

如果需要几个值，而不是一个区间，可以用逗号来表示:

```
case 1, 5, 10:
    // ...
```

# 关联值

Swift 的一个独特或至少不常见的特性是 *enum 关联值*。每个枚举事例都可以包含数据。

让我们看一个例子。我们希望允许用户选择性别。如果用户没有表明自己是女性还是男性，我们希望允许他们输入另一个性别。同时，我们希望保持枚举的魅力:

```
enum Gender {
  case female
  case male
  case other(gender: String, pronounce: String)
}
```

我们可以这样定义性别:

```
let gender = Gender.other(gender: "Non-binary", pronounce: "them")
```

然后我们可以在`case`块中提取这些值:

```
switch gender {
case .female:
  print("Say 'hi' to her")

case .male:
  print("Say 'hi' to him")

case let .other(_, pronounce):
  print("Say 'hi' to \(pronounce)")
}
```

如果你不需要处理所有的情况，你可以使用一个`if`语句:

```
if case let .other(genderName, _) = gender {
  print(genderName)
}
```

# 链接

链接是我最喜欢的现代编程语言的特性之一。当您处理选项时，链接尤其有用，这在 Swift 中非常常见。

让我们看一个例子——我们在一个名为`items`的可选变量中有一个项目列表。每个项目都有一个可选的 id。我们需要向服务器发送一个 id 列表。

```
var items: [Item]? = ...
var ids = [Int]()
if let items = items {
    for item in items {
      if let itemId = item.id {
        ids.append(itemId)
      }
    }
}
```

通过链接，上述示例可以变成一行代码:

```
var items: [Item]? = ...
let ids = items?.compactMap { $0.id } ?? []
```

这已经是很大的简化了。现在假设我们只有一个 id，第一个非空 id。

```
var items: [Item]? = ...
var id: Int?
if let items = items {
    for item in items {
      if let itemId = item.id {
          id = itemId
          break
      }
    }
}
```

我们可以简化这一点:

```
var items: [Item]? = ...
let id = items?.compactMap { $0.id }.first
```

当您处理更复杂的结构时，链接的真正威力是显而易见的。假设`Item`结构有另一个结构字段——`description`，它有一个字段`color`。

如果您需要获得颜色列表，例如，为了收集统计数据，您可以这样做:

```
var items: [Item]? = ...
let colors = items?.compactMap { $0.description }.compactMap { $0.color } ?? []
```

或者:

```
var items: [Item]? = ...
let colors = items?.compactMap { $0.description?.color } ?? []
```

除了`compactMap`，还可以使用`forEach`、`map`、`filter`等功能。如果左侧可选，需要在`.`前加`?`。

# 弱变量

弱变量允许避免保留周期。*保留周期*是两个对象相互强引用时的状态。这迫使一个对象总是留在内存中，即使它不再被使用。

一个典型的例子是视图控制器和演示者。或者视图控制器和视图模型。他们需要互相传递信息。最简单的实现是保持彼此之间的链接。当你关闭应用程序屏幕时，它不需要将它们保存在内存中，但 ARC(自动引用计数器)只有在没有其他对象对其进行强引用时才会释放一个对象。

让我们看一个例子:

```
class MyController: UIViewController {
  var viewModel: MyViewModel?

    // ...
}

class MyViewModel: NSObject {
  var controller: MyController?

  // ...
}
```

使用协议会更好，但是它不能解决保留周期问题，所以为了节省时间，我们在这里不使用它们。

初始化期间，`viewModel`和`controller`将开始相互参照。当 iOS 看到`MyController`不在 dissolve 中时，它会尝试释放它，但是`MyViewModel`仍然引用它。同样适用于`MyViewModel` - `MyController`正在引用它。怎么修？

```
class MyController: UIViewController {
  var viewModel: MyViewModel?

    // ...
}

class MyViewModel: NSObject {
  weak var controller: MyController?

  // ...
}
```

与默认的强引用不同，弱引用不包含对象。相反，当对象被释放时，它们变为`nil`。在上面的示例中，这将在解散后发生:

*   iOS 将检查是否有对`MyController`的强引用
*   没有强有力的引用，唯一的引用是弱的
*   iOS 发布了一个`MyController`的实例。`MyViewModel`实例的`controller`变量会自动设置为`nil`
*   自从`MyController`发布后，就不会再有强引用`MyViewModel`
*   `MyViewModel`的实例也被释放

使用故事板或 xib 时，`weak`变量的另一个例子是`@IBOutlet`引用。

在我们切换到下一个主题之前，还有一点需要注意——有时我们使用完成处理程序执行异步操作。例如:

```
func loadData() {
  network.load { data, error in
    if let error = error {
      self.show(error)
    } else {
      self.show(data)
    }
  }
}
```

在这个例子中，闭包有一个对调用类的强引用。如果它是`UIViewController`的实例，即使用户返回，它也会把它留在内存中，直到接收到数据。但是如果用户关闭屏幕，我们不需要显示任何内容。那么，为什么我们要把它保存在内存中并处理结果呢？

`weak`关键字将解决这个问题:

```
func loadData() {
  network.load { [weak self] data, error in
    if let error = error {
      self?.show(error)
    } else {
      self?.show(data)
    }
  }
}
```

如果你多次提到`self`，你可以使用`guard`语句:

```
func loadData() {
  network.load { [weak self] data, error in
    guard let self = self else { return }
    if let error = error {
      self.show(error)
    } else {
      self.show(data)
    }
  }
}
```

另一种避免保留周期的方法是使用`unowned`关键字。它类似于`weak`，但是它假设无主对象将在内存中。如果引用的实例被释放，可能会导致崩溃。

# 财产观察员

另一个现代特征是房产观察者。在更老的编程语言中它是不存在的，所以很多从其他语言转到 Swift 的开发者不知道如何使用它。

假设我们有一个具有属性的类:

```
class MyClass {
  var prop: Int = 0

  func propChanged() {
  }
}
```

我们想知道`prop`是什么时候被更改的，并采取一些措施。例如，如果我们有一个包含红色、绿色和蓝色组件的颜色结构，当其中一个组件改变时，我们需要重新绘制它。

显而易见的方法是，每当我们设置一个`prop`时就调用`propChanged()`。但这是一种快速获取 bug 的方法。您可能会忘记打电话，或者其他开发人员可能甚至不知道这是必要。

另一种方法是将`prop`和`propChanged`设为私有，并添加一个函数:

```
func changeProp(_ newValue: Int) {
  prop = newValue
  propChanged()
}
```

这是可行的，但是代码太多了。你可能有几十处房产。你不允许得到这个值，因为它是私有的。

正确的方法是:

```
class MyClass {
  var prop: Int = 0 {
    didSet {
      propChanged()
    }
  }
}
```

`didSet`是一名物业观察员。这是一个常规的 Swift 函数，可以使用`prop`的新值。每次值改变时都会调用它，所以你不用再担心它了。

只有两个观察者:

*   在设置新值之前调用`willSet`。`prop`仍有旧值。你可以用它来释放旧的资源，为新的价值做准备。
*   设置新值后调用`didSet`。

## 属性包装

属性包装器允许在设置或获取数据时对数据进行所有可能的操作。一个典型的例子是设置数据范围，比如我们需要限制一个从`1`到`5`的值。这是一个典型的评级案例。如果允许的选项是给出 1 到 5 颗星，6 将是错误的。如果你从后端得到它，你可以假设这是一个计算错误或四舍五入。

```
@propertyWrapper
struct Rating {
  private var number = 1

  var wrappedValue: Int {
    get { return number }
    set { number = max(min(newValue, 5), 1) }
  }
}

struct User {
  @Rating var rating: Int
}

var user = User()
user.rating = 10

print(user.rating)
// 5 is printed
```

在某些情况下，显示错误可能是更好的解决方案，但是在很多情况下，当您获取或设置变量的值时，数据处理可能是一个好主意。

# 摘要

我确信，如果您是一名经验丰富的 Swift 开发人员，您会了解其中的大部分功能，如果不是全部的话。但是如果你是 Swift 新手，或者你没有深入研究文档或阅读其他人的代码，你可能不知道其中的一些，因为它们没有在其他编程语言中使用。

我希望它对你有用。编码快乐，下次见！

# 第二部分

在我发表这篇文章后，我得到了一些关于缺失功能的评论。下面是第 2 部分，描述更高级、甚至更少使用的 Swift 功能:

[https://foxi corn . medium . com/cool-swift-features-that-less-used-part-2-92 bb 52 e8a 715](https://foxicorn.medium.com/cool-swift-features-that-rarely-used-part-2-92bb52e8a715)