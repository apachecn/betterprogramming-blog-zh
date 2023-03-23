# 如何通过减少集合类型初始化来提高 iOS 代码性能

> 原文：<https://betterprogramming.pub/how-to-boost-your-ios-code-performance-by-reducing-collection-type-initialisation-564bffee94a3>

## 优化快速和客观的 C 代码速度的技巧

![](img/d5042dfb3c40ba2513f744c225bf039a.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

创建新的集合类型会占用资源。频繁创建集合类型对象，例如，在 for 循环或频繁执行的函数中初始化字典，可能会消耗大量时间。

在下面的例子中，我用`reduce(_:_:)`和`reduce(into:_:)`来演示执行速度的差异。这两个示例都旨在创建一个字典，将用户名作为键，将用户 id 作为值。

# 例 1: `reduce(_:_:)`

```
Declaration:
func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) throws -> Result) rethrows -> Result
```

在示例 1 中，第一次执行闭包时，初始结果(一个空字典)被传递给闭包。之后，返回值将在闭包的下一次调用中使用，直到序列结束。

每次执行`reduce(_:_:)`的闭包时，我都会创建一个新的字典，并将捕获的值`dict`赋给它。我向字典中添加了一对新的用户`name`和`id`，然后将其返回。当序列结束时，最终结果是从闭包返回的最后一个`dict`值。

# 例 2: `reduce(into:_:)`

```
Declaration:
func reduce<Result>(into initialResult: Result, _ updateAccumulatingResult: (inout Result, Element) throws -> ()) rethrows -> Result
```

在例 2 中，当一个空字典在开始时被初始化时，每次执行闭包，对`dict`的任何改变都会更新字典。

## 速度比较

我用同样的用户数据测试了速度。

例 1 的执行时间为`0.07398414611816406`秒，例 2 的执行时间为`0.0013837814331054688`秒。比例 1 快了 50 多倍。

如果你喜欢这篇文章，这里有另一篇你可能喜欢的文章:

## 如何提高 iOS 代码性能:减少数组搜索

[](/how-to-boost-your-ios-code-performance-reduce-searching-an-array-55fbdfee2050) [## 如何提高 iOS 代码性能:减少数组搜索

### 优化快速和客观的 C 代码速度的技巧

better 编程. pub](/how-to-boost-your-ios-code-performance-reduce-searching-an-array-55fbdfee2050) 

## 参考

 [## Apple 开发者文档

### 编辑描述

developer.apple.com](https://developer.apple.com/documentation/swift/array/3126956-reduce)  [## Apple 开发者文档

### 编辑描述

developer.apple.com](https://developer.apple.com/documentation/swift/array/2298686-reduce)