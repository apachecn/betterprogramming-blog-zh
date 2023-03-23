# TypeScript 3.9 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-typescript-3-9-70e3d2eabe26>

## 刚刚上线的所有内容的概述

![](img/2e80e2c9b95b4a88196f0724a3164709.png)

由 [Corinne Kutz](https://unsplash.com/@corinnekutz?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

TypeScript 刚刚在 5 月 12 日发布了今年的第二个版本。是 3.9 版本，现在是稳定版。在本文中，我将指出 TypeScript 3.9 的一些令人兴奋的新特性。

# `@ts-expect-error`

让我们举一个例子，我们定义了一个函数，它将两个字符串作为参数。

```
printName(firstName: string, lastName: string) { 
    console.log(firstName);
console.log(lastName);
    assert(typeof firstName === "string");   
    assert(typeof lastName === "string");
}
```

通常，当 TypeScript 用户误用这个函数时，会得到一个有用的红色波浪线和一个错误消息，JavaScript 用户会得到一个断言错误。但是如果我们编写一个单元测试来检查这个功能，会发生什么呢？

```
expect(() => { 
   printName(1234, 5678); 
}).toThrow();
```

如果我们的测试是用 TS 编写的，它会抛出如下错误:

```
printName(1234, 5678);
// ~~~ 
// error: Type ‘number’ is not assignable to type ‘string’.
```

作为对此的解决方案，TypeScript 版本带来了一个新特性:`// @ts-expect-error`注释。如果我们将此注释作为前缀放在代码行之前，TypeScript 不会报告错误。但是如果没有错误，TypeScript 会通知我们的代码中有一个不必要的注释，就像这样:`Unused '@ts-expect-error' directive`。

这个`//@ts-expect-error`注释的另一个用途是我们可以将它用作抑制注释。`// @ts-ignore`是一个现有的注释，用作抑制注释——这两个注释的主要区别是`// @ts-ignore`将通知下面的行是否没有错误。

# 速度改进

![](img/7ed9eac63732e4cb905d346d056c4140.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

对于早期版本的 TypeScript，有很多关于 Material-UI 等包的编译和编辑速度的抱怨。

在新版本中，TypeScript 开发团队通过优化几个涉及大型联合、交集、条件类型和映射类型的病态案例解决了这个问题，并将 Material-UI 编译的时间减少了 40%。此外，他们还致力于像 Visual Studio 代码这样的编辑器的文件重命名功能。

# JavaScript 中的 CommonJS 自动导入

以前，无论文件类型如何，TypeScript 总是期望 ECMAScript 样式的导入。但是大多数开发人员在编写 JavaScript 文件时使用 CommonJS 风格的`require();` 导入，而不是 ECMAScript 风格的模块。

ECMAScript 样式:`**import** * **as** fs **from** "fs";`

常见风格:`**const** fs = require("fs");`

在最新版本中，TypeScript 现在会自动检测您正在使用的导入类型，以保持文件的样式整洁。

# 推理和`Promise.all`的改进

在 3.7 版本中，TypeScript 对像`Promise.all`和`Promise.race`这样的函数声明进行了更新。但是这个更新有一些复杂之处，因为它在用`null`或`undefined`混合值时导致了回归。下面给出一个例子。

```
interface Bird{ 
   fly(): void 
} 
interface Fish{ 
   singKissFromARose(): void 
} async function animalBehaviours(birdBehaviour: Promise<Bird>,     fishBehaviour: Promise<Fish| undefined>) { 
   let [bird, fish] = await Promise.all([birdBehaviour,    fishBehaviour]); 
   bird.fly();
   // ~~~~ 
   // Object is possibly ‘undefined’. 
}
```

虽然`fishBehaviour` 是包含`undefined`的一个，但不知何故影响了`birdBehaviour` 也包含`undefined`。因此，这个问题在 3.9 版的 TypeScript 中也得到解决。

# 代码操作保留新行

![](img/3ce39e07f66382104693175bc4b112de.png)

[Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

许多开发人员喜欢在独立的功能代码行之间保留新行，但早期的 TypeScript 代码重构并不保留新行。它几乎删除了代码行之间的所有空白行。在最新的版本中，TypeScript 已经解决了这个问题，现在 TypeScript 代码重构在运行重构后保留了代码中的新行。

# 缺失返回表达式的快速修复

有时我们可能会忘记在函数结束时返回函数的值。因此 TypeScript 现在提供了一个快速解决方案来添加缺失的`return`语句。

# 条件表达式中未调用的函数检查

在 3.7 版本之后，TypeScript 在`if`条件中检查未调用的函数检查并报告错误。在 3.9 版本中，这种错误检查还进一步扩展到了三元条件语句。

# 额外的小改动

除了这些变化，还有一些小的变化，如:

*   `}`和`>`现在是无效的 JSX 文本字符
*   `export *`被保留下来
*   Getters 和 setters 不再是可枚举的
*   扩展`any` 的类型参数不再充当`any`
*   更多`libdom.d.ts`改进

总的来说，这些是我们在最新发布的 TypeScript 中可以看到的变化。因此，如果你是一个打字爱好者，保持与新的[更新](https://devblogs.microsoft.com/typescript/announcing-typescript-3-9)同步总是好的，这可以使你的编码生活更容易。