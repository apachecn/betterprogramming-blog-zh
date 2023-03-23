# TypeScript 的 7 种高级用法

> 原文：<https://betterprogramming.pub/7-advanced-usages-of-typescript-f9abb0dd64ff>

## 您是否编写了大量冗余类型的代码？以下是一些高级功能。

![](img/1e042439716e58a53572b78fb3e0e9c5.png)

不管你相不相信，仍然有一些高级开发人员不知道 TypeScript。如果你不知道它是什么，先看看官方入门。

本文主要介绍 TypeScript 的高级用法。

# 无商标消费品

使用`any` **，** *并不是一个好的做法，要不惜一切代价避免。在大多数情况下，泛型是有益的。看看上面的片段。您可以将`User`转换为响应，其类型如下所示:*

```
{
  item?: {
    id: string
  };
  items?: {
    id: string
  }[];
}
```

# Keyof 类型运算符

有了`keyof`，你就不必为`onChange`中的`key`使用一个[联管节类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types)。`expiryDateTime`还将检查密钥是否存在。

如果你试着做这样的事情:

```
type Foo = { bar: number } & ExpiryDateTimeconst onChange = (
  key: keyof Foo,
  val: number
): void => {
  expiryDateTime[key] = val
}
```

`expiryDateTime`下面会有一条红线告诉你`Property 'bar' does not exist on type 'ExpiryDateTime'`。如果用`string`代替`key`，也会出现同样的错误

`keyof`在重构时帮你。如果你使用联合类型，那么你需要做双重工作。

# Typeof 类型运算符

`typeof`允许你从变量中提取类型。与 JavaScript 的`typeof`不同，TypeScript 的`typeof`允许在一个类型中使用，以将其作为一个类型引用，例如:

```
let foo = "bar";
let name: typeof foo; // string
```

我知道这太简单和多余了。让我们试一个复杂的:

如果我们对`getHisOrHer`返回类型进行更改，那么`HisOrHer`的类型也会随之更改，这是一次性的工作。

# 条件类型

你已经陷入如此尴尬的境地了吗？那么，有一个解决条件类型的方法。

这个概念类似于 JavaScript 的三元运算符`condition ? true : false`

`s`传递了`string`作为参数，所以它将使用类型`StringId`，而 n 传递了`number`，它将返回`NumberId`

可惜你不能有第三种或者更多的选择。

# 映射类型

在某些情况下，您将需要一个动态键来支持您的类型，如上面的代码片段。您将产品名称作为对象键，但是所有的值都是相同的。使用映射类型，您不必为每个产品名称创建一个类型。

更复杂的来了。假设您需要两个变量，一个用于颜色值，另一个用于开关。如果您添加更多颜色，此功能可以减少产生错误的机会。

## 映射修改器

做好准备。我将向您展示更复杂的映射类型的例子。

您可以使用`-`作为前缀来删除修饰符，或者使用`+`来添加修饰符。在代码片段中，`toMutable`删除了`readonly`修饰符。

## 通过`as`重新映射按键

`Capitalize`是一个字符串操作类型，还有几个更可用的:

*   大写—将第一个字符转换为大写
*   不大写—将第一个字符转换为小写
*   大写—将第一个字符转换为大写
*   大写—将所有字符转换为大写
*   小写—将所有字符转换为小写

# 模板文字类型

这个特性与 JavaScript 的模板文字中的概念相同。它在字符串文字的基础上构建类型。

# 实用类型

最后但同样重要的是，**实用程序类型:**

*   `Pick<T, Keys>` —从类型中选择`key`的属性
*   `Omit<T, Keys>` —与`Pick`相反，取除`Keys`以外的所有属性
*   `Partial<T>` —类似于`?`又名`optional`，但是你不需要将里面的所有属性都设置为可选的，因为你可能只需要变量或函数中的部分属性。
*   `Required<T>` —与`Partial<T>`相反，这将要求所有属性包括`optional`属性
*   `Readonly<T>` —整个属性是不可变的，并且是只读的
*   `Record<Key, T>` —我不建议在 TypeScript 中使用`object`类型，你会经常得到`TS2339: Property 'x' does not exist on type 'object'`。我总是用`Record<Key, T>`作为替代。

正如你所看到的，大多数功能可以相互结合，充分利用它。

无论谁和我一样有过这样的挣扎，我希望这篇文章能简化你的工作。本文不包括所有的高级特性；例如，我选择了常用的功能。如果你想知道更多，请查阅官方文档。

# 奖励—基于 Intellij 的 IDE

当你试图将一个 JavaScript 代码库迁移到 TypeScript 代码库或者重构一个某人爱用的代码库`any`时，这个特性就是你的救星。

# 参考

*   [打字正式文件](https://www.typescriptlang.org/docs/)