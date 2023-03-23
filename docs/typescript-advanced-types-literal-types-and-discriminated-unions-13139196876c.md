# TypeScript 高级类型

> 原文：<https://betterprogramming.pub/typescript-advanced-types-literal-types-and-discriminated-unions-13139196876c>

## 文字类型和有区别的联合

![](img/02d18ba2d1c494bfd94de56261bcd6dc.png)

照片由[山姆·卡特](https://unsplash.com/@samdc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 具有许多高级类型功能，有助于简化动态类型代码的编写。它还促进了现有 JavaScript 代码的采用，因为它让我们在使用 TypeScript 的类型检查功能的同时保留了 JavaScript 的动态功能。TypeScript 中有许多高级类型—交集类型、联合类型、类型保护、可空类型、类型别名等等。在这一部分中，我们将看看接口和类型别名、文字类型以及有区别的联合之间的区别。

# 接口和类型别名之间的区别

尽管接口和类型别名看起来很相似，但它们之间还是有细微的差别。接口创建了一种可以在任何地方使用的新类型。然而，类型别名只是已经存在的类型的新名称，包括接口。从 TypeScript 2.7 开始，可以通过创建新的交集类型来扩展类型别名。这意味着类似以下代码的内容在 TypeScript 2.7 和更高版本中是有效的:

```
type Cat = string & { age: number }
```

为了创建新的数据类型，应该使用接口而不是类型别名。

# 字符串文字

对于字符串，我们可以指定一个字符串变量可以取什么值，方法是指定它可以被赋值的可能值，每个值用管道符号(`|`)分隔。这些可以组合成其他类型，如联合类型、类型保护和类型别名。

例如，我们可以编写类似下面的代码:

```
type PersonName = 'Joe' | 'Jane' | 'Amy';
let personName: PersonName = 'Joe';
```

如果我们试图给`n`分配一个没有在`PersonName`类型别名中列出的值，就像我们在下面的代码中做的那样:

```
type PersonName = 'Joe' | 'Jane' | 'Amy';
let personName: PersonName = 'foo';
```

然后，我们从 TypeScript 编译器得到以下错误消息:

```
Type '"foo"' is not assignable to type 'PersonName'.(2322)
```

通过将管道符号用于字符串并分配给类型别名，它让我们声明一个类似于`enum`的变量，但不是。我们还可以通过为字符串设置可能的字符串值来区分重载。例如，我们可以写:

```
function echo(name: 'Joe'): string;
function echo(name: 'Jane'): string;
function echo(name: 'Amy'): string;
function echo(name: string) {
  return name;
}echo('Joe');
```

如果我们试图传入一个无效值，也就是重载中没有列出的任何值，那么我们会得到一个错误。例如，如果我们这样写:

```
function echo(name: 'Joe'): string;
function echo(name: 'Jane'): string;
function echo(name: 'Amy'): string;
function echo(name: string) {
  return name;
}echo('foo');
```

然后，我们从 TypeScript 编译器得到这些错误消息:

```
No overload matches this call.Overload 1 of 3, '(name: "Joe"): string', gave the following error.Argument of type '"foo"' is not assignable to parameter of type '"Joe"'.Overload 2 of 3, '(name: "Jane"): string', gave the following error.Argument of type '"foo"' is not assignable to parameter of type '"Jane"'.Overload 3 of 3, '(name: "Amy"): string', gave the following error.Argument of type '"foo"' is not assignable to parameter of type '"Amy"'.(2769)
```

# 数字文字类型

我们可以用数字创建文字类型，就像我们处理字符串一样。这样做的符号是相同的，除了我们用数字代替类型的有效值。例如，我们可以编写以下代码来定义数字文字类型，并将值赋给这种类型的变量:

```
type Num = 1 | 2 | 3;
let num: Num = 1;
```

如果我们试图分配一个没有在类型别名定义中列出的值，如下面的代码所示，会怎么样？：

```
type Num = 1 | 2 | 3;
let num: Num = 6;
```

我们得到以下错误消息:

```
Type '6' is not assignable to type 'Num'.(2322)
```

![](img/4ceb7ba53e482ef082ec0893f417d75d.png)

Tatiana Rodriguez 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 受歧视的工会

我们可以添加一个具有常数值的成员来区分构成联合类型的不同类型。这就是所谓的歧视性结合。其他名称包括标记联合或代数数据类型。在 TypeScript 中，我们可以创建一个由三部分组成的有区别的联合。首先，我们需要一个共同的，单一实例类型的属性，这是所谓的判别式。第二，我们需要一个接受这些类型的联合的类型别名，这是我们通常创建的联合类型。最后，我们需要一个类型保护。

我们可以用类似下面的代码用 TypeScript 创建一个有区别的联合:

在上面的例子中，我们有一个在所有接口中都可用的公共`kind`成员。然后我们创建联合`Animal`类型，它是`Cat`、`Dog`和`Bird`类型的联合。这意味着一个`Animal`对象的`kind`属性可以取值`cat`、`dog`或`bird`。然后，在`getSize`函数中，我们检查`kind`属性的值。在`switch`语句中，我们检查了`kind`属性，我们检查了该属性的值，并从`case`块内部键入了对`animal`参数进行类型化的数字，这是我们的类型保护。

我们应该添加类型保护来检查构成联合类型的所有类型。如果我们向上面的`Animal`联合类型添加另一个类型，但没有更新`getSize`函数，如下面的代码所示，会怎么样？：

我们将从 TypeScript 编译器获得以下错误信息:

```
Not all code paths return a value.(7030)
```

相反，我们应该涵盖所有情况:

接口和类型别名之间有细微的差别，尽管它们看起来有点像。接口创建了一种可以在任何地方使用的新类型。然而，类型别名只是已经存在的类型的新名称，包括接口。在 TypeScript 中，我们可以创建字符串或数字文字来限制这些类型的变量可以采用的可能值。我们还可以通过向每个接口添加一个 singleton 属性并为每个属性设置一个惟一的值来创建有区别的联合。然后，我们可以在代码中使用类型 guards 检查该属性的值，以获得我们想要的属性。