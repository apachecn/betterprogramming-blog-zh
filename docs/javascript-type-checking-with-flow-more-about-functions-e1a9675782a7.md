# 使用流进行 JavaScript 类型检查

> 原文：<https://betterprogramming.pub/javascript-type-checking-with-flow-more-about-functions-e1a9675782a7>

## 我们看看参数、函数类型、“这个”等等

![](img/67a868f223155c68c5911eba8a8f8f53.png)

照片由 [Adrien Converse](https://unsplash.com/@lurm?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在[unplash](https://unsplash.com/s/photos/flow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

[Flow](https://github.com/facebook/flow) 是脸书生产的一种类型检查器，用于检查 JavaScript 数据类型。它有许多内置的数据类型，我们可以用来注释变量和函数参数的类型。

在本文中，我们将了解更多关于 Flow 函数的属性，包括箭头函数、函数类型等等。

# 箭头函数

箭头函数是在函数内部没有不同值`this`的函数。

我们可以向箭头函数添加类型注释，就像对流程中的任何其他函数一样。例如，我们可以写:

```
const foo = (str: string, bool?: boolean, ...nums: Array<number>): void => {
  console.log(nums);
}
```

# 功能类型

我们可以指定参数类型，并将函数的类型作为自己的类型注释返回。这叫做*函数类型。*

类型注释可以写如下:

```
const foo: (str: string, bool?: boolean, ...nums: Array<number>) => void = (str: string, bool?: boolean, ...nums: Array<number>): void => {
  console.log(nums);
}
```

在上面的代码中，代码…

```
(str: string, bool?: boolean, ...nums: Array<number>) => void
```

…是`foo`函数的类型注释。它指定第一个参数是字符串，第二个是可选的布尔值，其余的是数字。此外，返回类型为`void`，如肥胖箭头后所述。

函数类型注释对于回调函数这样的事情很方便:

```
const fn = (callback: (a: number, b: number) => number) => {}
```

# 功能参数

可以通过在参数名称后添加一个冒号，然后在参数的类型批注后添加一个类型批注来添加函数参数。

例如，我们可以写:

```
function method(a: string, b: boolean) {

}
```

然后`a`接受一个字符串，`b`接受一个布尔值。

# 可选参数

我们可以通过在参数名称后添加问号来添加可选参数。

例如，我们可以写:

```
function foo(optionalString?: string) {

}
```

然后，我们可以输入`undefined`、nothing，或者从`string`到`foo`:

```
foo();          
foo(undefined); 
foo("string");
```

传递任何其他信息都会给我们带来错误。例如，写作…

```
foo(null);
```

…会给我们带来错误，因为它不是`string`、没有或`undefined`。

# 休息参数

使用 rest 运算符，我们可以将任意数量的参数传递给一个函数。如果参数的数量大于固定参数的数量，那么 rest 运算符会将其他参数存储在一个数组中。

例如，我们可以写:

```
function foo(a: number, ...args: Array<number>) {
  console.log(args);
}
```

然后，当我们这样称呼它时…

```
foo(1,2,3);
```

…我们将得到`[2,3]`作为`args`的值，因为在`args`上操作的 rest 操作符将超出第一个参数的值发送给`args`参数。

在上面的代码中，我们还指定`args`是一个数字数组，所以传入任何东西都会导致错误。

类型注释必须是一个`Array`类型，因为在 JavaScript 中，rest 操作符参数总是存储在一个数组中。

# 函数返回类型

我们可以通过在参数列表后添加类型注释来指定函数的返回类型。例如，如果我们想定义一个返回字符串的函数，那么我们可以编写下面的代码来创建一个返回字符串的函数:

```
function foo(): string {
  return 'foo';
}
```

如果为函数定义了返回类型，那么它必须在代码的所有分支中返回相同的数据类型。例如，如果我们写…

```
function foo(): string {
  if (Math.random() > 0.5){
    return 'foo';
  }
}
```

…这会给我们一个错误，因为`foo`并不总是返回一个字符串。要解决这个问题，我们可以写:

```
function foo(): string {
  if (Math.random() > 0.5){
    return 'foo';
  }
  return 'bar';
}
```

然后所有的分支返回一个字符串，流接受它为有效的。

函数总是返回一个承诺，所以如果我们想包含它，返回类型的注释必须是一个承诺。

例如，我们应该这样写:

```
async function foo(): Promise<string> {
  return 'foo';
}
```

![](img/be1d4d577a4e82d062f067f534482226.png)

照片由[徐川](https://unsplash.com/@chuanxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 这个的价值

对于箭头函数以外的函数,`this`的值根据函数的上下文而变化。在功能代码的顶层，`this`将取该功能的值。

在 Flow 中，我们不注释`this`的值，我们应该让类型由我们调用函数的任何上下文来推断。

例如，如果我们用一个数字调用一个函数`foo`，如下…

```
function foo() {
  return this;
}let num: number = foo.call(1);
```

…那么`this`将是一个数字。

# 谓词函数

在 Flow 中，我们可以用`%checks`关键字将一个函数标记为谓词函数。谓词函数是检查条件的函数。

我们可以通过编写以下代码来定义一个:

```
function areStrings(a, b): boolean %checks {
  return typeof a === 'string' && typeof b === 'string';
}
```

然后，我们可以在下面的代码中使用它:

```
function foo(a, b): string {
  if (areStrings(a, b)){
    return a + b;
  }
  return '';
}
```

我们也可以在其他谓词函数中调用谓词函数。例如，我们可以将前面的示例重写如下:

```
function isString(a): boolean %checks {
  return typeof a === 'string'
}function areStrings(a, b): boolean %checks {
  return isString(a) && isString(b);
}function foo(a, b): string {
  if (areStrings(a, b)){
    return a + b;
  }
  return '';
}
```

# 可调用对象

我们可以输入一个可调用的对象。例如，我们可以写:

```
type CallableObject = {
  (string, string): string,
  foo: string
};function concat(x, y) {
  return x + y;
}concat.foo = "hello world";
(concat: CallableObject);
```

对于一个被认为是`CallableObject`类型的对象，我们必须将其定义为一个具有所需签名和返回类型的函数。然后，我们必须添加所需的`foo`属性。

# `Function`式

我们可以通过对函数使用`(...args: Array<any>) => any`来指定更灵活的类型。这样，函数可以有任何签名并返回任何内容。例如，我们可以写:

```
function foo(fn: (...args: Array<any>) => any){
  return fn();
}
```

然后，我们可以传入任何函数作为`foo`的回调函数:

```
foo((a) => true);
foo((a , b) => a + b);
```

我们可以给参数添加类型注释，并返回函数的类型。此外，通过指定签名和返回类型，我们可以对函数本身进行类型注释。

此外，我们可以用`%check`关键字指定谓词函数类型。我们也可以对 Flow 使用 rest 参数。rest 参数必须具有数组类型。对于`async`函数，如果我们要添加一个返回类型，我们必须有一个`Promise`返回类型。

可选参数的名称后面应该有一个问号。

其他规则与 JavaScript 相同。