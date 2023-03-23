# SwiftUI 管理的视图

> 原文：<https://betterprogramming.pub/swiftui-managed-views-506e2d74368f>

## 如何克服环境、环境对象和其他属性包装的一些限制。

![](img/301eff1310ee20d5e798d81db7d195f3.png)

照片由 [charlesdeluvio](https://unsplash.com/@charlesdeluvio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/manager?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我之前写过一篇关于如何编写“可测试的”视图的文章。在那里，我展示了一些无需创建完整的视图模型就可以将逻辑和其他代码从视图体中分离出来的方法。

但是 SwiftUI 的`Environment`和`EnvironmentObject`属性包装器会使这种方法变得具有挑战性并且难以完成。在这篇文章中，我将说明这是为什么，然后演示几个解决问题的方法。

让我们先看看我们想要完成什么。

# 可测试的视图

基本上，编写可测试的视图意味着将嵌入了条件业务逻辑的代码和格式如下…

```
struct OrderDetailsListView: View {
    var order: Order
    var body: some View {
        Form {
            ForEach(order.items) { item in
                 HStack {
                     if item.quantity == 1 {
                         Text(item.name)
                     } else {
                         Text("\(item.name) $(\(item.quantity, specifier: "%.2f") @ $\(item.price, specifier: "%.2f")")
                     }
                     Spacer()
                     Text("$(\(item.total, specifier: "%.2f")")
                 }
            }
        }
    }
}
```

变成类似这样的代码...

```
struct OrderDetailsListView: View {
    let order: Order
    var body: some View {
       Form {
           ForEach(order.items) { item in
               OrderDetailsRowView(item: item)
           }
       }
    }
}struct OrderDetailsRowView: View {
    var item: OrderItem
    var body: some View {
        HStack {
            Text(itemDescription)
            Spacer()
            Text(itemTotal)
        }
    }
    var itemDescription: String {
        if item.quantity == 1 {
            return item.name
        } else {
            return "\(item.name) (\(item.formattedQuantity) @ \(item.formattedPrice))"
        }
    }
    var itemTotal: String {
        item.formattedTotal
    }
}
```

是的，这里有更多的代码，但是请注意列表视图和细节视图的视图体现在是多么的简单。

```
 var body: some View {
       Form {
           ForEach(order.items) { item in
               OrderDetailsRowView(item: item)
           }
       }
    } var body: some View {
        HStack {
            Text(itemDescription)
            Spacer()
            Text(itemTotal)
        }
    }
```

如果他们访问的项目是正确的，那么根据定义，视图本身也必须是正确的。实际上没什么会出错的。

因此，通过将项目描述和项目总代码分解成单独的、不同的、可见的变量，现在也可以实例化视图并**测试**视图的逻辑。

```
func testOrderDetailsRowView() { let view1 = OrderDetailsRowView(item: OrderItem.mock1)
    XCTAssert(view1.itemDescription == "Soft Drink")
    XCTAssert(view1.itemTotal == "$1.99") let view2 = OrderDetailsRowView(item: OrderItem.mock2)
    XCTAssert(view2.itemDescription == "Cheeseburger (2 @ $4.99)")
    XCTAssert(view2.itemTotal == "$9.98")}
```

如前所述，这种事情通常是通过向视图添加一个视图模型，然后测试它来完成的。但是添加一个单独的视图模型来管理这两种情况是多余的，完全没有必要。

# 环境变量

注意，`OrderDetailsRowView`视图很容易创建和测试，因为它是用一个简单的结构作为参数初始化的。

但是，当我想从其他地方获取信息时，会发生什么呢？比如说，环境？考虑:

```
struct AccountsMenuItem: View {
    @Environment(\.userAccounts) private var accounts
    var body: some View {
        if accounts.count == 0 {
            NavigationMenuItem("Create Account!", icon: "person") {
                NewAccountView()
            }
        } else {
            NavigationMenuItem(manageName, icon: imageName) {
                ManageAccountsView()
            }
         }
    }
    var manageName: String {
        accounts.count > 1 ? "Manage Accounts" : "Manage Account"
    }
    var imageName: String {
        accounts.count > 1 ? "person.2" : "person"
    }
}
```

如果我没有帐户，这个来自设置屏幕的视图将返回一个不同的导航项目来创建一个帐户，或者如果我有，返回一个项目来管理我的帐户。此外，显示的名称和图标都根据帐户数量而变化。

我可以很容易地在 Xcode 中预览`AccountsMenuItem`视图，通过`.environment`修改器提供不同的值。

```
struct AccountsMenuItem_Previews: PreviewProvider {
    static var previews: some View {
        Group {
            AccountsMenuItem()
                .environment(\.userAccounts, [])
            AccountsMenuItem()
                .environment(\.userAccounts, Account.mockAccounts)
        }
    }
}
```

从预告片中，我可以相当肯定地说这是正确的。

但是如果我想写和我最初的例子一样的单元测试呢？如果我想确保我的视图的逻辑在现在和将来都是正确的，也许是在别人改变了它之后，该怎么办？

这就是问题开始出现的地方。即使没有标记为 private，我们也不能用参数实例化视图，因为`Environment`属性包装器没有接受新默认值的初始化器。

```
let view = AccountsMenuItem(accounts: []) // FAILS
```

我们也不能使用`.environment(key, value)`属性。在 SwiftUI 之外不存在`environment`栈，对我们的视图应用修饰符的结果会导致我们的视图被包装在一个`ModifiedContent`结构中。我们再也看不到测试它们的变量了。

我们也不能把它丢回`AccountsMenuItem`。同样修改过的内容包装器也妨碍了这一点。

所以。我们被困住了吗？

# 环境诡计

如果您有自己的环境变量，那么您可以通过创建自己的`EnvironmentKey`和默认值来实现。标准代码看起来像这样。

```
struct AccountsEnvironmentKey: EnvironmentKey {
    static let defaultValue: [Account] = []
}extension EnvironmentValues {
    var userAccounts: [Account] {
        get { self[AccountsEnvironmentKey.self] }
        set { self[AccountsEnvironmentKey.self] = newValue }
    }
}
```

解决我们问题的“诀窍”是如下定义我们的密钥:

```
struct AccountsEnvironmentKey: EnvironmentKey {
    static var defaultValue: [Account] = []
}
```

我们所做的只是将`defaultValue`从`let`改为`var`。一旦我们做到了这一点，我们就可以这样做…

```
func testSingleAccount() throws {
    AccountsEnvironmentKey.defaultValue = [Account.mockSavings]
    let view1 = AccountsMenuItem()
    XCTAssert(view1.manageName == "Manage Account")
    XCTAssert(view1.imageName == "person")
}func testMultipleAccounts() throws {
    AccountsEnvironmentKey.defaultValue = Account.mockAccounts
    let view2 = AccountsMenuItem()
    XCTAssert(view2.manageName == "Manage Accounts")
    XCTAssert(view2.imageName == "person.2")
}
```

当以这种方式创建时，`AccountsMenuItem`默认(抱歉)使用我们之前设置的`defaultValue`。

我们现在可以嘲笑我们的观点并检验它们。

# 啐

如果你现在觉得“恶心”，我倾向于同意你的观点，但请记住几件事。

1.  苹果把`@Environment`标记为 final 或者没有提供一个能够接受新默认值的初始化器不是我的错。
2.  我们只在测试期间接触到`AccountsEnvironmentKey`。希望，任何在主应用程序中这样做的人在代码审查期间都会受到惩罚。
3.  另一个解决方案来了。

但至少现在你知道，如果需要的话，解决方案是可能的。

# 环境对象

那么环境对象呢？我们能在那里做同样的事情吗？

遗憾的是，我们不能(简短讨论，即)。

```
struct AccountsMenuItem: View {
    @EnvironmentObject var manager: AccountManager
    var body: some View {
        if isEmpty {
            NavigationMenuItem("Create Account!", icon: "person") {
                NewAccountView()
            }
        } else {
            NavigationMenuItem(manageName, icon: imageName) {
                ManageAccountsView()
            }
         }
    }
    var isEmpty: Bool {
        manager.accounts.isEmpty
    }
    var hasMultipleAccounts: Bool {
        manager.accounts.count > 1
    }
    var manageName: String {
        hasMultipleAccounts ? "Manage Accounts" : "Manage Account"
    }
    var imageName: String {
        hasMultipleAccounts ? "person.2" : "person"
    }
}
```

像`Environment`一样，`EnvironmentObject`属性包装器没有能够接受新默认值的初始化器；当我们测试时，我们没有环境；再次使用`environmentObject`修饰符将视图紧密地包围在`ModifiedContent`结构中。

那我们该怎么办？

# 托管视图

这里的解决方案是创建我称之为“受管理的”视图。为此，我们进一步将视图组合成两个独立的视图。一种方法是从环境中提取所需的数据，然后将数据传递给实际显示数据的视图。

外部视图是经理。内部视图是托管的。

这是我们的视图管理器。同样，它的工作很简单。从环境中提取值，并将其传递给托管视图。

```
struct AccountsMenuItem: View {
    @EnvironmentObject private var manager: AccountManager
    var body: some View {
        ManagedAccountsMenuItem(accounts: manager.accounts)
    }
}
```

这是托管视图。

```
struct ManagedAccountsMenuItem: View {
    var accounts: [Account]
    var body: some View {
        if accounts.count == 0 {
            NavigationMenuItem("Create Account!", icon: "person") {
                NewAccountView()
            }
        } else {
            NavigationMenuItem(manageName, icon: imageName) {
                ManageAccountsView()
            }
         }
    }
    var manageName: String {
        accounts.count > 1 ? "Manage Accounts" : "Manage Account"
    }
    var imageName: String {
        accounts.count > 1 ? "person.2" : "person"
    }
}
```

注意，就像我们的第一个例子一样，`ManagedAccountsMenuItem`现在采用了一个简单的值类型，很容易用我们希望的任何值进行实例化。

现在我们使用*那个*视图进行测试。

```
func testSingleAccount() throws {
    let accounts: [Account] = []
    let view1 = ManagedAccountsMenuItem(accounts: accounts)
    XCTAssert(view1.manageName == "Manage Account")
    XCTAssert(view1.imageName == "person")
}
func testMultipleAccounts() throws {
    let accounts = Account.mockAccounts
    let view2 = ManagedAccountsMenuItem(accounts: accounts)
    XCTAssert(view2.manageName == "Manage Accounts")
    XCTAssert(view2.imageName == "person.2")
}
```

实际上，在管理器中没有什么可以测试的。它无法控制是否存在。它在那里只是为了交接。

我们甚至可以使用相同的技巧来改进我们最初的、有点令人讨厌的解决方案。

```
struct AccountsMenuItem: View {
    @Environment(\.userAccounts) private var accounts
    var body: some View {
        ManagedAccountsMenuItem(accounts: accounts)
    }
}
```

如您所见，我们在这里使用了与之前相同的`ManagedAccountsMenuItem`视图。该视图不关心账户数据来自哪里，它只显示它被告知要显示的内容。

# 查看模型

“但是，迈克尔”，你可能会问，“为什么不简单地使用一个视图模型，并完成它。”

你可以。但是有一些反对这样做的理由；

1.  我们现在需要创建和管理一个相对重量级的`ObservableObject`，并将其分配给视图。结构比堆上分配的对象更有性能。
2.  我们现在必须弄清楚如何用我们可能需要的不同类型的数据构建**视图模型**的原型。我已经在另一篇文章中展示了如何做到这一点，但是这并不像看起来那么简单。
3.  我们当前的设计拥有来自 SwiftUI 环境的数据。要使用视图模型，我们需要弄清楚如何将数据从视图的环境或环境对象中获取到视图模型中。不用说，[那里也有问题](/swiftui-were-loading-we-re-loading-7d689fa8b0c7)。
4.  或者，我们需要完全跳过环境，自己想办法管理数据。话说回来，这可能不是一个选择。

几乎所有上述情况都会导致额外的复杂情况和需要解决的问题。以我们写更多的代码结束。

# SwiftUI 属性包装

SwiftUI 提供了用于管理核心数据的属性包装器，或者允许您从其他来源获取应用程序和用户信息。Google 为 Realm 提供了方便的包装器，还有很多其他的。

但是如果您使用它们，您可能会遇到我在这里使用 SwiftUI 的环境属性包装器时遇到的许多相同的困难。

幸运的是，你现在知道一个解决方案。

使用所有这些属性包装器会导致其他架构问题。但这可能是另一篇文章，改天。

# 体系结构

在软件架构中有一个老笑话，关于任何问题都可以通过简单地添加另一层来解决。

同样的情况也经常发生在 SwiftUI 中。只有在这里我可以说，几乎任何问题都可以通过将一个视图分解成更小的、组合良好的视图来解决。

苹果公司一直在重复这个话题，T2 也是，我也是。SwiftUI 视图不是视图，它们是轻量级的视图*定义*，在创建和使用它们的过程中几乎没有性能损失。

动手吧。

# 完成块

今天到此为止。

我认为 SwiftUI 开发社区正在慢慢完善创建成功的 SwiftUI 应用程序所需的一些核心原则和设计模式。

这是其中之一。

一如既往，如果你喜欢你所看到的，请按住“喜欢”按钮，并在下面留下你的问题或评论。