# 有助于 Swift Codable 的 3 个小解决方案

> 原文：<https://betterprogramming.pub/3-solutions-help-you-swift-codable-cfd8de188676>

## 定制您的可编码实现以适应不同的高级场景

![](img/4503ecae5ff4b3626f9457e1ac4436c3.png)

照片由 [Laurenz Heymann](https://unsplash.com/@einfachlaurenz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

Swift 4 引入了`Codable`协议来解码/编码 JSON 对象。`Codable`紧密集成到 Swift 工具链中。在客户端和服务器端 Swift 上广泛使用。

有时，我们会遇到一些挑战，需要我们定制如何使用`Codable`来适应不同的高级场景。这里是我在使用`Codable`和我的干净代码解决方案时遇到的三个严峻挑战。

# 同一个键有多个值类型

## 假设的 API

考虑一个假设的 API，它有时返回一个`String`，有时为键`id`返回一个`Int`:

杨梦的多重类型

最常见和最直接的解决方案是使用 Enum 来映射不同的情况。我们有干净的代码解决方案来避免它引入的样板文件吗？

## 干净代码解决方案

为了减少样板文件并生成干净的代码，我使用了`PropertyWrapper`:

杨梦的 CodableValue.swift

让我们一点一点地分解代码:

*   `LosslessStringCodable`协议是`LosslessStringConvertible`和`Codable`协议的组合。对于任何可以字符串转换的类型，它都可以使用泛型类型`T`。
*   `DynamicCoder`接受通用类型`LosslessStringCodable`。它可以解码不同的类型，包括`String`、`Bool`、`Int`、`Double`到`LosslessStringCodable`类型。它可以很容易地扩展到其他类型。
*   `CodableValue`是一个属性包装器，它将`wrappedValue`解码和编码成所需的泛型类型。

## 示例使用

杨梦的 User.swift

struct `User`使用 ID 作为`String`值的`CodableValue`属性包装器，但是它当然可以用作其他类型。

# 动态键

## 假设的 API

考虑一个假设的 API，它按顺序返回 JSON 对象作为键:

杨梦的 DynamicKeys.json

## 干净代码解决方案

使用字典来解码 JSON 将是一个合适的解决方案:

杨梦的 Customer.swift

## 示例使用

将 JSON 转换为数据，并使用`JSONDecoder`对其进行解码:

杨梦的《解码.雨燕》

# 具有协议属性的可编码结构

## 假设的 API

考虑一个假设的 API，它返回具有不同类型`Article`和`Brief`的已发布片段:

杨梦的 Publications.json

## 干净代码解决方案

杨梦的 PublicationMapper.swift

让我们来分解代码:

*   `PublicationType`包含两种情况:`Article`和`Brief`。最重要的部分是它根据情况返回不同的结构类型。
*   `PublicationMapper`具有值`publication`和映射协议类型的隐藏值`metatype`。
*   `PublicationMapper`实现编码器功能，将`publication`编码为特定的结构类型。
*   `PublicationMapper`还实现了 decoder 函数，通过检测元类型将 JSON 解码成特定的 struct 类型。

## 示例使用

杨梦的《共和报》

使用定制的`CodingKeys`和编码器/解码器功能实现`publican`结构，以将特定结构映射到`contents`数组。

杨梦的《解码.雨燕》

# 结论

使用`Codable`使得使用 JSON 序列化更加容易。如果你感兴趣，我发表了另一篇关于 Codable 和 SwiftyJSON 比较的文章。

本文提到的所有代码都可以在 GitHub 上找到[。](https://gist.github.com/ericleiyang/2bc5a1545ffcab83fe0d9c2a5563f6ae)