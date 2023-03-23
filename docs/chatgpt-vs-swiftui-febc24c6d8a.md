# ChatGPT 做 SwiftUI

> 原文：<https://betterprogramming.pub/chatgpt-vs-swiftui-febc24c6d8a>

## 我并不担心，但是…

![](img/6945b06c59eb81047943af6eb30a9b96.png)

安德烈·德·森蒂斯峰在 [Unsplash](https://unsplash.com/s/photos/AI-programmer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

ChatGPT 是 OpenAI 的一个演示项目，可以讨论很多话题。包括编程。

我们来测试一下，好吗？

# ChatGPT 对话

从这一点开始，接下来的一切就是我的输入提示，然后是 ChatCGP 的响应，以及完整的示例代码。

> 嘿，ChatGPT，你能做一个 SwiftUI 输入表单，包含姓名、地址、城市、州和邮政编码字段吗？

当然可以！下面是 SwiftUI 中一个简单的表单示例，其中包含姓名、地址、城市、州和邮政编码字段:

```
import SwiftUI

struct EntryForm: View {
    @State private var name: String = ""
    @State private var address: String = ""
    @State private var city: String = ""
    @State private var state: String = ""
    @State private var zipCode: String = ""

    var body: some View {
        Form {
            TextField("Name", text: $name)
            TextField("Address", text: $address)
            TextField("City", text: $city)
            TextField("State", text: $state)
            TextField("Zip code", text: $zipCode)
        }
    }
}
```

在这个例子中，`EntryForm`结构符合`View`协议，并定义了一个返回`Form`视图的`body`属性。`Form`视图包含每个表单字段的`TextField`视图，`@State`属性包装器用于管理每个字段的状态。

> 能否将字段移动到视图模型中？

是的，为了更好地管理表单的状态和逻辑，您可以将表单字段移动到单独的视图模型中。下面是一个可能的例子:

```
import SwiftUI

struct EntryForm: View {
    @ObservedObject var viewModel: EntryFormViewModel

    var body: some View {
        Form {
            TextField("Name", text: $viewModel.name)
            TextField("Address", text: $viewModel.address)
            TextField("City", text: $viewModel.city)
            TextField("State", text: $viewModel.state)
            TextField("Zip code", text: $viewModel.zipCode)
        }
    }
}

class EntryFormViewModel: ObservableObject {
    @Published var name: String = ""
    @Published var address: String = ""
    @Published var city: String = ""
    @Published var state: String = ""
    @Published var zipCode: String = ""
}
```

在这个例子中，`EntryForm`结构有一个引用`EntryFormViewModel`类实例的`@ObservedObject`属性。这允许`EntryForm`视图使用`$`前缀绑定到视图模型的属性，就像在前面的例子中使用`@State`属性一样。

`EntryFormViewModel`类为每个表单字段定义了`@Published`属性，当这些属性的值改变时，视图可以得到通知。这允许视图在用户更改表单字段时自动更新。

这种方法有助于将视图逻辑和状态管理与视图本身分开，使代码更容易理解和维护。

> 如果我需要验证所有字段都不为空，该怎么办？

如果您需要验证表单中没有一个字段是空的，那么您可以向`EntryFormViewModel`类添加一个方法来检查表单字段的值，并返回一个`Bool`来指示表单是否有效。下面是一个可能的例子:

```
class EntryFormViewModel: ObservableObject {
    @Published var name: String = ""
    @Published var address: String = ""
    @Published var city: String = ""
    @Published var state: String = ""
    @Published var zipCode: String = ""

    var isValid: Bool {
        return !name.isEmpty && !address.isEmpty && !city.isEmpty && !state.isEmpty && !zipCode.isEmpty
    }
}
```

在这个例子中，`isValid`属性使用每个表单字段的`isEmpty`属性来确定表单是否有效。如果所有字段都不为空，`isValid`属性将返回`true`，否则将返回`false`。

例如，您可以在视图中使用该属性来启用或禁用提交按钮，或者在表单无效时显示错误消息。

```
struct EntryForm: View {
    @ObservedObject var viewModel: EntryFormViewModel

    var body: some View {
        Form {
            TextField("Name", text: $viewModel.name)
            TextField("Address", text: $viewModel.address)
            TextField("City", text: $viewModel.city)
            TextField("State", text: $viewModel.state)
            TextField("Zip code", text: $viewModel.zipCode)

            if !viewModel.isValid {
                Text("All fields are required")
                    .foregroundColor(.red)
            }
        }
    }
}
```

在这个例子中，`EntryForm`视图检查`isValid`属性的值，如果表单无效，它使用`Text`视图显示一条错误消息。这允许用户知道为了使表单有效，需要填写哪些字段。

> 我们可以在表单中添加一个保存按钮吗？

是的，您可以通过使用`EntryForm`结构中的`Button`视图向表单添加一个保存按钮。下面是一个可能的例子:

```
struct EntryForm: View {
    @ObservedObject var viewModel: EntryFormViewModel

    var body: some View {
        Form {
            TextField("Name", text: $viewModel.name)
            TextField("Address", text: $viewModel.address)
            TextField("City", text: $viewModel.city)
            TextField("State", text: $viewModel.state)
            TextField("Zip code", text: $viewModel.zipCode)

            if !viewModel.isValid {
                Text("All fields are required")
                    .foregroundColor(.red)
            }

            Button(action: {
                // Save the form data here
            }) {
                Text("Save")
            }
            .disabled(!viewModel.isValid)
        }
    }
}
```

在这个例子中，`EntryForm`视图包括一个`Button`视图，该视图带有一个动作闭包，当点击按钮时会调用这个动作闭包。该闭包可用于将表单数据保存到数据库或其他存储位置。

`Button`视图还有一个`disabled`修饰符，如果`EntryFormViewModel`的`isValid`属性是`false`，这个修饰符将禁用按钮。这确保了在用户保存数据之前表单必须正确填写。

> 你能根据表单信息创建一个用户结构吗？

当然可以！下面是一个可以用来表示表单数据的`User`结构的例子:

```
struct User {
    var name: String
    var address: String
    var city: String
    var state: String
    var zipCode: String
}
```

这个`User`结构定义了每个表单字段的属性，比如`name`、`address`、`city`、`state`和`zipCode`。这些属性可用于存储用户在表单中输入的值。

下面是一个例子，说明如何使用表单中的值创建一个`User`结构的实例:

```
let user = User(
    name: viewModel.name,
    address: viewModel.address,
    city: viewModel.city,
    state: viewModel.state,
    zipCode: viewModel.zipCode
)
```

在这个例子中，`user`常量是用来自`EntryFormViewModel`实例的值初始化的。然后，您可以使用这个`User`实例将数据保存到数据库或其他存储位置，或者将数据传递给应用程序的另一部分，以供进一步处理。

> 我如何从结构中填充视图模型上的字段？

```
class EntryFormViewModel: ObservableObject {
    @Published var name: String = ""
    @Published var address: String = ""
    @Published var city: String = ""
    @Published var state: String = ""
    @Published var zipCode: String = ""

    var isValid: Bool {
        return !name.isEmpty && !address.isEmpty && !city.isEmpty && !state.isEmpty && !zipCode.isEmpty
    }

    func populate(with user: User) {
        name = user.name
        address = user.address
        city = user.city
        state = user.state
        zipCode = user.zipCode
    }
}
```

下面是如何使用该方法填充表单字段的示例:

```
let user = User(
    name: "John Doe",
    address: "123 Main St",
    city: "Anytown",
    state: "CA",
    zipCode: "12345"
)

viewModel.populate(with: user)
```

在这个例子中，`user`常量用一些样本数据初始化，然后在`viewModel`实例上调用`populate(with:)`方法，将`user`实例作为参数传入。这将设置视图模型的属性值以匹配`User`实例的值，这将依次用来自`User`实例的数据填充表单字段。

*结束会话*

总耗时:2 分钟。为这篇文章复制文本并设置格式花费的时间比这长。

## 完成块

几年前，我参加了西海岸电脑展，记得第一次看到苹果丽萨和施乐之星。

当我看着这些屏幕时，我感觉到小手指在我的背上爬来爬去，我记得当时想，“就是它了。从现在开始就是这样了。”

那些早期的所见即所得系统从未真正进入大众市场，但从那时起，两者的影响改变了世界。

我从 ChatGPT 那里得到了同样的感觉。

世界正在改变。

又来了。

继续:[另一个 ChatGPT SwiftUI 会话](https://michaellong.medium.com/another-chatgpt-swiftui-session-67359737033a)