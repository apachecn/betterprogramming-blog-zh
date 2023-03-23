# TypeScript 如何知道哪些类型相互兼容？

> 原文：<https://betterprogramming.pub/how-does-typescript-know-which-types-are-compatible-with-each-other-enums-and-more-2f4171e41ca0>

## 我们看看如何确定类、枚举和泛型具有兼容的类型

![](img/35aebebd30efec1ab46228f643e9db3a.png)

由 [Safar Safarov](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 枚举和更多

与 JavaScript 相比， [TypeScript](https://www.typescriptlang.org/) 的优势在于我们可以用它来注释变量、函数和其他实体的数据类型。这让我们可以在文本编辑器中自动完成，并且为构建应用程序增加了一个编译步骤。因此，编译时检查可以捕获更多的错误，如“未定义的错误”和“意外的数据类型”，否则这些错误会在运行时被捕获。

为此，TypeScript 根据实体的结构和其他方面(如参数数量、每个参数的类型等)检查不同变量是否具有相同的类型。在本文中，我们将继续研究 TypeScript 如何确定哪些数据类型是相互兼容的。

# 确定兼容的函数参数类型

当将值作为参数传递给函数时，如果源参数可赋值给目标参数，或者反之，则参数赋值将成功。这涉及到在编译时不能确定的动作，但仍然是允许的。这意味着我们可以传入比参数指定的类型更特殊的参数，以保持与 JavaScript 的兼容性。例如，我们可以在下面的代码中做一些事情:

在上面的代码中，我们传入了一个类型为`Employee`的变量，它比类型为`Person`的`person`参数有更多的属性。这意味着，像在 JavaScript 中一样，我们可以选择忽略传递给函数的对象的属性。当然，`employeeCode`属性不能在`fn`函数中访问，所以如果我们写:

我们会得到错误消息:

```
Property 'employeeCode' does not exist on type 'Person'.(2339)
```

为了禁止这种行为，我们可以在编译代码时启用`strictFunctionTypes`标志。

# 可选参数和 Rest 参数

在 TypeScript 中，为了进行函数参数类型兼容性检查，可选参数和必需参数可以互换。无论是源类型的额外参数，还是没有源类型的相应参数的目标类型的可选参数，都不是错误。例如，允许使用以下代码:

```
const runLater = (callbackFn: (...args: any[]) => void) => 0;
runLater((a, b) => { });
runLater((a, b, c) => { });
```

我们可以将任何函数作为回调函数传入，因为我们使用了 rest 操作来指定我们可以在回调函数中传入任意数量的参数，并将其传递给`runLater`函数。带有 rest 参数的函数被视为具有无限个参数。这是可以的，因为不向函数传递任何东西与在 JavaScript 中传递`undefined`是一样的。

# 重载函数

在同名函数有多个签名的情况下，函数重载必须具有与至少一个签名匹配的参数，TypeScript 才能接受有效的函数调用。例如，如果我们有以下函数重载:

```
function fn(a: { b: number, c: string, d: boolean }): number
function fn(a: { b: number, c: string }): number
function fn(a: any): number {
  return a.b;
};
```

然后我们必须调用`fn`函数，为第一个参数传入一个数字，为第二个参数传入一个字符串:

```
fn({ b: 1, c: 'a' });
```

TypeScript 将检查每个签名，以查看我们作为参数传入的对象的属性结构是否与任何重载匹配。如果不匹配，我们有以下内容:

```
fn({ b: 1 });
```

然后我们得到以下错误消息:

![](img/387b9caa9ec6cb7d31b4eaabd644064e.png)

照片由 [Emma Matthews 数字内容制作](https://unsplash.com/@emmamatthews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

# 枚举

在 TypeScript 中，枚举被认为与数字兼容，反之亦然。但是，不同枚举类型的枚举值被认为是不兼容的。例如，如果我们有:

```
enum Fruit { Orange, Apple };
enum Bird { Chicken, Duck, Goose };let fruit = Fruit.Orange;
fruit = Bird.Chicken;
```

然后在最后一行，我们会得到错误消息:

```
Type 'Bird.Chicken' is not assignable to type 'Fruit'.(2322)
```

# 班级

类的类型兼容性类似于对象文字类型。但是，只检查每个类的实例成员的兼容性。静态成员在类型兼容性检查中被忽略。例如，如果我们有:

那么代码将被 TypeScript 编译器接受，因为`animal`和`person`被认为是具有兼容类型的变量。当 TypeScript 编译器检查类型兼容性时，类似于`constructor`和`getNumPersons`方法的静态成员将被忽略。

当私有和受保护成员包含在一个类中时，在类型兼容性检查期间会对它们进行检查。例如，如果我们有以下代码:

那么 TypeScript 将拒绝代码，并显示以下错误:

```
Property 'age' is missing in type 'Person' but required in type 'Animal'.(2741)input.ts(3, 11): 'age' is declared here.
```

如果两个类的私有成员都是从同一个源派生的，TypeScript 将只接受代码。例如，如果我们有:

然后，当我们试图编译代码时，TypeScript 编译器将接受它，因为私有成员`age`在`Animal`类中，而`Dog`和`Cat`类是它的子类。这也适用于受保护的成员。

# 无商标消费品

泛型的类型兼容性检查也是从其结构中派生出来的。例如，如果我们有:

那么它们是兼容的，因为`x`和`y`都是`Person`类型，我们没有在接口的任何成员中引用泛型类型标记。然而，如果我们在`Person`接口中引用一个成员，正如我们在下面的代码中所做的:

那么我们传递给`<>`的类型确实很重要，因为我们用它来确定`age`的类型。如果我们尝试编译上面的代码，我们会得到错误:

```
Type 'Person<string>' is not assignable to type 'Person<number>'.Type 'string' is not assignable to type 'number'.(2322)
```

为了使它们被认为是兼容的类型，我们传入的类型必须在两个变量声明中相同，如下所示:

# 结论

在 TypeScript 代码中，我们可以传入比参数指定的类型更特殊的类型的参数，以保持与 JavaScript 的兼容性。

对于具有重载的函数，我们传入的参数必须与函数重载中声明的参数类型相匹配。对于类，在确定两个类是否属于兼容类型时，只考虑实例成员。这适用于公共成员。对于私有和受保护的成员，它们必须从同一个超类派生，这样两个类才能被确定为具有兼容的类型。枚举与数字兼容，反之亦然，但是来自两个不同枚举的枚举值互不兼容。通过遵循与对象、函数和类相同的规则，泛型被认为是相互兼容的。与任何其他代码一样，传递给泛型的类型将被视为类型兼容。