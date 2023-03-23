# Xcode 11.4 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-xcode-11-4-bd7c6f6bb0ba>

## Swift 5.2、Clang 编译器 11.0.3 等等

![](img/7018c4834506b7357f3947eb97822bd2.png)

苹果不断尝试，并制造出我们最喜欢的工具，为 iOS/macOS/watchOS/etc 开发应用程序。甚至更好。

他们宣布了有趣的新功能，我将简要介绍一下。在我看来，它们实际上非常重要。

# Clang 编译器(11.0.3)

如果您的框架使用任何第三方框架依赖项，编译器会在框架导入第三方依赖项时默认显示警告`-Watimport-in-framework-header`。

# 排除故障

*   视图调试器显示`UILayoutGuide`及其引用约束
*   视图调试器支持显示层。
    *编辑→显示图层*
*   您可以将 Xcode 的控制台 I/O 转发到终端。
    *产品→方案→编辑方案→选项页签*

# 模拟器

*   通过拖放或终端为可信连接安装证书:
    `xcrun simctl keychain booted add-root-cert my-selfsigned.cer`
*   更改或重置模拟器设备隐私权限。
    *例如，为位置授予权限，当 app 在使用时:*
    `xcrun simctl privacy booted grant location com.domain.app` *或为始终:* `xcrun simctl privacy booted grant location-always com.domain.app` *重置:*
    `xcrun simctl privacy booted reset all com.domain.app`
    *或应用所有权限:*
    `xcrun simctl privacy booted grant all com.domain.app`
*   切换外观模式—例如，亮/暗
    `xcrun simctl ui booted appearance dark`
*   更改设备载体名称:
    `simctl simctl status_bar booted override --operatorName 'Starlink'`
*   模拟器支持后台内容获取通知和远程推送通知。此外，simctl 还有用于远程推送通知的子命令`push`。
    T10。
    *详细解释，查看官方文档。文末有引用。*

## Swift (5.2)

*   更新的编译器支持从一级函数在高阶函数中声明默认值

```
func foo(x: Int) {
  func bar(y: Int = x) { print(y) } bar(y: 42)
  bar() // 21
}foo(x: 21)
```

*   结构(可能的类和枚举)支持`callAsFunction`特性。
    想法是像使用函数一样使用引用。很高兴知道`callAsFunction`是不受限制的，其行为就像一个典型的函数。它支持闭包、`mutating`关键字，可以`throw`和`rethrows`。并且可以声明不同的`callAsFunction`

```
struct Escaper {
  let strategy: EscapeStrategy
  let archiver: Archiver func callAsFunction(_ value: String) -> String {
    archiver.exec(strategy.encode(value))
  }
}let escape = Escaper(strategy: strategyA, archiver: gzipArchiver)
escape('Occupy Mars 🚀')
```

*   下标支持默认参数

```
struct MatrixArea {
  subscript(x: Int, y: Int = 0) -> Int {
    ...
  }
}matrix[10] // 100
matrix[2, 2] // 16
```

*   代码诊断的新策略——因此，更好的编译时错误解释。在此见示例[。](https://swift.org/blog/new-diagnostic-arch-overview/)

# 测试

`XCTest`类支持向`setUp()`、`tearDown()`、
、`setUpWithError()`和`tearDownWithError()`抛出函数。如果抛出错误，该方法将被标记为失败，但使用 XCTSkip API —跳过。

一个有趣的特性是可以根据条件跳过测试代码流:

```
func testLocation() throws {
  try XCTSkipUnless(Permissions.locationGranted) // skip test

  ...
  switch location.intersects(dimension: dimension) {
    case .one: try XCTSkip() // skip test
    case .two: try testJump() 
    case .three: try testLookingAfterCorner() @unknown default: fatalError()
  }
}
```

# 摘要

我喜欢 Xcode 及其相关工具的每一次更新，开发应用程序和项目变得更加容易和舒适。我希望今年会带来更多有趣的事情。

更详细的信息在这里:

 [## Xcode 11.4 发行说明

### Xcode 11.4 包括 iOS 13.4、iPadOS 13.4、tvOS 13.4、watchOS 6.2 和 macOS Catalina 10.15.4 的 SDK。Xcode 11.4…

developer.apple.com](https://developer.apple.com/documentation/xcode_release_notes/xcode_11_4_release_notes)