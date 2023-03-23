# 模拟打字面试

> 原文：<https://betterprogramming.pub/simulating-a-typescript-interview-cdaf9d217dff>

## 知道你的下一次面试是什么样子，从而领先一步

![](img/94b88da969bcb3e064a55600e970aee9.png)

照片由 [XPS](https://unsplash.com/@xps?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

前端面试一天比一天难。做好充分准备是关键。随着对打字稿的需求与日俱增，这个话题肯定会在你的面试中出现。

本文是打字稿面试的模拟。即使你不是在找新工作，你也可以用它来测试你的打字知识。面试分为两部分:

*   第一部分:五个打字稿问题
*   **第二部分**:六个现场编码练习

# 第 1 部分:打字稿一般问题

这些打字稿问题只是热身——现场编码前的初始对话。

## 1.TypeScript 和 JavaScript 有什么区别？

Typescript 是 Javascript 的超集。这意味着所有在 Javascript 上运行的代码都将在 TypeScript +一些其他特性上运行。然而，浏览器无法理解类型脚本代码——它需要传递给 JavaScript。

JavaScript 是弱类型，而 Typescript 是强类型。

## 2.TypeScript 有什么好处？

使用 Typescript 有很多好处:

*   类型检查使您的代码更加安全。
*   类型检查使您的代码更具可读性。
*   因为它是可选类型的，所以您可以对一些边缘场景或第三方库禁用类型。
*   适用于所有现有的 JavaScript。
*   巴别。
*   捕捉大多数空检查错误。

## 3.是什么让 TypeScript 可选类型化？

有三种简单的方法可以使 TypeScript 成为可选类型。它们应该仅在边缘情况下使用:

*   使用`any`关键字——你告诉编译器这个变量将接受任何类型。
*   `@ts-ignore`这将抑制下一行的错误

```
if (false) {
  // @ts-ignore: Unreachable code error
  console.log("hello");
}
```

*   `@ts-expect-error`类似于`@ts-ignore`，但是如果不必要的话会抛出一个错误。

## 4.什么是 TypeScript Declare 关键字？

`Declare`用于告诉编译器在另一段代码上声明了什么。这在使用第三方库时特别有用。它还告诉编译器在传输 TypeScript 时不要包含这些代码。

```
interface Interface {
  run: () => void;
}declare let plugin: Interface;
```

检查处的 TypeScript playground [以查看`plugin`的代码输出为 none。](https://www.typescriptlang.org/play?ssl=6&ssc=1&pln=1&pc=1#code/JYOwLgpgTgZghgYwgAgJLmvJyDeAoZZKAVxAC5kAKASmQF4A+ZANwHtgATAbjwF888HCAgA2cKChEQwyAA4jiAc1AV0kWIgg8gA)

## 5.如何将 TypeScript 配置成严格的？

通过在`ts-config.json`文件中添加`strict: true`到`compiler`选项，可以启用 TypeScript 严格模式。

转动`strict: true`将所有这些标志变为真:

*   `strictNullChecks`
*   `noImplicitAny`
*   `noImplicitThis`
*   `alwaysStrict`

万一你这次失败了，在这里检查我的打字稿`strict mode`文章[。](https://medium.com/better-programming/getting-strict-with-typescript-2e906b48c0a)

# 第 2 部分:编码生命周期

为了进行适当的模拟，给自己 25 分钟来完成所有六个练习。设置一个计时器，确保你不会被打扰。每个练习都有一个到 TypeScript 操场的链接，你可以在那里练习。

在编码之前，确保你理解你被要求做什么。面试的时候问澄清问题是可以的。

## 练习 1

给出如下界面:

```
interface Point3D {
  x: number;
  y: number;
  z: number;
}
```

如何创建一个等同于`Point3D`但没有`z`属性的接口`Point2D`？

打字操场[这里](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgAoHtRgMwBFkDeAUMqcgB4BcyIArgLYBG0A3CWQJ7V1OvukAvbg2ZQ2AXyJA)。

有多种解决方案—都是使用 TypeScript 映射类型实用工具实现的。如果你不了解映射类型，可以在这里查看我的文章[。](https://medium.com/better-programming/mastering-typescripts-mapped-types-5fa5700385eb)

**解决方案 1:**

```
type Point2D = Omit<Point3D, 'z'>;
```

**解决方案 2:**

```
type Point2D = Pick<Point3D, 'x' | 'y'>;
```

**解决方案 3:**

```
type Point2D = {
  [T in Exclude<keyof Point3D, 'z'>]: Point3D[T]
}
```

`solution 1`是最好的，如果你添加另一个成员到接口，它将被添加到`Point2D`。那不会发生在`solution 2`上。`Solution 3`有点矫枉过正，但是有效。

## 练习 2

给定前面的`Point3D`界面和该功能:

```
interface Point3D {
  x: number;
  y: number;
  z: number;
}function rotateX(x: number, offset: number) {
  return x + offset;
}
```

如何确保函数参数依赖于`Point3D`成员`x`的类型，而不是使用`number`，这样如果`Point3D`成员`x`的类型改变，函数参数类型也会改变？

这里有操场。

**解决方案 1**

您可以创建一个类型，然后在`interface`和`function`中使用它。这是不鼓励的，但它确实有效:

```
type X = number;interface Point3D {
  x: X;
  y: number;
  z: number;
}function rotateX(x: X, offset: X) {
  return x + offset;
}
```

**解决方案 2**

最佳解决方案是使用 TypeScript 的属性访问器。就像在 JavaScript 中，你可以用括号符号访问类型/接口变量。

```
function translateX(x: Point3D['x'], offset: Point3D['x']) {
  return x + offset;
}
```

## 练习 3

给定以下函数及其三个不同的调用:

```
// how you make this function accept any type wihout using any or unkownfunction fun(variable: string) {
  return variable;
}const result1 = fun('string');
const result2 = fun(true);
const result3 = fun(new Date());
```

如何让这个函数适用于所有三种不同的类型以及任何其他类型？

这里有游乐场。

**解决方案**

这里的解决方案是使用 Typescript 的特性:泛型。通过使用通用接口，您可以确保它对所有三种情况都有效。

## 练习 4

如何防止前面看到的函数用数组类型执行？

```
function fun<T>(variable: T) {
  return variable;
}const result1 = fun('string');
const result2 = fun(true);
const result3 = fun(new Date());
const result4 = fun(['one','two','three']); // should fail here
```

这里有游乐场。

**方案一**

最直观的做法是使用`extends`关键字将`T`限制为某些类型:

```
function fun<T extends string | boolean | Date>(variable: T) {
  return variable;
}
```

这有一个明显的缺点:你必须事先知道类型。

**方案二**

最优雅的解决方案是使用映射类型和`never`关键字。当类型为数组时，将类型`T`赋给`never`会使函数不可调用。无法满足类型为`never`的参数，因此它将中断。

注意:这些不起作用——那里不允许条件句。

```
function fun<T extends unknown[] ? never : T> ... // will complain abount synthaxisorfunction fun<T extends NotArray<T>> ... // will complain about recursivity
```

## 练习 5

```
const items = ['string', true, new Date()];const firstItem = items[0];
```

你能通过看上面的代码猜出`firsItem`变量吗？

如果类型不精确，如何确保它匹配数组索引类型？

操场[这里](https://www.typescriptlang.org/play?#code/MYewdgzgLgBAllApgWwjAvDA2gcmgJzjAHMcAaGKfAV0QrEQHcYARAQyQAoBKAXQG4AUINCRYAMzj5oASSTIM8eRCwAGAUA)。

**解决方案**

`firsItem`变量的类型是什么？由于数组是动态的，`firstItem`的类型是`string | boolean | date`。

然而，如果您在启用了`noUncheckedIndexedAccess`标志的情况下使用 Typescript `4.1`，则类型将是`string | boolean | date | undefined`。该标志将`undefined`作为一种可能的类型添加到动态数组中。

我们如何确保类型与索引一致？我们必须固定数组，这样 TypeScript 才能确定值类型。

方法是使用`as const`。这将把数组变成 TypeScript 的元组:

```
const items = [‘string’, true, new Date()] as const;const firstItem = items[0]; // firstItem is of type string
```

另一种不太优雅的方法是直接使用`Tuple`:

```
const items: [string, true, Date ] = [‘string’, true, new Date()];const firstItem = items[0]; // firstItem is of type string
```

## 练习 6

让我们尝试一个稍微复杂一点的问题:

我们如何修复上面的代码，以便通过向`if`语句添加一个条件，TypeScript 编译器就可以推导出正确的类型`Dog`或`Cat`？您可以为此更改`Animal`、`Dog`和`Cat`界面。

打字操场[这里](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgIImAWzgG2QbwChkTk4BzCALmRAFdMAjaAbkIF9DDRJZEUAIgHtyyCAA9IIACYBnNBmx4ipZAjrTpOasgAUASmQBeAHzIAbkODS2nbuGjwkyAMJwwYyRBnz0WXATEpLLaEAAONAbGZpbWtlwA9AnIABZCAO7I6UJ0ONLIAJ45yDDA4shgKcDymBCVQtIA-IQwdCAIYMBCIMhhjkJQmKgdXSC6cIq4NMKiAD6u7oYqpMAwegkAVBvIGwlLQaokE-44AHTqmtoGbKp2qqvrWzt7gYekx0qnIRDh1wfInE4QA)。

## 解决方案 1

第一个直观的解决方案是使用 TypeScript 类型的保护。我们可以为每个接口创建谓词，这是一个完全可以接受的解决方案:

缺点:我们必须为每个新接口添加一个方法，这将增加很多样板文件。

## 解决方案 2

这个问题的最佳解决方案是使用有区别的联合。注意，`animal`的类型是暗示解决方案的`Dog | Cat`。我们必须定义一个`type`属性，然后用它来区分两种类型:

通过使用`switch`语句和`Enums`代替文字类型，可以进一步改进这段代码:

有了`Enums`和`switch`语句，一切都更好看了。避免在一个函数中使用多个`if`语句看起来总是更干净。当大量使用时，背后可能有一个你可以使用的模式。

# 结论

面试有时非常具有挑战性。重要的是不要紧张，并努力理解要求你做的任何事情。通过练习上面的编码练习，你会有充分的准备。

你做对了多少个现场编码练习？

*   三个正确的练习:你是一个优秀的 TypeScript 开发人员，但是还有很大的改进空间。
*   四个练习对:你是一个中级的 Typescript 开发人员。
*   五个练习对:你是一个高级 Typescript 开发人员。
*   六个练习对了:你是打字高手。

让我知道你是否觉得这个测试太简单或太有挑战性，以及你是否找到了其他方法来解决这些练习。随时欢迎反馈。

在不久的将来，我会制作更多的打字稿内容。干杯！

# 相关文章

[](https://medium.com/better-programming/typescript-4-1s-advanced-mapped-types-eba9a2ba7a9) [## TypeScript 4.1 的高级映射类型

### 看看递归条件类型、模板文字类型等等

medium.com](https://medium.com/better-programming/typescript-4-1s-advanced-mapped-types-eba9a2ba7a9) [](https://medium.com/better-programming/7-must-know-typescript-transpiled-features-e2365cce17e2) [## 7 个必须知道的打字稿翻译功能

### 将最新的 ECMAScript 功能与 Typescript 一起使用

medium.com](https://medium.com/better-programming/7-must-know-typescript-transpiled-features-e2365cce17e2) [](https://medium.com/better-programming/master-typescripts-type-guards-1fd5436bc6f2) [## Master TypeScript 的类型保护

### 在 TypeScript 的 type guards 中从初学者到专业人员

medium.com](https://medium.com/better-programming/master-typescripts-type-guards-1fd5436bc6f2)