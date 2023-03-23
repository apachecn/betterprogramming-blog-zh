# 解释前 50 个打字稿面试问题

> 原文：<https://betterprogramming.pub/top-50-typescript-interview-questions-explained-5e69b73eeab1>

## 通过这些问题和答案，让你的打字稿编码面试变得轻松

![](img/8fc02020427f074b72fd444ab9eb8022.png)

图片来源:作者

TypeScript 是一个开源的 JavaScript 超集，由微软开发，用于在不破坏现有程序的情况下添加附加功能。TypeScript 现在被前端和全栈开发人员广泛用于大型项目，因为它有独特的好处，比如静态类型和许多速记符号。

今天，我们将通过涵盖 50 个顶级打字稿面试问题和答案来帮助您准备打字稿编码面试。

**下面是我们今天要讲的内容:**

*   一般打字稿问题
*   TypeScript 语法和语言基础
*   带有 JavaScript 问题的类型脚本
*   高级打字稿问题
*   还要练习 20 个打字稿问题
*   准备打字稿面试的技巧

# 一般打字稿问题

## 1.TypeScript 的主要特性是什么？

*   **跨平台**:TypeScript 编译器可以安装在任何操作系统上，包括 Windows、macOS、Linux。
*   **ES6 特性** : TypeScript 包含了计划中的 ECMAScript 2015 (ES6)的大部分特性，比如箭头函数。
*   面向对象语言:TypeScript 提供了所有标准的 OOP 特性，比如类、接口和模块。
*   **静态类型检查** : TypeScript 使用静态类型，并在编译时帮助进行类型检查。因此，您可以在编写代码时发现编译时错误，而无需运行脚本。
*   **可选静态类型**:如果你习惯 JavaScript 的动态类型，TypeScript 也允许可选静态类型。
*   **DOM 操作**:您可以使用 TypeScript 操作 DOM 来添加或删除客户端网页元素。

## 2.使用 TypeScript 有什么好处？

*   TypeScript 更具表现力，这意味着它的语法不那么混乱。
*   由于高级调试器专注于在编译前捕捉逻辑错误，所以调试很容易。
*   静态类型化使得 TypeScript 比 JavaScript 的动态类型化更容易阅读和结构化。
*   由于多才多艺的传输，它可以跨平台使用，在客户端和服务器端项目中都是如此。

## 3.TypeScript 的内置数据类型有哪些？

**数字类型**:用于表示数字类型值。TypeScript 中的所有数字都存储为浮点值。

```
let identifier: number = value;
```

**字符串类型**:表示存储为 Unicode UTF-16 代码的字符序列。字符串用单引号或双引号括起来。

```
let identifier: string = " ";
```

**布尔型**:保持`true`或`false`的逻辑二进制开关

```
let identifier: bool = Boolean value;
```

**Null 类型:** Null 表示值未定义的变量。

```
let num: number = null;
```

**未定义的类型:**未定义的文字，是所有变量的起点

```
let num: number = undefined;
```

**Void 类型:**分配给没有返回值的方法的类型

```
let unusable: void = undefined;
```

## 4.TypeScript 目前的稳定版本是什么？

目前的稳定版本是 4.2.3。

## 5.什么是 TypeScript 中的接口？

接口为使用该接口的对象定义了一个契约或结构。

接口是用关键字`interface`定义的，它可以包含使用函数或箭头函数的属性和方法声明。

```
interface IEmployee {
    empCode: number;
    empName: string;
    getSalary: (number) => number; // arrow function
    getManagerName(number): string; 
}
```

## 6.TypeScript 中的模块是什么？

TypeScript 中的模块是相关变量、函数、类和接口的集合。

您可以将模块视为包含执行任务所需的一切的容器。可以导入模块，以便在项目之间轻松共享代码。

```
module module_name{
class xyz{
export sum(x, y){
return x+y;
}
}
```

## 7.如何将 TypeScript 用于后端？

您可以将 Node.js 与 TypeScript 一起使用，以便将 TypeScript 的好处带到后端工作中。

只需通过输入以下命令将 TypeScript 编译器安装到 Node.js 中:

```
npm i -g typescript
```

## 8.TypeScript 中的类型断言是什么？

TypeScript 中的类型断言类似于其他语言中的类型转换，但没有 C#和 Java 等语言中可能有的类型检查或数据重构。类型断言对运行时没有影响，纯粹由编译器使用。

类型断言本质上是一种类型转换的软版本，它建议编译器将变量视为某种类型，但如果它是不同的形式，则不会强制它进入这种模式。

# TypeScript 语法和语言基础

## 9.如何在 TypeScript 中创建变量？

您可以通过三种方式创建变量:`var`、`let`和`const`。
`var`是旧文体中激烈划定范围的变量。你应该尽可能避免使用`var`，因为它会在大型项目中引起问题。

