# 如何使用 TypeScript 避免 bug

> 原文：<https://betterprogramming.pub/how-to-use-typescript-to-avoid-bugs-3d760435e243>

## 使用 TypeScript 来避免未定义和类型错误等常见错误

![](img/b61ed9862fb7bfc5207c8bf72351bbef.png)

由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 是 JavaScript 的超集，创建它是为了解决 JavaScript 的常见问题。

JavaScript 的一个问题是所有对象都有动态类型。这意味着，如果不将对象记录到调试器中，就无法知道它具有哪些属性。

这造成了很多挫折，因为您必须亲自检查每个对象，从而减慢了开发速度。没有静态类型，你也不能在你的编辑器中自动完成，因为没有办法 100%确定地知道那些对象中有什么。

静态类型是可选的，所以您仍然可以使用对象作为散列。

# 类型检查

此外，您可以将任何参数放入 JavaScript 函数中，这带来了与动态类型相同的困难，因为没有强制执行传入的内容。

这就产生了问题，因为你应该传入但没有传入的参数将是未定义的，然后你会得到未定义的错误。也没有什么可以阻止你传入太多的参数。

通过这两种检查，TypeScript 使代码更容易理解和遵循。当你改变代码的时候，你不必担心破坏东西，因为编译器会告诉你，你得到了那些基本的错误。

此外，依赖注入是 TypeScript 的一部分，这意味着您不必自己解析依赖。这也使得模仿依赖关系易于测试。

TypeScript 在即将发布的 JavaScript 版本(尚未最终确定)中提供了一些特性，这些特性可能对一些开发人员来说很方便。

TypeScript 通过为对象定义类型文件来向对象添加类型。它的工作原理是建立一个模块，然后包含一个`module.d.ts`文件。

这方面的一个例子是在[类型脚本文档](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-d-ts.html)中。

```
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>/*~ This is the module template file. You should rename it to index.d.ts
 *~ and place it in a folder with the same name as the module.
 *~ For example, if you were writing a file for "super-greeter", this
 *~ file should be 'super-greeter/index.d.ts'
 *//*~ If this module is a UMD module that exposes a global variable 'myLib' when
 *~ loaded outside a module loader environment, declare that global here.
 *~ Otherwise, delete this declaration.
 */
export as namespace myLib;/*~ If this module has methods, declare them as functions like so.
 */
export function myMethod(a: string): string;
export function myOtherMethod(a: number): number;/*~ You can declare types that are available via importing the module */
export interface someType {
    name: string;
    length: number;
    extras?: string[];
}/*~ You can declare properties of the module using const, let, or var */
export const myField: number;/*~ If there are types, properties, or methods inside dotted names
 *~ of the module, declare them inside a 'namespace'.
 */
export namespace subProp {
    /*~ For example, given this definition, someone could write:
     *~   import { subProp } from 'yourModule';
     *~   subProp.foo();
     *~ or
     *~   import * as yourMod from 'yourModule';
     *~   yourMod.subProp.foo();
     */
    export function foo(): void;
}
```

根据网站上的示例，您可以将顶级属性定义为一行代码。并且，嵌套属性可以通过使用关键字`namespace`来定义。

# 组合类型

您不必在 TypeScript 中使用单一类型。多个类型可以合并成一个。交集类型是将多种数据类型组合成一种的数据类型。

例如:

```
function(foo: Foo, bar: Bar): Foo & Bar {
    const result: Partial<Foo & Bar> = {};
    for (const prop in foo) {
        if (foo.hasOwnProperty(prop)) {
            (result as Foo)[prop] = foo[prop];
        }
    }
    for (const prop in bar) {
        if (bar.hasOwnProperty(prop)) {
            (result as Bar)[prop] = bar[prop];
        }
    }
    return result as Foo& Bar;
}
```

具有组合类型的对象从两种类型获取属性。

联合类型是一种数据类型，其中对象可以是一种类型，也可以是另一种类型。例如:

```
let foo: number|string;
```

在该声明之后，您可以将一个数字或一个字符串赋给变量，TypeScript 编译器不会抛出错误。

TypeScript 允许静态类型，同时还允许灵活性。

在 TypeScript、ES6 或更高版本中，这些功能都可用。`let`和`const`应该用来避免与`var`发生范围冲突。

您还可以获得新类型的元组和枚举。您可以通过执行以下操作来定义元组，即具有固定数量的元素和类型的数组:

```
let x: [string, number] = ["hello", 1]; 
```

如果将`x`设置为`[1, “hello”]`，TypeScript 编译器将会失败。

您可以为常量列表定义一个`enum`类型。例如:

```
enum Color {Red, Green, Blue} 
let green: Color = Color.Green;
```

数组在 TypeScript 中可以有固定的类型。例如，为了定义一个数字数组，我们把:

```
let x: number[] = [1,2,3];
```

同样，您可以像这样分配数组:

```
**let** x: Array<number> = [1, 2, 3];
```

如果将`x`设置为`['1','2','3']`，编译将会失败。

TypeScript 还向属性和方法添加了访问修饰符。您可以选择将属性和方法设置为`public`、`private`或`protected`。

`public`属性和方法对所有外部类都可用。`protected`属性和方法对子类和当前类可用。`private`属性和方法仅在类中可用。

例如:

```
class classA {
  public publicProp: number;
  private privateProp: number;
}class classB{
  public a: classA;
}let b: classB = new classB();
b.a: classA = new classA();
b.a.publicProp = 1; // OK
b.a.privateProp = 1; // error
```

最后一行将给出一个错误，因为`privateProp`对外部类不可用。

要在项目中包含 TypeScript，可以在 JavaScript 项目文件夹中运行以下内容:

```
$ npm install typescript --save-dev 
```

这些是 TypeScript 提供的一些基本而重要的优势。有了这些特性，它将节省您开发 JavaScript 应用程序的大量时间！