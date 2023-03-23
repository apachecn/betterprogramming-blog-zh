# 如何离线存储可编码模型

> 原文：<https://betterprogramming.pub/how-to-store-a-codable-model-offline-693329470b2>

## 在一个. json 文件中保持您的模型离线，并在离线时为存储和提取模型编写通用代码

![](img/0b9ab1956161f93083b0a2f516743504.png)

照片由[thiago japyasu](https://www.pexels.com/@japyassu?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从 [Pexels](https://www.pexels.com/photo/silver-iphone-x-beside-succulent-plant-1069798/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

大多数时候，我们可能需要像`CoreData, UserDefaults, SQLite`一样在存储中保持模型数据离线，但是可能一些开发人员不想花太多时间来创建核心日期实体或数据库表，这需要更多的代码，并且存储巨大的数据库是很好的。

将模型数据保留在应用程序中而不在应用程序中创建任何数据库的替代解决方案。我们将使用`.json`文件来保存和检索我们的可编码模型。

## 先说流程

我们有学生模型。

```
struct **Students**: **Codable**{
    let name, section, rank: **String**
}
```

*我们有几行代码用于在* `*FileManager*` *和 JSONEncoder 内创建一个* `*.json*` *文件，用于将我们的模型转换成数据形式。*

## 让我们保存并获取我们的可编码模型

如果你是第一次接触 Codable，请查看我之前的文章关于 Swift 中的 Codable。

离线保存模型

`let status = try **save**(students, **for**: “Students”)`

*在那一行中，我们调用* `*save*` *函数为一个学生模型取* `*“Students”*` *文件名。*

## 离线提取模型

*对于离线获取模型日期，我们需要将文件名传入* ***函数*** *，它将返回* ***学生*** *模型。*

**获取模型**

`let model = try **loadJSON**(withFilename: “Student”)`

现在我们知道了保存和获取模型以供离线使用的过程，并且不能为了保存和获取的目的而为每一个模型编写这样的代码。所以我们可以为它编写一个通用代码。对于相同的代码，从一个地方处理将会很容易。

这里我们将使用枚举来代替类或结构创建泛型代码。这将很容易使用，不需要初始化。

```
enum **FileUtility**<Value: **Codable**>{
    case fileName
}
```

*现在我们要给模型添加文件名基础:*

*我们将把* ***文件*** `*Enum*` *添加到我们之前列举的案例:*

```
enum **FileUtility**<Value: **Codable**>{
    case fileName(**Files**)
}
```

在扩展中，我们有保存和获取的功能。这些函数将有功能为`Encoding & Decoding`你的模型为`.json`文件到`Data`。

这些方法将是私有的。

我们只公开这些来访问 JSON 文件中的保存值和获取值。

有了这个功能，我们可以轻松地让我们的可编码模型离线。这个通用代码将只支持可编码模型。

代码的使用:

通用 Emun 需要具有文件名和操作名的模型。它易于使用和理解。

# 结论

这是从`.json`文件中保存和获取模型的快捷解决方案。你不能用这个代替`CoreData`或`SQLite`。你可以用这个功能代替`UserDefaults` `UserDefaults`是一个强大的 API 大多数开发人员喜欢在沙箱中保存少量数据。你可以用`UserDefaults`做同样的事情。至于你喜欢哪一个，完全是你自己的选择。通过将数据保存在文件管理器中，没有任何限制—这是 tt 的另一个替代解决方案。

你[可能也更喜欢](https://medium.com/better-programming/userdefaults-in-swift-4-d1a278a0ec79) `*UserDefaults*`。

因为更大的尺寸，获取和保存你的模型数据需要时间，所以你应该记住。这在您的代码中很容易使用，但是您需要小心使用它，并且您需要正确地提供文件名和模型类型。

您可以从这里获得完整的源代码。