```
var num:number = 1;
```

`let`是在 TypeScript 中声明变量的默认方式。与`var`相比，`let`减少了编译时错误的数量，增加了代码的可读性。

```
let num:number = 1;
```

`const`创建一个常量变量，其值不能改变。它使用与`let`相同的作用域规则，有助于降低整体程序的复杂性。

```
const num:number = 100;
```

## 10.如何从 TypeScript 中的子类调用基类构造函数？

可以使用`super()`函数调用基类的构造函数。

## 11.解释如何使用 TypeScript 混合。

混合本质上是反向工作的继承。Mixins 允许您通过组合以前的类中更简单的部分类设置来构建新的类。

不是类`A`扩展类`B`来获得它的功能，而是类`B`从类`A`获取并返回一个具有额外功能的新类。

## 12.如何在 TypeScript 中检查 null 和 undefined？

你可以使用同时检查空值和未定义值的杂耍检查，也可以使用为空值返回`true`而不为未定义变量求值`true`的严格检查。

## 13.TypeScript 中的 getter/setter 是什么？你如何使用它们？

Getters 和 setters 是特殊类型的方法，可以根据程序的需要，帮助您委派对私有变量的不同级别的访问。

Getters 允许您引用一个值，但不能编辑它。Setters 允许你改变一个变量的值，但是看不到它的当前值。这些是实现封装所必需的。

例如，一个新的雇主可能能够`get`公司的雇员数量，但是没有权限`set`雇员数量。

## 14.如何允许模块中定义的类在模块外部被访问？

您可以使用`export`关键字来打开模块，以便在模块外部使用。

## 15.如何使用 Typescript 将字符串转换为数字？

与 JavaScript 类似，您可以使用`parseInt`或`parseFloat`函数分别将字符串转换为整数或浮点数。您还可以使用一元运算符`+`将字符串转换为最合适的数字类型，“3”成为整数`3`，而“3.14”成为浮点数`3.14`。

```
var x = "32";
var y: number = +x;
```

## 16.什么是. map 文件，为什么/如何使用它？

一个`.map`文件是一个源映射，它显示了原始的 TypeScript 代码是如何被解释成可用的 JavaScript 代码的。它们有助于简化调试，因为您可以捕捉任何奇怪的编译器行为。

调试工具也可以使用这些文件来允许您编辑基础类型脚本，而不是发出的 JavaScript 文件。

## 17.TypeScript 中的类是什么？你如何定义他们？

类代表一组相关对象的共享行为和属性。

例如，我们的类可能是`Student`，它的所有对象都有`attendClass`方法。另一方面，`John`是类型`Student`的一个单独实例，可能有类似`attendExtracurricular`的额外的独特行为。

使用关键字`class`声明类:

```
class Student {    
    studCode: number;    
    studName: string;    
    constructor(code: number, name: string) {    
            this.studName = name;    
            this.studCode = code;    
    }
```

# 带有 JavaScript 问题的类型脚本

## 18.TypeScript 和 JavaScript 有什么关系？

TypeScript 是编译成 JavaScript 的 JavaScript 的开源语法超集。所有原始的 JavaScript 库和语法仍然有效，但是 TypeScript 添加了 JavaScript 中没有的额外的语法选项和编译器特性。

TypeScript 还可以与大多数与 JavaScript 相同的技术接口，比如 Angular 和 jQuery。

## 19.打字稿里的 JSX 是什么？

JSX 是一种可嵌入的类似 XML 的语法，允许你创建 HTML。TypeScript 支持嵌入、类型检查和将 JSX 直接编译成 JavaScript。

## 20.TypeScript 支持哪些 JSX 模式？

TypeScript 内置了对`preserve`、`react`和`react-native`的支持。

*   `preserve`保持 JSX 完好无损，以供后续改造使用。
*   `react`不经过 JSX 变换，而是发出`react.createElement`并作为`.js`文件扩展名输出。
*   `react-native`结合了`preserve`和`react`，它保持所有 JSX 和输出作为`.js`扩展。

## 21.如何编译一个类型脚本文件？

你需要调用 TypeScript 编译器`tsc`来编译一个文件。您需要安装 TypeScript 编译器，这可以使用`npm`来完成。

```
npm install -g typescript
tsc <TypeScript File Name>
```

## 22.TypeScript 中有哪些作用域？这个和 JS 比怎么样？

*   **全局作用域**:定义在任何类之外，可以在程序中的任何地方使用。
*   **函数/类范围:**函数或类中定义的变量可以在该范围内的任何地方使用。
*   **局部作用域/代码块**:在局部作用域中定义的变量可以在该块中的任何地方使用。

# 高级打字稿问题

