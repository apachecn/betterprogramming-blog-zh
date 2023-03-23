# TypeScript 数据类型介绍(第 1 部分)

> 原文：<https://betterprogramming.pub/introduction-to-typescript-data-types-void-null-undefined-never-and-object-types-11eb839e5381>

## N `ull`，v `oid`，u `ndefined`，n `ever`，o `bject`类型

![](img/7be1cf99291c9a1ead9bb3f9da79d218.png)

由[活动创建者](https://unsplash.com/@campaign_creators?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

JavaScript 和其他编程语言一样，有自己的数据结构和类型。

JavaScript 有一些我们必须知道的数据类型，以便用它来构建程序。不同的数据可以放在一起构建更复杂的数据结构。

JavaScript 是一种松散类型或动态类型的语言。这意味着用一种类型声明的变量可以转换成另一种类型，而不用显式地将数据转换成另一种类型。

变量也可以在任何时候包含任何类型，这取决于赋值的内容。在动态类型语言中，如果不记录就很难确定变量的类型，我们可能会在变量中分配我们不想要的数据。

TypeScript 纠正了这些问题，让我们为变量设置固定的类型，这样我们就可以确定类型。在本文中，我们将看看`void`、`null`、`undefined`、`never`和`object`类型。

# 空的

`void`型与`any`型正好相反。它意味着任何类型的缺失。因此，如果在运行 TypeScrip 编译器时没有指定`--strictNullChecks`设置，或者可以将其设置为`undefined`，则`void`类型的变量只能有值`null`。

因此，直接给一个`void`变量赋值不是很有用。它更多地用于指定函数的返回数据类型。具有`void`返回类型的函数不返回任何东西。

例如，我们可以对一个`void`变量进行无用的赋值，如下面的代码所示:

```
let useless: void = undefined;
```

我们可以通过编写以下内容将箭头函数的返回类型设置为`void`:

```
const voidFn = (): void => {
  console.log("Void function returns nothing");
}voidFn();
```

或者，我们可以将传统函数的返回类型设置为`void`，如以下代码所示:

```
function voidFn(): void {
  console.log("Void function returns nothing");
}voidFn();
```

两个函数调用都将从函数内部的`console.log`语句中输出`Void function returns nothing`。

# 空

`null`类型代表一个只能取值`null`的变量。`null`意味着变量没有值。

所以，再一次，给它赋值是没有用的。我们只能将`null`赋给一个有变量`null`的变量。运行 TypeScript 编译器时设置了`--strictNullChecks`标志，`null`只能赋给类型为`any`和`null`的变量。

它变得有用的地方是，我们可以用联合类型将多个变量的值赋给它。

然后，我们可以指定一些有用的东西和`null`类型。这与 JavaScript 不同，因为历史原因，值`null`是类型`object`而不是`null`。

# 不明确的

`undefined`类型代表一个只能取值`undefined`的变量。所以，再一次，给它赋值是没有用的。

我们只能将`undefined`赋值给一个包含变量`null`的变量。`undefined`只能赋给`any`类型和`undefined`类型的变量。

它变得有用的地方是，我们可以用联合类型将多个变量的值赋给它。

然后，我们可以指定一些有用的东西和`undefined`类型。它本身实际上是没有用的，所以我们不应该看到很多只有`undefined`类型变量的情况。

# 从不

`never`类型是一种表示从未发生的事情的值类型。它就像`void`一样，对于指定一个函数永远不返回任何东西是很有用的。

`never`类型是每个类型的子类型，并且可以分配给每个类型。然而，除了其他`never`变量之外，没有类型是`never`类型的子类型或可分配给`never`类型。

具有`never`返回类型的函数必须总是有一个不可到达的端点。例如，我们可以编写一个函数，它有一个无限循环，返回类型为`never`，如下面的代码所示:

```
function infiniteFn(): never {
  while (true) {
  }
}
```

抛出异常的函数也可能具有`never`返回类型，如下例所示:

```
function errorFn(message: string): never {
  throw new Error(message);
}errorFn('Error occurred');
```

# 目标

`object`类型是表示非原始对象的类型。也就是说，任何不是`number`、`string`、`boolean`、`bigint`、`symbol`、`null`或`undefined`的东西。

它主要用在标准库中的`Object`对象的类型定义中，以及其他不希望将原始值赋给它或传递给函数的代码中。

例如，在`Object.create`方法的类型定义中，我们看到参数的类型被设置为`object`，如下面的代码所示:

```
create(o: object | null): any;
```

同样，在同一个类型声明中的`setPrototypeOf`方法的签名中，我们看到`proto`参数，即接受对象原型的参数，也设置了`object`类型，如下所示:

```
setPrototypeOf(o: any, proto: object | null): any;
```

这样，我们就不能在 TypeScript 中将原始值作为参数传入这些方法。如果我们这样做，那么我们会得到一个错误。因此，如果我们的 TypeScript 文件中有以下代码，那么它们将被编译并运行:

```
const obj1 = Object.create({});    
const obj2 = Object.create(null);
console.log(obj1);
console.log(obj2);
```

我们将在两个`console.log`语句中得到一个空对象。

唯一的区别是`obj2`是一个纯对象，这意味着它不从任何原型继承。将原始值传递给下面代码中的`create`方法会导致编译失败:

```
Object.create(42); 
Object.create("abc"); 
Object.create(false);
Object.create(undefined)
```

上面的代码会给我们第一个错误一个`Argument of type ‘42’ is not assignable to parameter of type ‘object | null’`，给第二个错误一个`Argument of type ‘abc’ is not assignable to parameter of type ‘object | null’`。

第三行将带给我们`Argument of type ‘false’ is not assignable to parameter of type ‘object | null’`，最后一行将带给我们`Argument of type ‘undefined’ is not assignable to parameter of type ‘object | null’`。

编译会失败，代码不会运行。

此外，我们可以使用它来防止原始值被分配给它。例如，如果我们写:

```
let x: object = {};
```

然后，上面的代码将被编译并运行。但是，如果我们改为编写以下代码:

```
let x: object = 1;
```

然后我们得到`Type ‘1’ is not assignable to type ‘object’`，代码不能用 TypeScript 编译器编译和运行，因为`1`是一个原始值。

`void`型与`any`型正好相反。它意味着任何类型的缺失。因此，如果在运行 TypeScrip 编译器时没有指定`--strictNullChecks`设置，或者设置为`undefined`，那么`void`类型的变量只能有值`null`。

这对于声明不返回任何内容的函数非常有用。`null`类型变量只能取`null`值。只能给`undefined`类型赋值`undefined`。

`object`类型是一种表示非基本对象的类型。即不是`number`、`string`、`boolean`、`bigint`、`symbol`、`null`或`undefined`的任何东西。

它主要用于标准库中的`Object`对象的类型定义，以及其他不希望原始值被赋给它或传递给函数的代码。