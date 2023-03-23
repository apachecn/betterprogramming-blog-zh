# 您可能已经错过的 TypeScript 2.9 的特性

> 原文：<https://betterprogramming.pub/features-from-typescript-2-9-that-you-may-have-missed-899faadccca>

## TypeScript 已经超越了 2.9 版本。然而，在以前的版本中，有一些很棒的特性你可能已经错过了

![](img/a026f63217b072d4496361c68bfc88f1.png)

Christian Wiediger 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在本文中，我们将看看 TypeScript 2.9 发布的一些您可能已经错过的有用特性。

# 支持带有 keyof 和映射类型的数字和符号命名属性

在 TypeScript 2.9 之前，`keyof`和映射类型只支持字符串键。

现在它支持 JavaScript 支持的键的所有数据类型和映射类型。

使用 TypeScript 2.9，`for...in`循环可以将键解析为任何类型。

`keyof T`对于某些类型来说`T`是`string | number | symbol`联合类型的一个子类型。

映射类型`{ [P in K]: XXX }`让我们将`K`分配给`string`、`number`或`symbol`。

给定一个类型为`X`的对象，`keyof X`现在解析如下:

*   如果`X`包含字符串索引签名，`keyof X`的类型为`string`、`number`，文字类型表示类似符号的属性。
*   如果`X`包含数字索引签名，那么`keyof X`是`number`和表示类似字符串和类似符号属性的文字类型的并集。
*   否则，`keyof X`是表示类似字符串、类似数字和类似符号属性的文字类型的并集。

例如，如果我们有以下代码:

那么`K1`将是所有键类型的联合，即:

```
"foo" | typeof fooSymbol | Enum.A | "a" | 5 | 2
```

这是我们所有文字类型的联合。

如果我们使用`Extract`工具类型从所有文字类型的联合中提取字符串，就像我们使用`K2`一样，那么我们有:

```
"foo" | "a"
```

为`K2`的型号。

`K3`是从`keyof Foo`创建的文字类型中提取所有数字而创建的类型，这将使我们:

```
Enum.A | 5 | 2
```

最后，`K4`的类型是`typeof fooSymbol`，这是一个符号，因为我们再次使用了`Extract`实用程序类型。

因为`keyof`现在正确地支持所有类型的键，所以像`Partial<T>`和`Readonly<T>`这样的映射类型在应用于带有数字索引签名的对象类型时可以正确地工作。

例如，如果我们有:

然后我们知道`x`是一个字符串而不是`any`，因为当我们定义`Arrayish`映射类型时，数字键被 TypeScript 识别。

我们也可以用符号键访问值。例如，如果我们写:

2.9 之前的 TypeScript 版本会给我们一个错误，因为`keyof`不能识别符号。

然而，有了 TypeScript 2.9，我们不再有符号问题了。

# JSX 元素中的泛型类型参数

使用 TypeScript 2.9，我们可以将泛型类型参数传入组件。

例如，我们可以写:

```
type Props = { a: number; b: number; };  
const foo = <GenericComponent<Props> a={10} b={20} />
```

通过将`Props`类型传入我们的通用组件来验证我们传入的属性。

# 导入类型

`import`类型让非模块全局脚本访问模块中声明的类型。

例如，我们可以把:

```
import("./module")
```

来获取导入模块的类型。

# 支持 import.meta

TypeScript 2.9 支持引用`import.meta`属性。

# resolveJsonModule 模块

我们可以在 TypeScript 中导入`.json`文件，将`tsconfig.json`中的`resolveJsonModule`选项设置为`true`。

# 结论

TypeScript 2.9 的最大特点是支持带有各种运算符和实用程序类型的数字和符号键。

除此之外，还有一些新的选项可以让我们在 TypeScript 代码中引用导入类型和导入 JSON。