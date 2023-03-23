# 介绍 Assert——我的 XCTest 包装框架

> 原文：<https://betterprogramming.pub/assert-my-wrapper-framework-around-xctest-7d6bea2d05f9>

## 在 iOS 中编写健壮的测试

![](img/33bfe9340b11977638a3feec88f071f0.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com/s/photos/test?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

XCTest 感觉像是从未清理过的 Swift 迁移的一部分，甚至不清楚我们应该以什么顺序将变量传递到像`XCTAssertEqual`这样的东西中，这常常取决于谁编写了测试:

```
XCTAssertEqual(actual, expected)
XCTAssertEqual(expected, actual)
```

记住这一点，让我们通过在我们想要断言的东西周围创建一个包装器来拆分断言。然后我们将编写一个简单的函数来返回我们的`Assert`对象。

现在我们有了包装器，我们可以添加一些扩展，这里我将主要依靠`XCAssert`函数，但是你也可以很容易地依靠`throws`。

很简单，此时我们可以将上面的`XCTAssertEqual`替换为:

这很好，还有一点我们可以清理，让我们介绍一个运营商。

在这一点上你可以停下来，但是当我们想要处理更复杂的类型时呢？让我们添加一些对闭包和`@dynamicMemberLookup`的支持。

这允许我们做什么？

有了闭包，我们可以更容易地断言一个对象上的值，而不必在每个字段上执行`assert`，我们返回一个`Assert<User>`，并且可以使用`@dynamicMemberLookup`对用户的所有字段进行操作。

如果一个断言失败了，我们仍然会看到由于`file: StaticString = #file`和`line: UInt = #line`它在哪一行失败。

如果您对这种方法感兴趣，我已经创建了一个 Swift 包，其中包含了额外的操作符`!=`、`<=`、`<`、`>`、`>=`、`!==`、`===`等。如果您愿意，它还提供文本表示(即`greaterThanOrEqual(to: ...)`)。命名遵循`XCTAssert`命名。

Swift 套餐/更多信息:[https://github.com/cjnevin/Assert](https://github.com/cjnevin/Assert)