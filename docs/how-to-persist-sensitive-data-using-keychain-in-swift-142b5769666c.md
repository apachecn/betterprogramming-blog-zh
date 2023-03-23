# 如何在 Swift 中使用 Keychain 保存敏感数据

> 原文：<https://betterprogramming.pub/how-to-persist-sensitive-data-using-keychain-in-swift-142b5769666c>

## 不要使用用户默认值来存储数据。请改用钥匙链

![](img/4369855c388eaba720e8d5454e6374e0.png)

照片由 [Vinit Vispute](https://unsplash.com/@creativemomentsphotography09?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

开发 iOS 应用程序时，有时我们需要在本地存储敏感数据(密码、访问令牌、密钥等)。对于初级开发人员来说，首先想到的是使用`UserDefaults`来存储它。

然而，众所周知，使用`UserDefaults`存储敏感数据是一个[非常糟糕的主意](https://www.youtube.com/watch?v=UAgtOTOH2nQ)，因为使用`UserDefaults`存储的数据没有加密，非常不安全。

为了安全地在本地存储敏感数据，我们应该使用苹果公司提供的[钥匙链服务](https://developer.apple.com/documentation/security/keychain_services)。这是一个相当老的框架，因此正如你将在后面看到的，它的 API 不像苹果的其他现代框架那样快捷。

在本文中，我将向您展示如何创建一个通用的助手类，它可以在 iOS 和 macOS 中工作，使用 keychain 服务保存、更新、读取和删除数据。涉及的话题太多了，所以让我们开始吧！

# 将数据存储到钥匙串

如前所述，我们将在文章中创建一个助手类。为了简单起见，让我们将 helper 类设为单例类:

为了将数据保存到 keychain，我们必须利用接受类型为`CFDictionary`的查询对象的`SecItemAdd(_:_:)`方法。

我们的想法是创建一个查询对象，其中包含我们想要保存的数据以及与数据相关联的主键。之后，我们将通过向`SecItemAdd(_:_:)`方法提供查询对象来执行保存操作。

从上面的代码片段中可以看出，查询对象由 4 个字典键组成，让我们详细了解一下:

*   `kSecValueData`:表示保存到钥匙串的数据的密钥。
*   `kSecClass`:表示正在保存的数据类型的键。这里我们将它的值设置为`kSecClassGenericPassword`,表示我们正在保存的数据是一个通用密码项。
*   `kSecAttrService`和`kSecAttrAccount`:当`kSecClass`设置为`kSecClassGenericPassword`时，这两个键是必须的。这两个键的值将作为保存数据的主键。换句话说，我们稍后将使用它们从钥匙串中检索保存的数据。

专业提示:查看[这个](https://developer.apple.com/documentation/security/keychain_services/keychain_items/item_class_keys_and_values#1678477)和[这个](https://developer.apple.com/documentation/security/errsecduplicateitem)文档，了解`kSecClass`的其他可能值和它们各自的主键。

对于`kSecAttrService`和`kSecAttrAccount`使用什么值没有硬性规定。但是，建议使用有意义的字符串。例如，如果我们正在保存脸书访问令牌，我们可以将`kSecAttrService`设置为“*访问令牌*”，将`kSecAttrAccount`设置为“ *facebook* ”。

创建查询对象后，我们可以调用`SecItemAdd(_:_:)`方法将数据保存到 keychain 中。

然后`SecItemAdd(_:_:)`方法将返回一个`OSStatus`来指示保存操作的状态。如果我们获得`errSecSuccess`状态，这意味着数据已经成功保存到钥匙串中。

下面是如何使用我们刚刚创建的`save(_:service:account:)`方法:

Keychain 在 Xcode playground 中不工作，因此您可以在视图控制器、SwiftUI 视图中运行上面的代码，也可以将其作为单元测试用例运行。

# 更新钥匙串中的现有数据

现在我们已经有了`save(_:service:account:)`方法，让我们尝试使用相同的`kSecAttrService`和`kSecAttrAccount`值保存另一个令牌:

这一次，我们将无法将访问令牌保存到钥匙串中。相反，我们将在 Xcode 控制台中得到一条消息，显示“`Error: -25299`”。错误代码`-25299`表示保存操作失败，因为我们使用的密钥已经存在于钥匙串中。

为了解决这个问题，我们需要检查`-25299`错误代码(相当于`errSecDuplicateItem`)，然后使用`SecItemUpdate(_:_:)`方法更新密钥链。让我们继续更新我们之前的`save(_:service:account:)`方法:

与保存操作类似，我们必须首先创建一个由`kSecAttrService`和`kSecAttrAccount`组成的查询对象。但是这一次，我们将不得不创建另一个由`kSecValueData`组成的字典，并将其提供给`SecItemUpdate(_:_:)`方法。

这样，我们就使我们的`save(_:service:account:)`方法能够更新 keychain 中的任何现有项目。

# 从钥匙串中读取数据

从钥匙串中读取数据的方式与我们将数据保存到钥匙串中的方式非常相似。我们首先创建一个查询对象，然后调用一个方法从 keychain 中获取数据:

像往常一样，我们需要为查询对象设置`kSecAttrService`和`kSecAttrAccount`的值。除此之外，我们还需要在查询对象中包含一个新键`kSecReturnData`，并将其值设置为`true`。这表明我们希望查询返回项目数据。

之后，我们将利用`SecItemCopyMatching(_:_:)`方法并通过引用传入一个`AnyObject`类型的`result`对象。这个`result`对象将保存查询对象所请求的项目数据。

最后，我们将把`result`对象转换成`Data`并返回它。值得注意的一点是，就像`SecItemAdd(_:_:)`方法一样，`SecItemCopyMatching(_:_:)`方法也将返回一个表示读取操作状态的`OSStatus`，但是我们在这里不做任何检查，因为如果读取操作失败，我们将返回`nil`。

这就是让我们的 keychain helper 类支持读操作的全部内容。让我们来看看它的实际应用:

# 从钥匙串中删除数据

如果我们的钥匙串助手类不支持删除操作，它将是不完整的。继续添加以下代码片段:

如果您一直在跟进，那么您应该对上面的代码很熟悉。这几乎是不言自明的，只需注意这里我们使用`SecItemDelete(_:)`方法从 keychain 中删除项目。

# 创建通用钥匙串助手类

在这个阶段，我们已经实现了 keychain helper 类的所有必要功能。但是，有一个限制——它只支持读写类型为`Data`的项目。如果我们可以在钥匙串上存储任何数据类型的对象，那不是很好吗？

这里的想法是创建一个通用的保存方法，该方法接受任何数据类型符合`Codable`协议的对象。这样，我们将能够使用`JSONEncoder`编码给定的对象，并使用我们之前创建的`save(_:service:account:)`方法存储它。

通过使用相同的思想，创建一个通用的 read 方法非常简单。我们将使用`JSONDecoder`来解码从钥匙链中获得的数据并将其返回。

这样，我们就成功地使我们的 keychain helper 类通用化了。让我们来看看它的实际应用:

如果您想在自己的项目中尝试 keychain helper 类，您可以在这里获得完整的示例代码。

# 包扎

创建应用程序时，我们有责任保护用户的隐私，并确保他们的敏感数据安全地存储在应用程序中。因此，如果你还在使用`UserDefaults`保存用户的敏感数据，你绝对应该停止这样做，而开始使用钥匙链！

如果你喜欢阅读这篇文章，并希望在新文章发布时得到通知，请随时在 Twitter 上关注我。

感谢阅读。