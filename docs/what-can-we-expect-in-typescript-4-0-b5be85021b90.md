# 在 TypeScript 4.0 中我们可以期待什么？

> 原文：<https://betterprogramming.pub/what-can-we-expect-in-typescript-4-0-b5be85021b90>

## 了解下一版本的 TypeScript 将会带来什么

![](img/43daf5dfbbc0a9c5de2d3bd9f106c0f1.png)

Natalie Rhea Riggs 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在我之前的[文章](https://medium.com/better-programming/whats-new-in-typescript-3-9-70e3d2eabe26)中，我谈到了 TypeScript 版本 3.9 中的新特性。今天，我将浏览预计将于 2020 年 8 月发布的 TypeScript 4.0。根据[迭代计划](https://github.com/microsoft/TypeScript/issues/38510)，看起来他们正在各方面努力，包括语言特性、性能、基础设施，以及——最重要的——错误。

在本文中，我将介绍一些计划在 4.0 版本中发布的特性

# 短路赋值运算符

TypeScript 计划引入一个新的操作符(`??=`)，如果变量是空的，就将默认值赋给变量。

以前，`obj.x = obj.x ?? 20;`。

有了新的符，`obj.x ??= 20;`。

虽然这不是一个突破性的变化，但拥有这些类型的操作总是好的。

# 标记元组元素

还有另一个建议的语言特性，它改变了元组的定义方式。目前，元组的定义没有标签:

```
// length, count
type Segment = [number, number];
```

建议的改进将使该特性能够向每个元组元素添加标签:

```
type Segment = [length: number, count: number];
```

# catch 子句绑定未知

在当前的 TypeScript 版本中，`catch`子句中不允许任何类型的注释。

```
try {
  throw 20;
} catch (err) {
  console.error(err.specialFunction()); 
}
```

在上面的例子中，错误的类型是`any`，因此它不是类型安全的。新的建议是允许在`catch`子句中使用`unknown` 类型的注释，使其类型安全:

# 从构造函数推断类属性

如果我们在`noImplicitAny`模式下配置 TypeScript，**当前版本的 TypeScript 不会编译下面的代码:**

**但是 4.0 版本会编译，TypeScript 会推断出`x`的类型是`string` 还是`number`。**

# **远期申报**

**有些情况下，我们需要能够告诉 TypeScript 某个类型可能存在，这取决于代码执行的环境。在 4.0 版中，TypeScript 通过让我们定义占位符类型来充当占位符，直到实现可用，从而为我们提供了正确处理这种情况的方法:**

**提出的`exists` 关键字将让我们定义那些占位符类型。当引入实际的实现类型时，TypeScript 编译器将确保该实现遵守占位符定义的约束:**

# **其他拟议变动**

**除了这些语言特性，还有许多提议的特性和错误修复。其中一些列举如下:**

*   **[编辑器支持](https://github.com/microsoft/TypeScript/issues/35524) `[/** @see */](https://github.com/microsoft/TypeScript/issues/35524)` [标签](https://github.com/microsoft/TypeScript/issues/35524)**
*   **[编辑器支持](https://github.com/microsoft/TypeScript/issues/390) `[/** @deprecated */](https://github.com/microsoft/TypeScript/issues/390)` [标签](https://github.com/microsoft/TypeScript/issues/390)**
*   **[改进的自动导入](https://github.com/microsoft/TypeScript/issues/37812)**
*   **[调查](https://github.com/microsoft/TypeScript/issues/27711) `[awaited](https://github.com/microsoft/TypeScript/issues/27711)` [型](https://github.com/microsoft/TypeScript/issues/27711)**
*   **[语义高亮](https://github.com/microsoft/TypeScript/issues/38435)**

# **结论**

**理解本文基于 TypeScript 4.0 的迭代计划是很重要的，在实际发布之前事情可能会发生变化。你也可以进入[迭代计划](https://github.com/microsoft/TypeScript/issues/38510)并表达你对提议想法的想法。**