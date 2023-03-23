# TypeScript 中“未知”类型的秘密

> 原文：<https://betterprogramming.pub/secrets-of-unknown-types-in-typescript-2987cd1263f0>

## 为什么在 TypeScript 中“unknown”比“any”更受欢迎？

![](img/ebbd879e49c451e31dfe8b2e77e3661f.png)

本帖将介绍 TypeScript 中的`unknown`类型，它是在 TypeScript 3.0 中引入的，是`any`的类型安全对应。

我为什么这么说？接下来我将展示一下`unknown`型的特点以及与`any`相比的优势，让我们开始吧！

# 可转让性

所有类型都可以分配给`unknown`，但是反过来，`unknown`只能分配给自身和`any`。

# 交叉点类型

在交集类型中，所有类型都会吸收`unknown`。

这并不难理解，因为交集类型代表了两者的交集，而我们在上一节已经知道所有类型都可以赋给`unknown`，所以任何与`unknown`的交集都将是自身。

# 工会类型

在联合类型中，`unknown`吸收除`any`之外的所有类型。

联合类型表示两者的组合。如果两者可以合并，那么就返回范围更宽松的那个，所以除了`any`就是`unknown`。

# 关键字' keyof '

`keyof unknown`返回类型`never`，表示永远不会出现的值的类型。**而** `**keyof any**` **返回可用作对象索引的任何值的类型**。为什么这么说？

这是因为 TypeScript 编译选项中只有 [keyofStringsOnly](https://www.typescriptlang.org/tsconfig#keyofStringsOnly) ，默认为 false，则`keyof any`将返回`string | number | symbol`类型，为 true 时，`keyof any`将返回类型`string`。

值得注意的是，TypeScript 中内置的高级类型`Record`用这个来限定可以用作对象索引的类型。其源代码如下:

```
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

有关 TypeScript 中内置实用程序类型的更多信息，请查看我以前的文章:

[](https://javascript.plainenglish.io/7-typescript-built-in-utility-types-you-must-know-d7a73a489d7) [## 您必须知道的 7 种 TypeScript 内置实用程序类型

### 提高对内置类型的理解。

javascript.plainenglish.io](https://javascript.plainenglish.io/7-typescript-built-in-utility-types-you-must-know-d7a73a489d7) 

关于 TypeScript 中`never`类型的秘密，请查看我的另一篇文章:

[](https://blog.bitsrc.io/secrets-of-never-types-in-typescript-de57795a34da) [## TypeScript 中“从不”类型的秘密

### 如何在 TypeScript 中获取 never 类型的值，never vs. void 在 TypeScript 中，never 在 TypeScript 函数声明中。

blog.bitsrc.io](https://blog.bitsrc.io/secrets-of-never-types-in-typescript-de57795a34da) 

# 已初始化的

没有声明`const`的变量总是被认为是初始化的。

# 缩小类型前的限制

除了等式运算符，不能使用任何其他运算符，如`+-*/`、属性访问、函数调用等。，没有缩小`unknown`的类型。

# 窄型

从上一节我们知道，如果我们要操作`unknown`类型的值，我们必须先缩小类型，这样 TypeScript 编译器就知道我们在做什么，你的代码也就保证了安全。

缩小类型可以使用`typeof`、`instanceof`、自定义类型谓词、类型断言等。

请注意，在使用类型断言时，TypeScript 不会帮助我们执行任何检查，这意味着您必须知道您在做什么，并确保您在做您想要做的事情。

# 结论

`any`类型导致 TypeScript 编译器跳过对该变量的类型检查，这可以理解为进入 TypeScript 类型系统的一个出口，但在大多数情况下，这太宽松了。另一方面，在执行大多数操作之前，类型`unknown`的值必须缩小到一个特定的类型，这通常意味着更安全和更健壮。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://medium.com/@islizeqiang/index-of-my-stories-published-in-2022-3c6e3cb94c07)。