# 类型脚本泛型介绍

> 原文：<https://betterprogramming.pub/introduction-to-typescript-generics-functions-8ebfe688fd61>

## 创建可以跨类型使用的泛型函数

![](img/a7afdeb8621dbad24f12f060a9410bf4.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

创建可重用代码的一种方法是创建这样的代码，它允许我们根据自己的需要使用不同的数据类型。 [TypeScript](https://www.typescriptlang.org/) 提供了创建可重用组件的泛型结构，我们可以在一段代码中处理各种类型。这允许用户通过放入他们自己的类型来使用这些组件。

在本文中，我们将研究定义泛型函数的许多方法，在这些方法中，我们可以设置参数类型和返回值，以避免重新定义具有相同逻辑但不同参数和返回值类型的函数。

# 定义通用函数

泛型的一个基本用途是创建逻辑相同但参数类型和返回类型不同的函数。例如，如果我们想创建一个 identity 函数，只返回传入的相同内容，我们可以编写如下代码:

```
function echo(arg: number): number {
  return arg;
}
```

如果我们希望这个函数接受多个类型作为参数，并返回传入的相同类型，那么我们可以通过放入一个泛型类型标记，而不是放入用于`arg`参数和返回类型的`number`类型，将它变成一个泛型函数。我们用`T`关键字来表示一个类型变量。`T`关键字允许我们稍后为通用函数捕获组。我们可以为`T`切换出`number`，如下面的代码所示:

```
function echo<T>(arg: T): T {
  return arg;
}
```

当我们调用该函数时，我们可以编写如下代码:

```
console.log(echo<number>(1));
console.log(echo<string>('string'));
console.log(echo<boolean>(true));
```

我们得出以下结论:

```
1
string
true
```

使用上面的代码，我们可以在每个函数调用中对传入的参数和返回类型进行类型检查。`<>`中的代码表示我们希望参数成为的类型，以及函数将接受的参数和返回类型的返回类型。我们不必像上面那样显式地输入类型。只要我们定义了泛型函数，TypeScript 就足够智能来识别类型。例如，假设我们编写以下代码来调用`echo`函数:

```
console.log(echo(1));
console.log(echo('string'));
console.log(echo(true));
```

代码仍将编译、运行和输出与上面相同的内容。这使代码更短，但显式设置类型对开发人员来说更清晰。此外，编译器有时可能无法识别类型，尤其是当代码更复杂时。在这种情况下，必须提供类型。

如果我们想要访问对象的特定属性，那么我们必须更加具体地使用泛型类型。例如，如果我们假设参数的类型和返回类型总是某种数组，那么我们可以用下面的代码来指定:

```
function echo<T>(arg: T[]): T[] {
  console.log(arg.length);
  return arg;
}
```

同样，我们可以用`Array<T>`泛型类型来代替，如下面的代码所示:

```
function echo<T>(arg: Array<T>): Array<T> {
  console.log(arg.length);
  return arg;
}
```

在这两段代码中，我们可以记录作为参数传递给函数的数组的`length`属性，因为它保证了我们传递和返回的总是数组。例如，我们可以在下面的代码中调用新的`echo`函数:

```
echo([1, 2, 3]);
echo(['a', 'b', 'c']);
```

在上述情况下，类型推断仍然有效。

在上面的例子中，我们有一个参数和一个返回值。但是如果我们想传入多个不同类型的参数并返回其中的一个或多个呢？我们可以这样写:

```
function echo<T, U>(arg: T, arg2: U): [T, U] {  
  return [arg, arg2];
}
```

在上面的代码中，我们有两个泛型类型，`T`和`U`，它们代表相同或不同的类型。例如，我们可以在下面的代码中使用它们:

```
console.log(echo(1, 'a'));
```

在上面的代码中，第一个参数是一个数字，第二个是一个字符串。我们也可以将两者设为相同的类型，如下面的代码所示:

```
console.log(echo(1, 2));
```

![](img/c8fdd7f340f62936d70de73991b73c22.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 类型标记的变化

我们最终会遇到的另一个问题是，我们希望参数的数据类型不同于返回类型。解决这个问题的一种方法是将泛型类型和常规类型与接口混合，就像我们在下面的代码中所做的那样:

我们从`console.log`中得到以下内容:

```
{a: 1, b: "a", c: false, d: {}}
```

我们也可以省略返回类型，就像我们在下面的代码中所做的那样:

```
function echo<T, U, V, W, X>(a: T, b: U, c: V, d: W) {  
  return { a, b, c, d };  
}console.log(echo(1, 'a', false, {}));
```

我们得到与上面相同的输出。注意，在上面的两个例子中，我们可以指定任意多的字母作为泛型类型标记。泛型类型标记不一定是一个字母。它只是一个单个字母的惯例。我们可以这样写:

```
function echo<T, U, V, W, AA>(a: T, b: U, c: V, d: AA) {  
  return { a, b, c, d };  
}
```

定义泛型函数的另一种方法是将签名和泛型类型标记一起放在接口中。当我们通过将函数赋给变量来声明函数时，我们可以用接口设置变量的类型，然后将泛型函数赋给变量，并在函数中添加通常的泛型类型标记。例如，我们可以编写类似下面的代码:

我们也可以将`<>`中的泛型类型标记添加到`interface`声明中，如下所示:

```
interface EchoFn<T> {
  <T>(a: T): T;
};
```

然后，当我们声明将函数赋给的变量时，我们必须显式指定接口的类型，如下所示:

在上面的代码中，我们必须在最后一行显式添加`number`类型声明，而不是让 TypeScript 自动推断。

# 摘要

为了避免重新定义具有相同逻辑但不同参数类型和返回类型的函数，我们可以定义泛型函数。为了做到这一点，我们在函数和用于对函数进行类型化的接口中添加了泛型类型标记。

我们通过在函数名后面的`<>`中插入通用类型标记来定义函数，每个标记用逗号分隔。我们还将它们添加到参数中的冒号之后、冒号之后和左花括号之前。

返回类型的泛型类型标记是可选的。如果我们想返回一个和我们传递给参数的类型不同的东西，我们可以忽略它。同样，在接口中，我们将函数的签名放在接口中，也可以放在接口定义中，以便在我们定义的函数中强制使用泛型类型标记。