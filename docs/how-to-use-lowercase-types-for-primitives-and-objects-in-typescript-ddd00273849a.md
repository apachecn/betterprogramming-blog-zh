# 如何在 TypeScript 中对基元和对象使用小写类型

> 原文：<https://betterprogramming.pub/how-to-use-lowercase-types-for-primitives-and-objects-in-typescript-ddd00273849a>

## 关于数字、大整数、字符串、布尔、符号和对象的完整指南

![](img/ed36116b3ae2332e488685c48bac2e1e.png)

照片由[丹尼尔·库鲁维拉](https://unsplash.com/@d_kuruvilla?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是一种基于 JavaScript 的强类型编程语言。类型安全在 TypeScript 中至关重要。它阻止或防止由不同数据类型之间的差异导致的类型错误。

TypeScript 有原语类型、对象类型、[枚举类型](/a-complete-guide-to-enums-in-typescript-6f76da009146)、联合类型、元组类型、交集类型、`keyof`类型、`typeof`类型、模板文字类型、索引访问类型、映射类型、条件类型、接口类型、类类型、泛型类型等。

不仅种类繁多，有些种类还有两种形态，比如`boolean` vs `Boolean`，甚至还有三种形态，比如`object`、`Object`、`{}`。

在[打字手册](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)中有说明:

*   不要使用类型`Number`、`String`、`Boolean`、`Symbol`或`Object`。
*   务必使用`number`、`string`、`boolean`和`symbol`类型。使用非原语类型`object`代替`Object`。

显然，`BigInt`在列表中缺失。但是规则是一样的:用`bigint`代替`BigInt`。

让我们详细检查一下这些内置类型。

# JavaScript 类型

JavaScript 是一种松散类型的动态语言。JavaScript 中的变量不直接与任何特定的值类型相关联。任何变量都可以被赋值和重新赋值。

JavaScript 变量分为两种类型:

*   原始类型:除了定义不可变值的对象之外的所有类型。
*   对象类型:类型定义了属性的集合。它包括所有不是基元的类型。

基本类型可以进一步分为以下类型:

*   `boolean`
*   `string`
*   `symbol`
*   `number`
*   `bigint`
*   `null`
*   `undefined`

TypeScript 是 JavaScript 的超集。它加强了对 JavaScript 松散类型本质的限制，从而防止了类型错误。

# `boolean`对`Boolean`

`boolean`是逻辑值的基本类型，只能是`true`或`false`。

`Boolean`是一个`boolean`值的对象包装器(函数)。

`new Boolean()`创建一个`Boolean`的对象实例。

当`Boolean`用作类型时，它不引用对象包装器。相反，它是一个 TypeScript 接口，如下所示:

`new Boolean()`从本地构造函数创建一个对象实例。

`boolean`被视为`Boolean`的一个子类型。我们可以给`Boolean`分配一个原始的`boolean`值，但反之则不然。

总而言之，最好的做法是用`boolean`代替`Boolean`。

# `number`对`Number`

`number`是在`[-(2⁵³ − 1), 2⁵³ − 1]`范围内的双精度 64 位二进制格式 IEEE 754 值的基本类型。除了表示浮点数外，它还有三个符号值:`+Infinity`、`-Infinity`、`NaN`(**N**ot**a**Number)。

`Number`是`number`值的对象包装器(函数)。

`new Number()`创建一个`Number`的对象实例。

当`Number`用作类型时，它不引用对象包装器。相反，它是一个 TypeScript 接口，如下所示:

从本地构造函数创建一个对象实例。

`number`被视为`Number`的子类型。我们可以将一个原始的`number`值赋给`Number`，但反之则不然。

总而言之，最好的做法是使用`number`而不是`Number`。

# `bigint vs.` BigInt

`bigint`是表示任意精度整数的数值的基本类型。通过将`n`追加到整数的末尾或者通过调用构造函数`BigInt`来创建`bigint`值。

`BigInt`是一个`bigint`值的对象包装器(函数)。

`BigInt`没有`new`操作员。它也不打算被子类化。它可以用作类定义的`extends`子句的值，但是对`BigInt`构造函数的`super`调用将导致异常。

当`BigInt`用作类型时，它不引用对象包装器。相反，它是一个类型脚本接口。在下面的`BigIntConstructor`界面(第 17 行)中，没有`new`操作符。

`bigint`被视为`BigInt`的一个亚型。我们可以给`BigInt`分配一个原始的`bigint`值，但反之则不然。

总而言之，最好的做法是用`bigint`代替`BigInt`。

# `string`对`String`

`string`是文本值的基本类型。字符串是不可变的——字符串一旦创建，就不能修改。

`String`是`string`值的对象包装器(函数)。

`new String()`创建一个`String`的对象实例。

当`String`用作类型时，它不引用对象包装器。相反，它是一个类型脚本接口。

从本地构造函数创建一个对象实例。

`string`被视为`String`的子类型。我们可以将一个原始的`string`值赋给`String`，但反之则不然。

总而言之，最好的做法是使用`string`而不是`String`。

# `symbol`对`Symbol`

`symbol`是唯一且不可变的值的基本类型，可用作对象属性的键。

`Symbol`是一个`symbol`值的对象包装器(函数)。

`new Symbol()`无效，因为它没有构造函数。

此外，符号不会自动转换为字符串。

当`Symbol`用作类型时，它不引用对象包装器。相反，它是一个类型脚本接口。在下面的接口定义中，没有`SymbolConstructor`。

`symbol`被视为`Symbol`的子类型。我们可以给`Symbol`分配一个原始的`symbol`值，但反之则不然。

总而言之，最好的做法是使用`symbol`而不是`Symbol`。

# 空且未定义

类型只有一个值:`null`。一个`null`值代表一个指向不存在的对象的引用。`null`的行为像一个原语，但是`typeof null`返回`"object"`。

类型只有一个值`undefined`。任何已声明但未赋值的变量都有值`undefined`。`typeof undefined`返回`"undefined"`。

`null`和`undefined`都只有小写选项。

`null`和`undefined`常用于一个活接头类型，如`string | null`、`number | undefined`等。

# 对象、对象和{}

任何非原始类型都被认为是一个`object`类型，它是属性的集合。对象的`typeof`将返回`"object"`或`"function"`。

为什么是`"function"`？

函数是一级对象，因为它们可以像任何其他对象一样拥有属性和方法。历史上，`typeof <function>`返回`"function"`，而不是`"object"`。函数原型对象被指定为函数对象，以确保与在 ECMAScript 2015 规范之前创建的 ECMAScript 代码兼容。

任何非原始值都可以赋给`object`类型变量，如下图所示:

`Object`是一个`interface`。

`new Object()`从本地构造函数创建一个对象实例。

`Object`不如`object`具体。除了`null`和`undefined`之外，任何东西都可以分配给`Object`类型的变量。

另外还有这个空型，`{}`。`{}`在运行时与`Object`相同——除了`null`和`undefined`之外，任何东西都可以分配给`{}`类型的变量。

然而，在编译时，`{}`没有`Object`的任何成员— `{}`没有`Object`那么具体。

因为`Object`接口有一个成员`toString()`，它返回`string`。第 3 行显示一个关于返回`number` (3)的错误。

同时，`{}`和`object`没有此限制。

一般来说，`object`、`Object`、`{}`都不太具体。它们可以相互分配。

同样，定义为`object`、`Object`或`{}`的变量不能访问它们的属性。

据说:不用`Object`，用非原语`object`类型。

根据我们的经验，我们建议不要使用这些通用对象类型。[类型别名或接口声明，甚至匿名类型](/typescript-anonymous-types-type-aliases-and-interface-declarations-b60bc8a08f8c)，是指定类型的更好方法。

# 结论

我们已经研究了 TypeScript 中的许多内置类型，以下是我们的建议:

*   不要使用类型`Number`、`BigInt`、`String`、`Boolean`、`Symbol`、`Object`或`{}`。
*   务必使用`number`、`bigint`、`string`、`boolean`、`symbol`类型。
*   如果有必要，使用`object`类型，但是首先考虑类型别名、接口声明，甚至匿名类型。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。