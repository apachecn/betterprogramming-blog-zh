# 用流检查交集类型和 JavaScript 类型

> 原文：<https://betterprogramming.pub/javascript-type-checking-with-flow-intersection-types-8f115659f4bb>

## 看一下对象类型的交集

![](img/bd3527e76e0be579106e8ab1a0f061f6.png)

照片由 [Deb Dowd](https://unsplash.com/@fin777?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[Flow](https://flow.org/) 是脸书制作的一个类型检查器，用于检查 JavaScript 数据类型。它有许多内置的数据类型，我们可以用它们来注释变量和函数参数的类型。

在本文中，我们将看看如何使用交集类型来创建接受多种类型组合的变量。

# 定义交叉点类型

我们可以通过用`&`符号分隔多个类型来创建交集类型，如下所示:

```
Type1 & Type2 & ... & TypeN
```

我们也可以用前导符号`&`将上面的表达式分成多行:

```
type Bar =
  & Type1
  & Type2
  & ...
  & TypeN
```

此外，我们可以从其他交叉点类型创建交叉点类型:

```
type A = Type1 & Type2;
type B = Type3 & Type4;

type Foo = A & B;
```

# 交叉点类型的属性

当我们调用一个接受交集类型的函数时，我们必须传入包含所有这些类型的数据。但是在函数内部，我们可以将它们视为交集类型中的一种类型。

例如，如果我们定义以下函数:

```
type A = { a: number };
type B = { b: boolean };
type C = { c: string };

function foo(value: A & B & C) {

}
```

那么我们可以将`value`视为`A`、`B`和`C`中的任意一个，如下所示:

```
function foo(value: A & B & C) {
  let a: A = value;
  let b: B = value;
  let c: C = value;  
}
```

# 不可能的交集类型

我们可以创造不可能的交集类型。例如，我们可以创建一个属于`string`和`boolean`交集的类型，如下所示:

```
type StringBoolean = string & boolean;
```

这种类型是无用的，因为没有值可以既是字符串又是布尔值，所以我们不能给它赋值。

![](img/2f80fc994b235893e6cd18b002daddbf.png)

Matthew LeJune 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 对象类型的交集

对象类型的交集意味着交集类型的对象必须具有两种类型的所有属性。

例如，假设我们有以下交集类型:

```
type Foo = { foo: string };
type Bar = { bar: boolean };type FooBar = Foo & Bar;
```

我们创建的类型为`FooBar`的变量必须具有`Foo`和`Bar`的所有属性:

```
let foobar : FooBar = {
  foo: 'abc',
  bar: true
};
```

当两个对象类型具有相同的属性名称但不同的类型时，那么当它们相交在一起时，具有相同名称的新属性将具有两种类型的交集作为新类型。

例如，假设我们有:

```
type Foo = { foo: string };
type Bar = { foo: boolean };type FooBar = Foo & Bar;
```

那么`foo`将拥有`string & boolean`交集类型，这意味着不能给它设置任何值。

使用交集类型，我们可以创建具有构成交集类型的每种类型的所有属性的类型。我们也可以从原始类型中创建交集类型，但是它们是无用的，因为不能给这些类型的变量赋值。

这意味着交集类型主要对对象类型有用。