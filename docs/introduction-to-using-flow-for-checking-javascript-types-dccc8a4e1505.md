# 使用 Flow 检查您的 JavaScript 类型

> 原文：<https://betterprogramming.pub/introduction-to-using-flow-for-checking-javascript-types-dccc8a4e1505>

## 在您的项目中设置流，并使用它来检查基本类型

![](img/a431e8d67de786c26865c98a906bb4f4.png)

[迈克·刘易斯 HeadSmart 媒体](https://unsplash.com/@mikeanywhere?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

因为 JavaScript 是一种动态类型语言，所以变量和对象属性有意外类型会有问题。此外，一些 JavaScript 类型的强制规则是任意的。为了使开发 JavaScript 应用程序更容易，脸书对 JavaScript 进行了扩展，为语言添加了类型注释和类型检查。

# 向项目添加流程

这种语言由脸书对 JavaScript 的扩展以及原始的 JavaScript 语言组成，被称为 Flow。

我们可以很容易地在新项目中使用它。为了全局添加它，我们只需安装 Yarn 并运行:

```
yarn add --dev @babel/core @babel/cli @babel/preset-flow
```

然后，我们创建一个`.babelrc`文件，并添加以下内容:

```
{
  "presets": ["@babel/preset-flow"]
}
```

然后我们可以构建并运行代码:

```
yarn run babel src/ -- -d lib/
```

该命令可以添加到`package.json`文件，如下所示:

```
{
  "name": "project-name",
  "main": "lib/index.js",
  "scripts": {
    "build": "babel src/ -d lib/"
  }
}
```

我们也可以通过在我们的项目中运行`yarn add --dev flow-bin`来为每个项目添加它，添加 Flow 作为项目的开发依赖。然后我们可以运行`flow`命令:`yarn run flow`(如果你以前没有运行过的话，你可能需要运行`yarn run flow init`)。

# 向添加流代码以进行类型检查

既然安装了 Flow，我们可以向代码中添加类型注释，让 Flow 进行类型检查。

最简单的方法是在变量后添加类型注释来指示其类型:

```
// @flow
let x: number = 1;
```

注意，我们必须在我们的代码上添加`// @flow`,以使 Flow 能够对我们的代码进行类型检查。

此外，我们可以向函数参数添加类型注释:

```
// @flow
function cube(x: number): number {
  return x**3;
}
```

然后当我们写:`cube('');`时，我们得到下面的错误:

```
[Flow] Cannot call `cube` with empty string bound to `x` because string [1] is incompatible with number [2].
```

这是因为我们将一个字符串传递给了一个需要数字的函数。

如果我们传入一个数字，它应该是:

```
cube(2);
```

# 基本类型

对于我们将在 JavaScript 中遇到的各种数据类型，Flow 有自己的类型注释。

像 JavaScript 一样，它有各种原始数据类型。JavaScript 有以下基本类型:

*   布尔运算
*   用线串
*   数字
*   `null`
*   `undefined`(流量类型中的`void`)
*   符号(从 ES6 开始，但在 Flow 中尚不支持)

它们可以作为文字使用，也可以用包装构造函数或函数来构造:

```
//literals
true;
"abc";
1;
null;
undefined;// constructors
new Boolean(false);
new String("abc");
new Number(1);// factory function
Symbol("abc");
```

文字值有小写的类型名。用构造函数创建的类型对象的类型名是大写的。例如，我们可以有:

```
// literals
x: number// constructor objects
x: Number
```

![](img/86bb06fddb12f00563bac2de58238f03.png)

塞缪尔·费拉拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 布尔运算

布尔值可以是`true`或`false`。我们可以写:

```
// [@flow](http://twitter.com/flow)
function boo(val: boolean) {

}boo(true);
boo(2);
```

`boo(2);`会给我们带来以下错误:

```
[Flow] Cannot call `boo` with `2` bound to `val` because number [1] is incompatible with boolean [2].
```

JavaScript 会在需要时将对象转换为布尔值。例如，当我们比较布尔值时，它将在`if`语句或等式语句中转换它。

在 JavaScript 中，我们可以这样写:

```
if (1) {}
```

这是因为它有真和假的价值。当转换为布尔值完成时，假值被转换为`false`。以下值是假的:

*   0
*   `undefined`
*   `null`
*   `NaN`
*   空字符串
*   `false`

其他一切都是真实的。

对于 Flow，如果我们有一个类型为`boolean`或`Boolean`的参数或变量，我们必须显式地传入布尔值。`boolean`和`Boolean`类型不能与流程互换。鉴于我们有:

```
// [@flow](http://twitter.com/flow)
function boo(val: boolean) {

}
```

如果我们写`boo(new Boolean(true));`我们会得到下面的错误:

```
Cannot call `boo` with `new Boolean(...)` bound to `val` because `Boolean` [1] is incompatible with boolean [2].
```

## 数字

JavaScript 只有一种类型的数字。它可以是浮点或整数。

流量有`number`和`Number`两种类型。`number`类型用于数值，而`Number`类型是包装器对象。

例如，我们可以写:

```
let x: number = 1;
```

或者我们可以写:

```
let x: Number = new Number(1);
```

然而，我们不能写:

```
let x: number = new Number(1);
```

上面的代码会给我们带来以下错误:

```
[Flow] Cannot assign `new Number(...)` to `x` because `Number` [1] is incompatible with number [2].
```

我们也不能写:

```
let x: Number = 1;
```

这会给我们带来以下错误:

```
[Flow] Cannot assign `1` to `x` because number [1] is incompatible with `Number` [2].
```

## 用线串

流分别有文字和包装对象的`string`和`String`类型。

它像其他原始类型一样工作。所以我们可以写:

```
let x: string = 'abc';
```

或者我们可以写:

```
let x: String = new String('abc');
```

但是我们不能颠倒文字和包装对象的赋值。

Flow 将只接受字符串和其他字符串或数字的串联。这意味着以下内容将起作用:

```
"foo" + "bar";
"foo" + 1;
```

我们还可以将一个字符串文字和一个包装器对象字符串连接起来。例如，我们可以写:

```
"foo" + String('abc');
```

但是，我们不能将一个字符串与用字符串构造函数创建的字符串连接起来。所以下面会给我们一个错误:

```
"foo" + new String('abc');
```

将字符串与任何其他类型连接会给我们带来错误。

## `null`和`void`

Flow 用`null`类型表示`null`值，用`void`表示`undefined`值。

因此，以下内容将会起作用:

```
let x: null = null;
let y: void = undefined;
```

不能将任何其他内容分配给这些类型。函数参数也是如此。

脸书创建了 Flow 来让我们检查代码中数据类型的正确性。它防止对变量和函数参数进行意外的类型赋值。

对于基本类型，它有单独的对象类型，这些对象是用基本对象和文字的构造函数创建的。例如，`string`是字符串文字，`String`是用`new String(...)`创建的字符串。

它还有`null`和`void`两种型号，分别用于`null`和`undefined`。