# 如何在 JavaScript 中向对象添加流类型

> 原文：<https://betterprogramming.pub/javascript-type-checking-with-flow-objects-1eb7fca4032e>

## 从脸书之流开始

![](img/500846d9d5b15b004293749b374b21a1.png)

由 [tribesh kayastha](https://unsplash.com/@tribeshk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/workflow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Flow](https://flow.org/) 是脸书制作的一个类型检查器，用于检查 JavaScript 数据类型。它有许多内置的数据类型，我们可以用它们来注释变量和函数参数的类型。

在本文中，我们将看看如何向对象添加流类型。

# 句法

为对象创建类型的语法与在 JavaScript 中创建对象文字的语法很接近。

我们可以通过编写以下代码来创建一个简单的类型化对象:

```
let obj: { bar: boolean } = { bar: true };
```

在上面的代码中，`{ bar: boolean }`是`obj`的类型。这意味着`obj`对象必须至少拥有`bar`属性。除了`bar`属性之外，我们还可以添加其他属性。

我们用逗号分隔多个键值对:

# 可选属性

为了使属性可选，我们可以在属性名后添加一个问号。

例如，我们可以写:

```
let obj:  { name?: string } = {
  name: 'Joe'
};
```

`name`属性是可选的，所以我们可以省略它或者将其设置为`undefined`。

我们可以写:

```
let obj:  { name?: string } = { name: undefined };
let obj2:  { name?: string } = { };
```

# 密封物体

当我们创建一个已经设置了属性和值的对象时，Flow 将认为对象*是密封的*，这意味着我们不能添加属性或从中删除属性。

如果我们有以下对象:

```
let obj: { a: number, b: number, c: number } = { a: 1, b:2, c:3 };
```

那么下面的代码将给出错误:

```
obj.d = 1;
delete obj.a;
```

# 未密封的对象

如果我们创建一个没有任何属性的对象，Flow 会认为它是一个未密封的对象，这意味着我们可以对它做任何事情。

例如，如果我们有:

```
let obj = {};
```

然后我们可以写:

```
obj.d = 1;
delete obj.a;
```

# 分配未密封的对象属性

Flow 将为被赋予对象属性值的变量提供属性值可以采用的所有可能类型。

例如，如果我们有以下对象定义:

那么`foo`属性将具有`number|string`类型。这意味着我们可以编写下面的代码将`obj.foo`的值赋给一个新变量:

```
let foo: number|string = obj.foo;
```

我们不能写:

```
let foo: number = obj.foo;
```

或者:

```
let foo: string = obj.foo;
```

因为`obj.foo`不总是数字或字符串。

![](img/f9a46fd59b3a0a5a9f7ecea3d764488d.png)

Amy Asher 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 具有未定义属性值的未密封对象

不检查具有未定义属性值的未密封对象的属性的类型。

例如，如果我们有以下对象:

```
let obj = {};
obj.foo;
```

我们可以将它赋给任何类型的变量，如下所示:

```
let foo: number = obj.foo;
let bar: string = obj.foo;
let baz: boolean = obj.foo;
```

# 精确的对象类型

我们可以为给定类型的对象添加额外的属性。例如，如果我们有以下类型:

```
{ foo: string }
```

我们可以这样写:

```
let obj: { foo: string } = { foo: 'bar', baz: 1 };
```

只要我们将`foo`属性设置为一个值，我们就可以向`obj`添加任何我们想要的东西。

我们可以用确切的属性数来设置类型，方法是:

```
{| foo: number |}
```

`|`符号指定该类型的对象只能有`foo`属性，因此下面的赋值将会失败:

```
let foo: {| foo: number |} = { foo: 1, bar: 2 };
```

我们通过移除额外的`bar`属性来修复它:

```
let foo: {| foo: number |} = { foo: 1 };
```

# 作为地图的对象

要将对象用作映射，我们可以创建具有动态属性名称的类型。例如，我们可以编写以下代码来创建具有动态字符串属性的对象类型:

```
let obj: { [prop: string]: number } = {};
```

在上面的代码中，`{ [prop: string]: number }`指定`obj`可以拥有带有字符串属性名和数字属性值的动态属性。

例如，我们可以将以下内容分配给`obj`:

```
obj['foo'] = 1;
obj['bar'] = 2;
```

属性标识符也可以是数字。如果我们有:

```
let obj: { [prop: number]: number } = {};
```

然后我们可以写:

```
obj[0] = 1;
obj[1] = 2;
```

对于具有数字属性的对象类型，每个值都被假定为具有我们在对象类型中定义的类型。例如，如果我们有:

```
let obj: { [prop: number]: number } = {};
obj[0] = 1;
obj[1] = 2;
```

那么`obj`的所有索引都假设为一个数，那么我们可以写成:

```
obj[100].toString();
```

不管它是否存在。

# `Object`类型

在 Flow 的未来版本中将删除`Object`类型。对于可以拥有任何东西的对象，我们应该使用`any`或`{ [key: string]: any}`。

例如，我们可以写:

```
let obj: Object = {};
obj['foo'] = 1;
obj[1] = 2;
```

# 结论

Flow 用于检查 JavaScript 对象类型的类型系统相当全面。我们可以设置属性和对象的类型。

已经设置了属性和值的对象被认为是密封的，这意味着我们不能添加或删除它们的属性。空对象被认为是未密封的，这意味着我们可以对它们做任何事情。

我们也可以用带有`|`符号的提取结构来定义对象类型。另一方面，我们也可以用括号为属性名定义动态对象类型。