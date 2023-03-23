# 为 JavaScript 和 TypeScript 导出和导入模块的便捷指南

> 原文：<https://betterprogramming.pub/a-handy-guide-to-export-and-import-modules-for-javascript-and-typescript-6cff8e47d554>

## 如何准确地包含完成工作所需的内容

![](img/27c1903c37c337c3242429100b216ec1.png)

[法托斯 Bytyqi](https://unsplash.com/@fatosi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

EcmaScript 是一种脚本语言规范，由 ECMA 的 Ecma International-262 和 ISO/IEC 16262 标准化。JavaScript 是 ECMAScript 最流行的实现之一。从 1997 年 6 月的第一版开始，JavaScript 已经发展到提供更多的功能并且变得更容易使用。ES6 或 ES2015 是一个革命性的版本。它为编写复杂的应用程序增加了大量的新语法。其中一个突出的增加是模块。

ES6 模块是一个 JavaScript 文件。它在自己的范围内执行，而不是在全局范围内。在模块中声明的变量、函数和类不能在模块之间共享，除非它们被显式导出和导入。

*   [export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) :从模块中导出原始值、对象、函数或类的语句，以供其他模块使用。
*   [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) :使用从另一个模块导出的原始值、对象、函数或类的语句。通常，导入是静态的，但也可以是动态的。

[TypeScript](https://www.typescriptlang.org/) 也采用了 ES6 模块。还有更多的东西需要导出和导入，比如接口、类型、名称空间和枚举。

出口和进口密切相关。下面我们举几个例子，看看如何写导出和导入语句。

# 导出和导入语句:示例 1

变量可以通过在每个声明前加上`export`来单独导出。

```
export let a, b, c, d, e, f, g, h;
export let i, j, k, l, m, n, o, p;
export let q;a = 1; // a primitive value of number
b = null; // a primitive value of null
c = undefined; // a primitive value of undefined
d = Symbol(); // a primitive value of Symbol
e = [1, 2, 3]; // an arrayf = { // a plain object
  a: 1,
  b: 2,
  c: 3
};g = Date; // a Date object
h = new Map(); // a Map object
i = new Set(); // a Set objectj = new Promise((resolve) => { // a Promise object
  resolve(5);
});k = function() { // an anonymous function
};l = function myFunction () { // a named function
};m = () => true; // an arrow functionn = function* myGenerator(i) { // a generator function
  yield i;
}o = async function myAsync() { // an async function
  await n;
}p = class { // a class with no class name
};q = class MyClass { // a class with a class name
};
```

通过指定变量名，可以有选择地导入这些导出:

```
import { a, b, c } from './myExports';
----------------------- or ---------------------
import { k } from './myExports';
----------------------- or ---------------------
import { a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q } from './myExports';
```

对于许多导入项，有一个选项可以将整个东西作为一个模块导入，然后使用模块名。

对于下面的导入语句，所有内容都作为`myImportModule`导入:

```
import * as myImportModule from './myExports';
```

然后代码可以使用导出的项目如`myImportModule.a`、`myImportModule.b`等。

# 导出和导入语句:示例 2

变量可以通过在每个带有赋值的声明前加上`export`来单独导出。

```
export var a = 1; // It is recommended to use let or const
                  // var is kept for backwards compatibility
export let b = null; // a variable initialized to null
export const c = undefined; // a constant variable
```

这些出口可以按如下方式导入:

```
import { a, b, c } from './myExports';
```

# 导出和导入语句:示例 3

函数和类的定义可以通过在它们的定义前面加上`export`直接导出。

```
export function myFunction() {}
export class myClass {}
```

这些出口可以按如下方式导入:

```
import { myFunction, myClass } from './myExports';
```

此外，TypeScript 支持导出类型、接口和枚举。

```
export type NumberOrString = number | string; // a type example, union is supported by type, but not by interfaceexport interface Base {} // a base interface exampleexport interface Child extends Base {} // an extended interface example, inheritance is supported by interface, not by typeexport enum DayOfWeek { // an enum example
  Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday
}
```

这些`exports`可以按如下方式导入:

```
import { NumberOrString, Base, Child, DayOfWeek } from './myExports';
```

TypeScript 具有`export =`语法。它指定从模块导出的单个对象。这可以是函数、类、接口、命名空间或枚举。下面是 React 如何被 [DefinitelyType](https://github.com/DefinitelyTyped/DefinitelyTyped) 导出。

```
export = React;
export as namespace React;
```

# 导出和导入语句:示例 4

变量可以作为列表导出。

```
const dayDefinitions = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
const daysOfWeek = dayDefinitions.length;
let currentDayIndex = 3;
export { dayDefinitions, daysOfWeek, currentDayIndex };
```

这些出口可以按如下方式导入:

```
import { dayDefinitions, daysOfWeek, currentDayIndex } from './myExports';
```

# 导出和导入语句:示例 5

变量可以作为列表导出。此外，列表中的项目可以重命名。新名称也称为别名。

```
const a = 1, c = undefined;
let b = null;
export { a as NUMBER_ONE, b as newNameForB, c as undefined_value};
```

这些出口可以按如下方式导入:

```
import { NUMBER_ONE, newNameForB, undefined_value } from './myExports';
```

此外，进口商可以用新的别名重命名该项目。以下是别名的别名示例:

```
import { NUMBER_ONE as myA, newNameForB as myB, undefined_value as myC} from './myExports';
```

# 导出和导入语句:示例 6

析构赋值也可以用于`export`。此外，可以有选择地重命名这些类型的导出。

```
let person1 = {
  name: 'John',
  age: 10,
  grade: 5
};let person2 = ['Lisa', 12, 7];// name is renamed to nameOfPerson1
// age is renamed to ageOfPerson1
export const { name: nameOfPerson1, age: ageOfPerson1 } = person1;// first item is renamed to nameOfPerson2
// second item is renamed to ageOfPerson2
export const [ nameOfPerson2, ageOfPerson2 ] = person2;
```

这些出口可以按如下方式导入:

```
import { nameOfPerson1, ageOfPerson1, nameOfPerson2, ageOfPerson2 } from './myExports';
```

# 导出和导入语句:示例 7

有两种类型的导出:默认导出和命名导出。每个模块在一个模块中最多可以有一个默认导出，但它可以有多个命名导出。

只有表达式、函数或类允许作为默认导出。默认导出的名称不是必需的。

从例 1 到例 6，所有的导出都被命名为`export`。这里，我们将看一些默认导出的例子。

```
export default 5;
----------------------- or ---------------------
const a = 5;
export { a as default };
----------------------- or ---------------------
const a = 5;
export default a;
----------------------- or ---------------------
let a;
export default a = 5;
----------------------- or ---------------------
export default function() {};
----------------------- or ---------------------
export default class {};
```

当默认导出被导入时，导入者可以给它起任何名字。

```
import { default as X } from './myExports';
----------------------- or ---------------------
import X from './myExports';
```

下面这句台词看着眼熟吗？

```
import React from 'react';
```

事实上，导入默认导出和其他项目可以混合使用。下面是另一句熟悉的台词吗？

```
import React, { useCallback, useState, useEffect } from 'react';
```

# 再出口的例子

模块可以通过新模块导出或部分再导出。

```
export * from './myExports'; // re-export everything
export {myFunction} from './myExports'; // partial re-export
```

重新导出将所有导出语句作为单个源一起聚合到一个新模块中。

如果我们有一个`reExports.js`定义如下:

```
export { a, b, c, d } from './myExports1';
export { e, f } from './myExports2';
export { g, h, i} from './myExports3';
```

它可以灵活地导入为:

```
import { a, b, c, d, e, f, g, h, i } from './reExports';
```

重新导出对 JavaScript 和 TypeScript 都有效。

一个小测验来结束事情:你知道为什么下面的代码抱怨没有定义`myFunction`吗？

```
export {myFunction} from './myExports';
myFunction();
```

这是因为再出口声明不使其在本地进口。以下代码将修复该问题:

```
import {myFunction} from './myExports';
export {myFunction} from './myExports';
myFunction();
```

感谢阅读。我希望这有所帮助。如果你有任何问题，请随时回复。你可以在这里看到我的其他媒体出版物。