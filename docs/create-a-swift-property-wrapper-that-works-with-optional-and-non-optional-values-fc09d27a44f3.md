# 创建使用可选和非可选值的 Swift 属性包装器

> 原文：<https://betterprogramming.pub/create-a-swift-property-wrapper-that-works-with-optional-and-non-optional-values-fc09d27a44f3>

## 深入探究属性包装器和可解码的魔法

![](img/e1b869a7808b5a2f2513a5b461a8f6fd.png)

[https://developer.apple.com/swift/images/swift-og.png](https://developer.apple.com/swift/images/swift-og.png)

`@propertyWrapper`是一个解决方案，允许开发人员在管理属性的代码和变量定义之间添加一个逻辑层。自从在 Swift 5.1 中引入以来，他们已经发现了许多用例，如将数据保存到缓存、在使用前改变变量、数据验证等。

在文档中可以找到许多关于属性包装器的有用信息。以下是一些例子:

*   [https://docs . swift . org/swift-book/language guide/properties . html](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

或在提案中

*   [https://github . com/apple/swift-evolution/blob/main/proposals/0258-property-wrappers . MD](https://github.com/apple/swift-evolution/blob/main/proposals/0258-property-wrappers.md)

# 介绍

我面临的问题是要有一个既能应用于字符串又能应用于可选字符串的单属性包装器，如下所示:

更一般的是:

# 真实世界的例子

每个 API 响应包含一个名为`id`的字段，对于每个可以被请求的对象都是唯一的。它可能有以下类型:

*   `Integer` : 1，2，3 …
*   `String`:“甲”、“乙”、“丙”…
*   或者，在特定情况下，如微服务器错误，它可以为空。

以下是一些响应示例:

## 带有字符串 id 的响应

## 带有 int id 的响应

## 没有任何 id 的响应

# 我们来处理吧！

当涉及到设计代码时，总是从末尾开始。想想你的解决方案应该是什么样子，并尝试调整你的内部源代码。

*   每个界面都应该尽可能简单
*   其他开发人员可用的每一个代码、公共接口或任何一种被广泛使用的源代码都应该简短、信息丰富、易于理解，并且易于使用

想到的最简单的解决方案是这样的:

```
@StringAdaptable var id: String
```

实现简单明了:

当从解码器初始化一个值时，它首先被转换为 int 类型，如果失败，则转换为 string 类型。当两者都失败时，控制台会抛出一个错误。

它在 JSON 中运行良好，如下所示:

但是当`id`变成零值时会发生什么呢？当前代码抛出了一个大家都很熟悉的错误:

```
keyNotFound(CodingKeys(stringValue: “id”, intValue: nil), Swift.DecodingError.Context(codingPath: [], debugDescription: “No value associated with key CodingKeys(stringValue: \”id\”, intValue: nil) (\”id\”).”, underlyingError: nil))
```

好，让我们试着将`id`变量改为可选变量:

这一次，我们在运行时发现了两个错误，但在编译时没有。他们在这里:

```
Property type 'String?' does not match 'wrappedValue' type 'String'
Type 'Song' does not conform to protocol 'Decodable'
```

因为属性包装器中的`wrappedValue`是一个字符串，编译器抛出一个类型不匹配错误，正如您所看到的:

```
String? is not a String
```

之后，出现第二个错误，表明 Song 没有实现 Decodable。

让我们停下来想一想如何让这个代码工作。想来想去，只有一个好的明确的办法。`StringAdaptable`确认可选和非可选字符串。

这让我们想到了最初的问题:

# 如何做一个可以添加可选或者非可选的属性包装器？

首先，创建一个将在属性包装中用作通用解决方案的协议。它表明实现该协议的每个类都可以用 string 表示。

然后，两种类型必须实现`ExpressibleByString`:

*   线
*   可选

最后是`StringAdaptable`的一些变化。代码如下:

属性包装器(`StringValue` ) 中的泛型值必须是`ExpressibleByString`和`Decodable`的类型。类型约束用于强制包装可以处理的两种不同类型。

*   `String`
*   `Optional<String> aka String?`

现在，下面的代码没有显示任何编译错误:

但令人惊讶的是，还是没有效果。在解析过程中，一个空值程序抛出一个错误。它看起来是这样的:

```
keyNotFound(CodingKeys(stringValue: "albumId", intValue: nil), Swift.DecodingError.Context(codingPath: [], debugDescription: "No value associated with key CodingKeys(stringValue: \"albumId\", intValue: nil) (\"albumId\").", underlyingError: nil))
```

# 了解合成器

为了找到错误的解决方案，理解 Decodable 在幕后是如何工作的是很好的。当某个物体符合它时会发生什么？

让我们从合成器如何工作的基本知识开始:

*   第一步是生成`CodingKeys`，如果它们还不存在的话。
*   第二个是生成`init(from: Decoder)`，如果它还不存在的话。
*   第三是翻译属性包装器，如果它们存在的话。

第一步非常简单，可能错误并不隐藏在其中。

仔细看看第二步。在构造函数初始化之前，必须将属性包装转换为有效的类型。例如，以下属性:

```
@StringAdaptable var albumId: String?
```

被翻译成

```
var albumId: StringAdaptable<String?>
```

这可能会令人困惑，也是为什么以前的代码版本不能正确解码带有可选值的 JSON。我首先想到的是，可选值将被转换为:

```
var albumId: StringAdaptable<String>?
```

但是一个可解码的合成器不是这样工作的。

# 跳转到 KeyedDecodingContainer

每种被解码的类型都使用一个类`KeyedDecodingContainer`。

*   [https://developer . apple . com/documentation/swift/keyeddecoding container](https://developer.apple.com/documentation/swift/keyeddecodingcontainer)

为了正确解码`@StringAdaptable`,必须扩展`KeyedDecodingContainer`。它必须支持`StringAdaptable<String>`和`StringAdaptable<String?>`类型，允许可选和非可选值从 JSON 准确地转换为预期的变量。

如果一个值出现在给定的 JSON 中，它将被解析并返回一个给定值。否则，它返回用 nil 初始化的`StringAdaptable`，在代码执行过程中将被 nil 替换。

# 完整代码

# 包裹

`Decodable`大部分时候就是管用。在引擎盖下，它执行所有 JSON 编码/解码魔术。但有时，当出现特殊要求时，最好记住，即使在代码执行期间解码有效，在编译之前还有一个额外的步骤:合成。

即使带有值的结构简单明了，编译器也会生成许多样板文件来确保程序正确执行。

就是这样。

感谢阅读！敬请关注更多内容。