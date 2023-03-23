# Master TypeScript 的类型保护

> 原文：<https://betterprogramming.pub/master-typescripts-type-guards-1fd5436bc6f2>

## 在 TypeScript 的 type guards 中从初学者到专业人员

![](img/36f31b56523b29c97087a49701ee4761.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

众所周知，TypeScript 是微软在 2012 年开发的一种语言。它是一个 JavaScript 超集，并且是动态类型的。因为它是动态类型的，所以类型守卫有时会发挥很大的作用。

# 基础知识

到底什么是类型守卫？它们是一套帮助我们缩小对象类型的工具。这意味着你正在从一个更一般的类型转向一个更具体的类型。它们最常见的用法是缩小联合类型。

有多种技术可以执行类型保护。选择正确的将取决于您的具体场景和个人偏好。我们将探索每一种技术及其注意事项。

您可以使用[TypeScript Playground app](https://www.typescriptlang.org/play)来运行本文中包含的所有代码片段。这是一个很好的工具，你可以用它来练习和提高你的打字技巧。

# 1.可空类型

让我们从最简单的开始。假设你有一个可以为空的类型。任何时候你在做一个`null`，你基本上是在做一个类型守卫。如果您已经在进行`null`检查，那么您已经在直观地使用类型保护:

理解类型保护如何处理条件块是非常重要的。在一个简单的条件块中(如上)，类型保护将只应用于它的内部。但是，如果您在条件类型保护内执行了一个`return`语句，TypeScript 会很聪明地从条件块外的`union`中删除该类型:

型守卫现在没那么恐怖了吧？

# **2。===而且！==运算符**

`typeof`是你日常打字使用的一个基本工具。它最常用于从值对象中提取 TypeScript 类型。它将值对象移植回 TypeScript 的类型。

不要混淆，因为 JavaScript 有自己的`typeof`操作符，它返回一个表示未赋值操作数类型的字符串。

```
// Typescript
const literal = 'literal type';
type literal = typeof literal; // literal = 'literal type'// Javascript
const literal = 'literal type';
typeof literal; // 'string'
```

用`typeof`提取类型的另一个例子:

```
interface Book {
  author: string;
}const b: Book = { author: 'jose granja'};type C = typeof b; // we have extracted the type Book from b
```

您可以将`typeof`与操作符`!==`或`===`结合起来执行类型保护。

让我们用一个`string | number`类型的联合入口参数创建我们自己的`fizzBuzz`。当变量是一个`string`时，我们将打印`Fizz`。当它是一个数字时，我们将打印`Buzz`:

没有`typeof`操作符，我们无法从`x`中提取类型。这就是它如此重要、基本和强大的原因。

# 3.实例 of

你可能对 JavaScript `instanceof`操作符很熟悉。这个操作符类似于`typeof`，但是它的使用主要集中在 ECMAScript 类上。右边的运算符需要是一个类。否则，我们会得到一个错误:

# 4.类型断言

这些是最手工的。它们基于开发人员对代码的了解。这就是为什么应该尽可能避免它们。然而，它们是你可以使用的工具，你应该意识到它们。

```
function addOne(x: unknown) {
  // devs knows x will always be a number
  return x as number + 1;
}
```

您可以使用`as`关键字手动更改变量类型。你可以从一个通用类型到一个更具体的类型。但是，如果这两种类型不相关，您需要先转换为`unknown/any`:

```
const x: number = 12;const a: string = x; // Type 'number' is not assignable to type 'string'const b: string = x as unknown as string; // Type 'number' was converted to 'string'
```

在上面的例子中，输入是错误的，因为我们将一个`number`赋值给一个`string`。编译器确实信任我们，即使它看起来是错误的，因为我们对此非常明确。这就是为什么要避免这种选角。

在上面的`addOne`示例中，如果我们正确地输入了函数 param，我们可以移除类型保护:

```
function addOne(x: number) {
 return x + 1; // no type guard needed
}
```

# 5.歧视工会

您可以使用`literal`类型与类型保护装置一起工作:

这看起来并没有那么花哨…但是让我们让`'hunter' | 'pray'`成为一些接口类型保护的关键。

让我们首先定义两个相似的接口，它们具有相同的`kind`属性，但是类型不同。这样，我们可以通过断言`kind`属性来知道哪个接口是调用者的类型。TypeScript 将自动区分联合类型，并在函数范围内分配正确的类型。

在 Typescript 中，它被称为`Discriminated Union`:您正在使用一个文字成员来区分联合成员。

我们可以通过在 TypeScript 中使用`enum`以一种更简洁的方式来做到这一点。受歧视的工会也与`enums`合作。这使得我们的代码不容易出现类型和打字错误。

# 6.用户定义的 Using 谓词

这些比之前的要复杂一点。您可以为任何给定的类型定义类似函数的断言。我们只需要定义一个返回类型为`type predicate`的函数，它返回`true/false`:

注意函数`isHunter`的返回类型是`x is Hunter`。这是因为它是一个断言函数，这也是它返回`true`或`false`的原因。

在代码块`isHunter(x)`中，`x`变量的类型为`Hunter`。这意味着我们可以安全地调用它的`hunt`方法。然而，在这个代码块之外，`x`类型仍然是`unknown`。

# 7.用户定义的使用 in 运算符

还有另一种方法，那就是使用`in`操作符。JavaScript 确实有一个类似的操作符，它的行为方式也类似。它的目的是检查接口/类型中是否存在给定的属性。

让我们重复前面的例子，但这次使用`in`:

我们可以通过检查方法`hide`或`hunt`的存在来区分`Hunter/Pray`类型。

何时使用这种方法与前一种方法将取决于代码风格和用例。

# 警卫和回调类型

类型守卫和回调有一个特殊的行为值得了解。让我们用一些例子来验证一下:

```
let x: string | number = "a";if (typeof x === "string") {
  console.log(x.split(' ')[0]); // x is of type string as expected
}
```

如果我们在回调中使用像`x`这样的可变变量，TypeScript 将不会考虑类型保护。它会将`x`视为`setTimeout`回调中的`string | number`，并因此抛出一个错误:

那是因为 TypeScript 不确定什么时候调用，而在那段时间内，`x`可以改变它的类型。TypeScript 不可能判断变量是否会改变它的类型，所以它给出一个错误作为预防措施:

如果变量不是可变的，这就不会发生。如果我们将其定义为`const x`，而不是`let x`，那么将不会抛出错误，因为该变量的值是密封的:

# 包扎

![](img/835429be15fd64d29622e7ecd214f73c.png)

瓦西里·科洛达在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我们已经看到了使用类型保护的不同方法。根据经验，你不应该滥用它们。

如果是，就要分析一下为什么这么经常需要它们。幕后可能有更大的问题在发生。它们只是为了在一些特定的场景中使用。

如果您想继续提高您的类型脚本技能，我写了一篇关于映射类型的文章，值得一看:

[](https://medium.com/better-programming/mastering-typescripts-mapped-types-5fa5700385eb) [## 掌握 TypeScript 的映射类型

### 了解如何像专家一样处理 Typescript 映射类型

medium.com](https://medium.com/better-programming/mastering-typescripts-mapped-types-5fa5700385eb) 

将来会有更多的 TypeScript 内容出现。干杯！