## 23.TypeScript 中的 arrow/lambda 函数是什么？

胖箭头函数是一种定义匿名函数的函数表达式的速记语法。类似于其他语言中的 lambda 函数。arrow 函数让您跳过`function`关键字，编写更简洁的代码。

## 24.解释 rest 参数和声明 rest 参数的规则。

Rest 参数允许您向函数传递不同数量的参数(零个或更多)。当您不确定一个函数将接收多少个参数时，这很有用。rest 符号`...`之后的所有参数都将存储在一个数组中。
例如:

```
function Greet(greeting: string, ...names: string[]) {
    return greeting + " " + names.join(", ") + "!";
}Greet("Hello", "Steve", "Bill"); // returns "Hello Steve, Bill!"Greet("Hello");// returns "Hello !"
```

rest 参数必须是参数定义中的最后一个，并且每个函数只能有一个 rest 参数。

## 25.什么是三斜线指令？什么是三斜线指令？

三斜线指令是单行注释，包含用作编译器指令的 XML 标记。每个指令都指示在编译过程中要加载什么。三斜线指令只在文件的顶部起作用，在文件的其他地方将被视为普通的注释。

*   `/// <reference path="..." />`是最常见的指令，定义了文件之间的依赖关系。
*   `/// <reference types="..." />`类似于`path`,但是定义了一个包的依赖关系。
*   `/// <reference lib="..." />`允许您显式包含内置的`lib`文件。

## 26.`Omit`型是做什么的？

`Omit`是实用类型的一种形式，有助于常见的类型转换。`Omit`允许您通过传递当前`Type`并选择新类型中要省略的`Keys`来构造类型。

```
Omit<Type, Keys>
```

例如:

## 27.如何在 TypeScript 中实现函数重载？

要在 TypeScript 中重载一个函数，只需创建两个同名但参数/返回类型不同的函数。两个函数必须接受相同数量的参数。这是 TypeScript 中多态性的一个重要部分。

例如，您可以创建一个`add`函数，如果这两个参数是数字，就将它们相加，如果它们是字符串，就将它们连接起来。

## 28.如何使一个接口的所有属性都是可选的？

您可以使用`partial`映射类型轻松地使所有属性可选。

## 29.什么时候应该使用关键字 unknown？

如果你不知道先要哪种类型，但想稍后再分配，你应该使用`unknown`，而`any`关键字不起作用。

## 30.什么是装修工，可以应用到哪些方面？

装饰器是一种特殊的声明，通过用`@<name>`注释标记类或类成员，可以让你一次修改所有的类或类成员。每个 decorator 必须引用一个将在运行时被求值的函数。

例如，装饰器`@sealed`将对应于`sealed`函数。任何标有`@sealed`的都将用于评估`sealed`功能。

```
function sealed(target) {
  // do something with 'target' ...
}
```

它们可以连接到:

*   类声明
*   方法
*   访问者
*   性能
*   因素

注意:默认情况下，装饰器是不启用的。要启用它们，您必须从您的`tsconfig.json`文件或命令行编辑编译器选项中的`experimentalDecorators`字段。

# 还要练习 20 个打字稿问题

*   31.TypeScript 中成员变量和方法的默认访问修饰符是什么？
*   32.什么时候应该使用`declare`关键词？
*   33.TypeScript 中的泛型是什么？你什么时候会使用它们？
*   34.您将如何以及何时使用`enum`系列？
*   35.什么是名称空间，为什么要使用它们？
*   36.如何实现可选参数？
*   37.说出 TypeScript 和 JavaScript 的三个不同点。
*   38.TypeScript 是函数式编程语言吗？
*   39.哪些 TypeScript 特性对全栈开发人员有益？
*   40.打字稿语言服务(TSLS)有什么优势？
*   41.TypeScript 提供了哪些特性来帮助制作可重用的组件？
*   42.TypeScript 中的元组和数组有什么区别？
*   43.TypeScript 中内部模块和外部模块有什么区别？
*   44.TypeScript 支持哪些集合？
*   45.`Record`型是做什么用的？
*   46.TypeScript 给技术栈带来了什么优势？
*   47.如何使用 TypeScript 生成定义文件？
*   48.TypeScript 支持抽象类吗？
*   49.如何将 TypeScript 文件设置为每当有变化时就进行编译呢？
*   50.TypeScript 中的`Ambients`是什么，应该在什么时候使用？

# 准备打字稿面试的技巧

准备面试总是有压力的，但是事先适当的准备可以帮助你建立自信。准备面试时，请记住:

*   实践动手解决问题，而不仅仅是阅读它们。
*   把你的学习材料分成几个部分，每一部分之后进行自我测验。
*   准备你的行为面试，这和编码部分同样重要。

*快乐学习！*