# JavaScript 中为什么需要严格模式？

> 原文：<https://betterprogramming.pub/why-do-we-need-strict-mode-in-javascript-df34771eb950>

## 这是什么？这是为了什么？我们为什么要使用它？

![](img/8e3767e880b873ebb460847a6f61de8d.png)

照片由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

严格模式是现代 JavaScript 的重要组成部分。这是一种允许我们选择更受限制的 JavaScript 语法的模式。

严格模式的语义不同于旧的 JavaScript 的“草率模式”,后者具有更宽松的语法，并且在代码中出现错误时会被隐藏。这意味着错误被忽略，代码运行时可能会出现意外结果。

严格模式对 JavaScript 语义做了几处修改。它消除了静默错误，而是抛出它们，这样代码就不会在运行时出现错误。

它还会指出妨碍 JavaScript 引擎进行优化的错误。此外，它还禁止可能在 JavaScript 的未来版本中定义的特性。

严格模式可以应用于单个函数或整个脚本。它不能只应用于花括号中的语句或其他块。为了让脚本使用严格模式，我们在脚本的顶部添加了语句`"use strict"`或`'use strict'`在任何其他语句之前。

如果我们有一些使用严格模式的脚本和一些不使用严格模式的脚本，那么我们可能有一些使用严格模式的脚本与其他不使用严格模式的脚本连接在一起。

这意味着不使用严格模式的代码在连接在一起时可能会使用严格模式。反之亦然。所以，最好不要混在一起。

我们也可以将此应用于函数。为此，我们将语句`"use strict"`或`'use strict'`添加到主体顶部的函数中，放在任何其他语句之前。它适用于内部的一切，包括嵌套在使用严格模式的函数中的函数。

例如:

```
const strictFunction = ()=>{
  'use strict';
  const nestedFunction = ()=>{
    // this function also uses strict mode
  }
}
```

ES2015 中引入的 JavaScript 模块自动启用了严格模式，因此无需任何语句即可启用。

# 严格模式下的变化

严格模式改变使用它的代码的语法和运行时行为。代码中的错误被转换为运行时抛出的语法错误，简化特定变量的计算方式，简化`eval`函数和`arguments`对象的更改，以及可能在未来 es 规范中实现的更改。

# 将错误转化为错误

错误转化为错误。它们以前是以非严格模式接受的。严格模式限制错误语法的使用，不会让代码在有错误的情况下运行。

它不允许我们用`var`、`let`或`const`来声明变量，这使得创建全局变量变得困难，所以创建变量而不用这些关键字来声明它们是行不通的。例如，下面的代码将抛出一个`ReferenceError`:

```
'use strict';
badVariable = 1;
```

我们不能在严格模式下运行上面的代码，因为如果严格模式关闭，这段代码将创建一个全局变量`badVariable`。严格模式可以防止意外创建全局变量。

任何静默失败的代码现在都会抛出异常。这包括之前被忽略的任何无效语法。

例如，在严格模式下，我们不能不给只读变量赋值，比如`arguments`、`NaN`或`eval`。

任何对只读属性的赋值，比如不可写的全局属性、getter-only 属性的赋值，以及对不可扩展对象的属性赋值，都会在严格模式下抛出异常。

下面是一些在严格模式下会失败的语法示例:

```
'use strict';

let undefined = 5; 
let Infinity = 5;

let obj = {};
Object.defineProperty(obj, 'foo', { value: 1, writable: false });
obj.foo = 1

let obj2 = { get foo() { return 17; } };
obj2.foo = 2

let fixedObj = {};
Object.preventExtensions(fixedObj);
fixed.bar= 1;
```

上面所有的例子都会抛出一个`TypeError`。`undefined`和`Infinity`是不可写的全局对象。`obj`是不可写的属性。

`obj2`的`foo`属性是仅 getter 属性，因此无法设置。`fixedObj`被阻止使用`Object.preventExtensions`方法向其添加更多属性。

此外，当有代码试图删除不可删除的属性时，会抛出一个`TypeError`。例如:

```
'use strict';
delete Array.prototype
```

这样会抛出一个`TypeError`。

在引入 ES6 之前，严格模式也不允许对象中有重复的属性名，因此以下示例将引发语法错误:

```
'use strict';
var o = { a: 1, a: 2 };
```

严格模式要求函数参数名是唯一的。在没有严格模式的情况下，如果两个参数的名称都是*一个是*，那么当传入参数时，后面定义的那个将被接受为参数的值。

在严格模式下，不再允许多个函数参数具有相同的名称，因此以下示例将无法运行，并出现语法错误:

```
const multiply = (x, x, y) => x*x*y;
```

在严格模式下也不允许八进制语法。这不是规范的一部分，但是浏览器通过在八进制数前加一个 0 来支持它。

这让开发人员感到困惑，因为有些人可能认为数字前面的 0 没有意义。因此，严格模式不允许此语法，并将引发语法错误。

严格模式还防止使用使优化变得困难的语法。在进行优化之前，它需要知道一个变量实际上是存储在它认为的存储位置，所以我们必须防止这种阻止优化发生的语法。

这方面的一个例子是`with`语句。如果我们使用它，它会阻止 JavaScript 解释器知道你指的是哪个变量或属性，因为在`with`语句内部或外部可能有同名的变量。

如果我们有类似下面的代码:

```
let x = 1;
with (obj) {
  x;
}
```

然后，JavaScript 不会知道`with`语句中的`x`是指`x`变量还是`obj`、`obj.x`的属性。

所以`x`的存储位置是不明确的。因此，严格模式阻止使用`with`语句。如果我们有如下的严格模式:

```
'use strict';
let x = 1;
with (obj) {
  x;
}
```

那么上面的代码就会有语法错误。

严格模式阻止的另一件事是在`eval`语句中声明变量。

例如，如果没有严格模式，`eval('let x')`会在代码中声明变量`x`。这让人们可以隐藏字符串中的变量声明，这些声明可能会覆盖`eval`语句之外的相同变量声明。

为了防止这种情况，严格模式不允许在我们传递给`eval`语句的字符串参数中声明变量。

严格模式还禁止删除普通变量名，因此下面将引发语法错误:

```
'use strict';

let x;
delete x;
```

# 禁止无效语法

严格模式下不允许`eval`和`argument`的无效语法。

这意味着对它们的任何操作都是不允许的，比如给它们赋值或者用它们作为变量、函数或者函数中参数的名字。

以下是不允许的`eval`和`argument`对象的无效使用示例:

```
'use strict';
eval = 1;
arguments++;
arguments--;
++eval;
eval--;
let obj = { set p(arguments) { } };
let eval;
try { } catch (arguments) { }
try { } catch (eval) { }
function x(eval) { }
function arguments() { }
let y = function eval() { };
let eval = ()=>{ };
let f = new Function('arguments', "'use strict'; return 1;");
```

严格模式不允许为`arguments`对象创建别名并通过别名设置新值。

在没有严格模式的情况下，如果函数的第一个参数是`a`，那么设置`a`也会设置`arguments[0]`。在严格模式下，`arguments`对象总是拥有调用函数的参数列表。

例如，如果我们有:

```
const fn = function(a) {
  'use strict';
  a = 2;
  return [a, arguments[0]];
}console.log(fn(1))
```

然后我们应该看到`[2,1]`被记录。这是因为将`a`设置为 2 并不会将`arguments[0]`也设置为 2。

# 性能优化

另外，不再支持`arguments.callee`。如果没有严格模式，它所做的就是返回被调用的函数的名字，这个函数就是`arguments.callee`所在的函数。

它阻止了像内联函数这样的优化，因为`arguments.callee`要求如果访问`arguments.callee`的话，对非内联函数的引用是可用的。所以在严格模式下，`arguments.callee`现在抛出一个`TypeError`。

使用严格模式，`this`将不会被强制始终作为一个对象。如果函数的`this`与`call`、`apply`或`bind`绑定到任何非对象类型，如`undefined`、`null`、`number`、`boolean`等原始类型，那么它们必须被强制成为对象。

如果`this`的上下文切换到非对象类型，那么全局`window`对象将取代它的位置。这意味着全局对象被暴露给被调用的函数，而`this`被绑定到非对象类型。

例如，如果我们运行以下代码:

```
'use strict';function fn() {
  return this;
}
console.log(fn() === undefined);
console.log(fn.call(2) === 2);
console.log(fn.apply(null) === null);
console.log(fn.call(undefined) === undefined);
console.log(fn.bind(true)() === true);
```

所有的控制台日志都将是`true`，因为当`this`被更改为非对象类型时，函数中的`this`不会自动转换为`window`全局对象。

# 安全修复

在严格模式下，我们也不允许将函数的`caller`和`arguments`公开，因为`caller`可能会公开调用该函数的`caller`属性所访问的函数的函数。

调用函数时，`arguments`有传入的参数。例如，如果我们有一个名为`fn`的函数，那么通过`fn.caller`，我们可以看到调用了`fn`的函数，通过`fn.arguments`，我们可以看到在调用`fn`时传递给`fn`的参数。

这是一个潜在的安全漏洞，可以通过禁止访问函数的这两个属性来消除。

```
function secretFunction() {
  'use strict';
  secretFunction.caller;    
  secretFunction.arguments;
}
function restrictedRunner() {
  return secretFunction();
}
restrictedRunner();
```

在上面的例子中，我们不能用严格模式访问`secretFunction.caller`和`secretFunction.arguments`，因为人们可能会用它来获取函数的调用栈。如果我们运行该代码，将会抛出一个`TypeError`。

在 JavaScript 的未来版本中，将成为受限关键字的标识符将不允许用于变量或属性名等标识符。

以下关键字不允许用于在代码中定义我们的标识符。`implements`、`interface`、`let`、`package`、`private`、`protected`、`public`、`static`和`yield`。

对于 ES2015 或更高版本，这些已成为保留字，因此在没有严格模式的情况下，它们肯定不能用于命名变量和对象属性。

几年来，严格模式一直是一个标准。浏览器对它的支持是常见的，只有像 Internet Explorer 这样的旧浏览器可能会有问题。

其他浏览器在使用严格模式时应该不会有问题。因此，它应该用于防止错误和避免安全风险，如暴露调用堆栈或在`eval`中声明新变量。

此外，它消除了静默错误，而是抛出它们，这样代码就不会在运行时出现错误。它还会指出妨碍 JavaScript 引擎进行优化的错误。

此外，它还禁止可能在 JavaScript 的未来版本中定义的特性。