# TypeScript 中枚举的完全指南

> 原文：<https://betterprogramming.pub/a-complete-guide-to-enums-in-typescript-6f76da009146>

## 在反应式工作环境中对枚举的深入探讨

![](img/1a12cf0ddcc35bd6a11025eac94b8650.png)

照片由 [Courtney Baucom](https://unsplash.com/@courtneybaucom?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在计算机编程中，[枚举类型](https://en.wikipedia.org/wiki/Enumerated_type)是由一组命名值组成的数据类型，这些命名值称为该类型的元素、成员、枚举数或枚举数。在 Typescript 中，枚举类型使用关键字`[enum](https://www.typescriptlang.org/docs/handbook/enums.html)`，该关键字定义了一组组织在集合中的命名常数。

为了方便起见，我们将使用 React 工作环境来探索枚举。示例并不局限于 React，它们还可以应用于其他 TypeScript 工作环境。

# 使用类型脚本创建 React 应用程序

我们已经使用基于 JavaScript 的 Create React 应用程序探索了许多功能和库。这次为什么要用 TypeScript？

JavaScript 不支持枚举，尽管`enum`是 JavaScript 中的保留字。更准确地说，[JavaScript enum](https://github.com/rbuckton/proposal-enum)正处于提案阶段。

TypeScript 枚举为字符串或数字提供结构化包装。

安装 [Create React App](/upgrade-create-react-app-based-projects-to-version-4-cra-4-d7962aee11a6) :

```
npx create-react-app react-enum --template typescript
cd react-enum
```

执行`npm start`，基于 TypeScript 的 Create React 应用程序启动并运行。

# 数字和字符串枚举

我们用关键字`enum`定义枚举，后跟枚举名称和枚举成员，它们是括号中的常量名称。

```
enum Name {Constant1, Constant2, ...};
```

按照惯例，枚举名和常量名都是 Pascal 大小写，每个复合词的第一个字母都是大写的。

下面是一个包含四种水果的`Fruit`枚举的例子:

```
enum Fruit {
  Apple,
  Orange,
  Peach,
  Pear,
}
```

默认情况下，枚举为数字枚举，即`Fruit.Apple`为 0、`Fruit.Orange`为 1、`Fruit.Peach`为 2、`Fruit.Pear`为 3。

该数字也可以自由初始化:

```
enum Fruit {
  Apple = 5,
  Orange = -1,
  Peach,
  Pear,
}
```

那么`Fruit.Apple`就是 5，`Fruit.Orange`就是`-1`，`Fruit.Peach`就是 0，`Fruit.Pear`就是 1。

为了更好的可读性，枚举可以定义为字符串。字符串枚举必须用字符串值初始化。

```
enum Color {
  Green = 'Green',
  LightGreen = 'Light Green',
  White = 'White',
}
```

在这种情况下，`Color.Green`就是`'Green'`，`Color.LightGreen`就是`'Light Green'`，`Color.White`就是`'White'`。

还有混合了数值和字符串值的异类枚举。

```
enum Task {
  Name = 'Todo List',
  Assigned = 0,
  InProgress,
  Finished
}
```

有了这个定义，`Task.Name`就是`'Todo List'`，`Task.Assigned`就是 0，`Task.InProgress`就是 1，`Task.Finished`就是 2。

用以下代码修改`src/App.tsx`:

第 1–6 行定义了`Fruit`枚举，显示在第 24–28 行。enum 是一个对象(第 24 行)，带有常量名和数值的键，`{"0":"Apple","1":"Orange","2":"Peach","3":"Pear","Apple":0,"Orange":1,"Peach":2,"Pear":3}`。因此，我们可以使用对象映射(`Fruit.Apple`或 `Fruit.['Apple']`)和反向映射(`Fruit[0]`)。

第 8–12 行定义了`Color`枚举，显示在第 29–32 行。它是一个对象，`{"Green":"Green","LightGreen":"Light Green","White":"White"}`。我们可以看到字符串枚举成员没有反向映射。

第 14–19 行定义了`Task`枚举，显示在第 33–37 行。它是一个物体，`{"0":"Assigned","1":"InProgress","2":"Finished","Name":"Todo List","Assigned":0,"InProgress":1,"Finished":2}`。它是一个异类枚举，只有数值成员具有反向映射。

运行该应用程序，它会在浏览器上显示以下输出:

没有 bowsers 直接支持 TypeScript。[代码可以转换成 JavaScript(更确切地说是 ES6)，在所有主流浏览器上运行，除了 IE (ES5)](https://www.typescriptlang.org/tsconfig#target) 。枚举被转换为以下 JavaScript 代码:

# 常量枚举表达式

枚举成员用常量枚举表达式初始化，这些表达式可以在编译时完全计算。以下是有效的表达式:

*   数值或字符串的文字枚举表达式

```
enum BooleanValue {
  Yes = 1,
  No = 0,
}enum Result {
  Right = 'Right',
  Wrong = 'Wrong',
}
```

*   以常量枚举表达式为操作数的`+`、`-`、`*`、`/`、`<<`、`>>`、`>>>`、`&`、`|`、`^`二元运算符产生的值

```
enum AccessCode {
  None = 1 << 0,
  Read = 1 << 1,
  Write = 1 << 2,
}
```

*   对以前定义的常量枚举成员的引用

```
enum AccessCode {
  None = 1 << 0,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadOrWrite = Read | Write,
}enum Permission {
  NotAllowed = 0,
  Allowed = AccessCode.ReadOrWrite,
}
```

*   带括号的常量枚举表达式

```
enum Values {
  NumberValue = (2 * 3),
  StringValue = ('Today' + 'Tomorrow'),
}
```

*   应用于常量枚举表达式的`+`、`-`、`~`一元运算符之一

```
enum ThreeOps {
  Plus = +1,
  Minus = -1,
  Tilde = ~1,
}
```

*   函数调用

```
const add3 = (value: number) => value + 3;
enum ComputedValues {
  V1 = add3(5),
  V2 = add3(7),
}
```

让我们将这些枚举放入`src/App.tsx`中，看看它们是如何工作的:

第 1–4 行定义了`BooleanValue`枚举，显示在第 43–45 行。

第 6–9 行定义了`Result`枚举，显示在第 46–48 行。

第 11–16 行定义了`AccessCode`枚举，显示在第 49–53 行。

第 18–21 行定义了`Permission`枚举，它显示在第 54–56 行。

第 23–26 行定义了`Values`枚举，它显示在第 57–59 行。

第 28–32 行定义了`ThreeOps`枚举，显示在第 60–63 行。

第 34 行定义了一个函数表达式`add3`。

第 35–38 行定义了`ComputedValues`枚举，显示在第 64–66 行。

运行该应用程序，它会在浏览器上显示以下输出:

枚举被转换为以下 JavaScript 代码:

# 枚举类型

定义的枚举是 TypeScript 中的一种类型。此外，枚举成员也可以用作类型。

来看看下面这个`src/App.tsx`:

第 1–9 行定义了一个枚举，`Day`。它在第 23 行输出类型为`object`。

`Day`用作类型，在第 10–13 行定义常量变量。这些变量实际上有数字类型(第 25–28 行)。

`Day`用作类型，在第 14 行定义函数表达式。该函数在第 29–32 行被调用。

第 15 行使用枚举成员`Day.Sunday`来定义类型`number`(第 33 行)，实际值(第 34 行)超出了枚举值。

作为比较，第 16 行和第 17 行不能使用 defined 变量来定义类型。

运行该应用程序，它会在浏览器上显示以下输出:

枚举的行为类似于对象。枚举成员可以通过点符号或括号符号来引用。例如，`Day.Sunday`和`Day['Sunday']`可以互换使用。

运行以下`src/App.tsx`:

第 14 行显示编译错误，`Element implicitly has an 'any' type because index expression is not of type 'number'.ts`。

如果我们使用枚举括号表示法，索引表达式必须是有效的枚举成员。如果我们使用`Day[key as 'Sunday' | 'Monday' | 'Tuesday' | 'Wednesday' | 'Thursday' | 'Friday' | 'Saturday']`它将会工作。其实`Day[key as 'Sunday']`也行。

然而，更好的方法是使用`keyof`关键字。`keyof typeof`获取将所有枚举键表示为字符串的类型，即`'Sunday' | 'Monday' | 'Tuesday' | 'Wednesday' | 'Thursday' | 'Friday' | 'Saturday'`。

下面是通过编译的代码:

运行它，我们会在浏览器上看到对象映射和反向映射值:

如何只获取枚举键？

如何只获取枚举值？

如何获取枚举长度？

# 枚举对象

枚举是运行时的对象。枚举可以传递给函数。

这是改装过的`src/App.tsx`:

第 11 行是一个函数表达式，它将一个对象作为参数。它与枚举`Day`无关，只不过它们都有键`Wednesday`。

用`Day`调用函数(第 16 行)。它在浏览器上产生以下输出:

枚举是对象，但是生成的代码有点复杂。`Day`枚举被转换成以下 JavaScript 代码:

从 TypeScript 3.4 开始，`[const](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions)` [断言被引入](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions)。它的语法是一个类型断言，使用`as const`就足够了，例如`123 as const`。当文字表达式用`const`断言构造时，它意味着:

*   表达式中的任何文本类型都不应被加宽。
*   对象文字获取`readonly`属性。
*   数组文字变成了`readonly`元组。

我们可以用`src/App.tsx`中的`const`断言创建一个对象。

它没有反向映射。运行它，我们会在浏览器上看到对象映射值:

这是生成的 JavaScript 代码:

它不是枚举，但可以像枚举一样使用。生成的代码很简单，类似于原始的 TypeScript 代码。因此，使用`const`断言是 TypeScript 枚举的一个可行替代方案。

# 结论

我们已经研究了 React 工作环境中的类型脚本枚举。以下是总结:

*   有数字和字符串枚举，也有混合类型的异构枚举。
*   枚举成员使用各种常量枚举表达式。
*   枚举和枚举成员都可以用作类型。
*   枚举是运行时的对象，枚举可以传递给函数。
*   使用 const 断言是 TypeScript 枚举的一种可行的替代方法。

感谢阅读。我希望这有所帮助。如果你有兴趣，可以看看[我的其他媒体文章](https://jenniferfubook.medium.com/jennifer-fus-web-development-publications-1a887e4454af)。