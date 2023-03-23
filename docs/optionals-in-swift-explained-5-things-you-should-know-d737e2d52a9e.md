# Swift 的期权解释:你应该知道的 5 件事

> 原文：<https://betterprogramming.pub/optionals-in-swift-explained-5-things-you-should-know-d737e2d52a9e>

## 编写干净的代码，同时注意可能的零值

![](img/1d63dc5ff463187b890d582d328c1909.png)

照片由 [Max Nelson](https://unsplash.com/@maxcodes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/swift?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

可选方案是 Swift 的核心，从 Swift 的第一个版本开始就存在了。可选值允许我们编写干净的代码，同时注意可能的零值。

如果您是 Swift 新手，您可能需要习惯向属性添加问号的语法。一旦你习惯了它们，你实际上可以开始从它们中获益，例如，扩展。

# Swift 中的可选值是什么？

在我们深入了解你应该知道的关于期权的一系列事情之前，先了解一些基础知识是有好处的。

属性、方法和下标可以返回一个可选的，这基本上意味着它要么返回一个值(如果它存在的话)，要么返回另一个值`nil`。多个查询可以链接在一起，称为*可选链接*。这是对*强制展开*的一种替代，稍后将对此进行更详细的解释。

下面的代码示例显示了可选的`String`和可选的链接的定义，以打印出字符数。

```
let name: String? = "Antoine van der Lee"
print(name?.count ?? 0)
```

注意:`??`操作符(零合并操作符)将在后面解释。

# 1.Swift 中的强制解包选项

*强制展开*如果可选项存在，则返回该值；如果可选项为`nil`，则触发运行时错误。

但是在我们深入到力展开之前，让我们先来看看在没有力的情况下展开可选的可能性。

## 如何解开可选的？

在 Swift 中有多种方法可以打开可选的。你可以用一句`guard`陈述:

```
let name: String? = "Antoine van der Lee"
guard let unwrappedName = name else {
    return
}
print(unwrappedName.count)
```

或者，您可以使用`if let`语句:

```
let name: String? = "Antoine van der Lee"
if let unwrappedName = name {
    print(unwrappedName.count)
}
```

或者，您可以使用双问号运算符，也称为 nil 合并运算符。这将返回可选值(如果存在)或默认值(在本例中定义为零):

```
let name: String? = "Antoine van der Lee"
print(name?.count ?? 0)
```

## 使用感叹号(！idspnonenote)强制打开可选的。)

可以直接在可选值后使用感叹号(`!`)强制展开可选值。

```
var name: String? = "Antoine van der Lee"
print(name!.count)
```

每当上述示例中的 name 变量被设置为`nil`时，都会导致如下致命的运行时错误:

```
Fatal error: Unexpectedly found nil while unwrapping an Optional value
```

## 解包可以连锁

可选链接可以这样完成:

```
struct BlogPost {
    let title: String?
}

let post: BlogPost? = BlogPost(title: "Learning everything about optionals")
print(post?.title?.count ?? 0)
```

强制解包选项的计数相同:

```
let post: BlogPost? = BlogPost(title: "Learning everything about optionals")
print(post!.title!.count)
```

但是要注意，如果你只打开最后一个可选的，你仍然会得到一个可选的。

在下面的例子中，我们只打开了标题，而没有打开文章。这意味着如果帖子是`nil`的话，我们仍然得不到标题:

```
let post: BlogPost? = BlogPost(title: "Learning everything about optionals")
print(post?.title!.count) // Prints: Optional(35)
```

## 选项作为最佳实践，强制展开以捕捉编程错误

最佳实践是在默认情况下使用选项，如果不需要就不要使用感叹号。有些人甚至建议启用[强制展开 SwiftLint 规则](https://github.com/realm/SwiftLint/blob/master/Source/SwiftLintFramework/Rules/Idiomatic/ForceUnwrappingRule.swift)。这将防止您引入许多意外的崩溃。

然而，如果一个值是`nil`，当它是一个编程错误时，有时使用强制展开也是好的。因此，您可以通过强制解包并在早期捕获 bug 来帮助自己进行调试。

# 2.可选的是两种情况的枚举

很高兴知道可选的基本上是两种情况的列举:

```
enum Optional<Wrapped> {
    /// The absence of a value.
    case none

    /// The presence of a value, stored as `Wrapped`.
    case some(Wrapped)
}
```

然而，您可以使用`nil`来表示缺少值，而不是使用`.none`案例。

记住这一点，您也可以使用 enum 将上面的 name 变量定义为可选变量:

```
let name = Optional.some("Antoine van der Lee")
print(name!.count)
```

或者，您可以像使用普通枚举一样切换可选的:

```
func printName(_ name: String?) {
    switch name {
    case .some(let unwrappedValue):
        print("Name is \(unwrappedValue)")
    case .none:
        print("Name is nil")
    }
}

printName(nil) // Prints: "Name is nil"
printName("Antoine van der Lee") // Prints: "Name is Antoine van der Lee"
```

并且查看它的[文档](https://developer.apple.com/documentation/swift/optional)，你可以看到一个可选的附带了一些方便的方法。一个很好的例子是`map`方法:

```
let sideLength: Int? = Int("20")
let possibleSquare = sideLength.map { $0 * $0 }
print(possibleSquare) // Prints: "Optional(400)"
```

或者是`flatMap`方法，在这种情况下，只有通过了至少包含五个字符的验证，才会返回名称:

```
var name: String? = "Antoine van der Lee"
let validName = name.flatMap { name -> String? in
    guard name.count > 5 else { return nil }
    return name
}
print(validName) // Prints: "Optional("Antoine van der Lee")"
```

## 扩展选项

现在你知道一个可选的被定义为一个枚举，你可以猜测你也可以为它写扩展！

最常见的例子是扩展一个可选的`String`并处理一个空值:

```
extension Optional where Wrapped == String {
    var orEmpty: String {
        return self ?? ""
    }
}

var name: String? = "Antoine van der Lee"
print(name.orEmpty) // Prints: "Antoine van der Lee"
name = nil
print(name.orEmpty) // Prints: ""
```

# 3.编写可选的单元测试

当你写测试的时候，有一个很好的方法来处理选项而不需要强制解包。如果您使用强制解包，您将冒导致致命错误的风险，这将阻止您的所有测试成功。

您可以使用`XCTUnwrap`，如果可选选项不包含值，它将抛出一个错误:

```
func testBlogPostTitle() throws {
    let blogPost: BlogPost? = fetchSampleBlogPost()
    let unwrappedTitle = try XCTUnwrap(blogPost?.title, "Title should be set")
    XCTAssertEqual(unwrappedTitle, "Learning everything about optionals")
}
```

# 4.任择议定书方法

如果您有使用 Objective-C 的经验，您可能会错过可选协议方法。

尽管在 Swift 中有更好的方法来编写可选协议方法，但标准库中最常见的方法是这样的:

```
protocol UITableViewDataSource : NSObjectProtocol {

    optional func numberOfSections(in tableView: UITableView) -> Int

    // ...
}
```

这允许您使用问号来调用方法:

```
let tableView = UITableView()
let numberOfSections = tableView.dataSource?.numberOfSections?(in: tableView)
```

# 5.嵌套期权是一种东西

尽管 [SE-0230 —展平“尝试”产生的嵌套选项](https://github.com/apple/swift-evolution/blob/master/proposals/0230-flatten-optional-try.md)去掉了嵌套可选的一个最常见的原因，它还是一个东西！

```
var name: String?? = "Antoine van der Lee"
print(name!!.count)
```

你已经解开了一个可选的，它仍然返回一个可选的。在早期的 Swift 版本中，当您使用`try?`操作符时，通常就是这种情况。

一个常见的例子是当您使用包含可选值的词典时:

```
let nameAndAges: [String:Int?] = ["Antoine van der Lee": 28]
let antoinesAge = nameAndAges["Antoine van der Lee"]
print(antoinesAge) // Prints: "Optional(Optional(28))"
print(antoinesAge!) // Prints: "Optional(28)"
print(antoinesAge!!) // Prints: "28"
```

你可以看到，它基本上只需要你使用一个额外的感叹号或问号。

# 结论

就是这样！我们介绍了在 Swift 中使用选件时您需要了解的许多事情。从使用感叹号(`!!`)展开可选元素的基础到扩展`Optional`枚举的更高级实现。