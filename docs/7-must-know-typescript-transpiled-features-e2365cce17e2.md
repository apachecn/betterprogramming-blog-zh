# 7 个必须知道的打字稿翻译功能

> 原文：<https://betterprogramming.pub/7-must-know-typescript-transpiled-features-e2365cce17e2>

## 将最新的 ECMAScript 功能与 Typescript 一起使用

![](img/8c4b1cbe7e1f472a4ead64310c3d1f5f.png)

由[凯文·Ku](https://unsplash.com/@ikukevk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Typescript 是一种由微软开发和维护的编程语言。它是 Javascript 的超集，为语言增加了输入功能。它具有很酷的特性，如[型防护装置](https://medium.com/better-programming/master-typescripts-type-guards-1fd5436bc6f2)、[映射型](https://medium.com/better-programming/mastering-typescripts-mapped-types-5fa5700385eb)和[联合型](https://medium.com/better-programming/mastering-typescripts-mapped-types-5fa5700385eb)。它非常适合处理大型代码库，因为它的类型化为您的接口提供了一些更全面的契约。

Typescript 将文件转换成 Javascript。在您的`tsconfig`配置文件中，您可以指定您想要将它转换成哪种`target`语言。这意味着您已经可以开箱即用先进的 ECMAScript 功能。

让我们看看 Typescript 可以传输的 7 大特性。

# 1.可选链接

> ****可选链接*** *操作符(* `***?.***` *)允许读取位于连接对象链深处的属性值，而不必明确验证链中的每个引用是否有效。* `*?.*` *操作符的功能与* `*.*` *链接操作符类似，只是如果引用是*[*nullish*](https://developer.mozilla.org/en-US/docs/Glossary/Nullish)*(*`[*null*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)`*或* `[*undefined*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)` *)，则表达式会短路，返回值为* `*undefined*` *。用于函数调用时，如果给定函数不存在，则返回* `*undefined*` *。”**
> 
> **— MDN**

*`ECMAScript 2020`带来了这个牛逼的功能。它在早期的 Typescript 语言中是可用的，并且非常方便。*

*可选属性如下:*

*可选方法如下:*

*堆栈可选赋值器包括:*

*💁当您将 Typescript 配置切换到`strict: true`时，这种可选的链接很方便。它将以一种快速而优雅的方式解决所有这些问题。*

# *2.零合并*

*这是`ECMAScript 2020`规格中附带的新功能。在`Typescript 3.7`中介绍过。你不需要一个最小的`target`来传输这个文件。可以配合`ES3/ES5`使用。*

> ***无效合并运算符(** `***??***` **)** 是一个逻辑运算符，当其左侧操作数为`[*null*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)`或`[*undefined*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`时，返回其右侧操作数，否则返回其左侧操作数*
> 
> *— MDN*

*⚠️注意到，合并运算符没有考虑像`0`这样虚假值。它将被计为一个实际变量。当用这个替换基于`truthy/falsy`的操作符时，你必须小心。*

# *3.逻辑赋值运算符*

*Javascript 很早就支持`assignment operators`对数字进行操作。使用`ES2021`，将支持逻辑运算符。*

*从 Typescript `4.0`开始支持这个特性，并且不需要最小的`target`配置。*

*下面让我们来看看它的实际应用:*

*如果你已经习惯了`assignment operators`，你会发现它使用起来既简单又直观。*

# *4.众所周知的 ES6 功能*

## *Const and let*

*`const`和`let`是对`ES6`非常著名的补充。有很多人使用这个特性，然后使用 [Babel](https://babeljs.io/) 将其传输到`ES5`。Typescript 将免费为您完成这项工作。你只需要在`tsconfig`上设置相应的`target`。下面是一些代码:*

*如果您的目标是`ES5`，这是输出:*

```
*var MAX_ATTEMPTS = 1;
var createTask = function () {
    var attempts = 0;
    return function () {
        if (attempts < MAX_ATTEMPTS) {
            console.log('success');
            attempts++;
            return;
        }
        console.log('error');
    };
};
var task = createTask();
task(); // success
task(); // error*
```

## *模板字符串*

*`ES6`推出这一众所周知的功能。*

> *模板文字是允许嵌入表达式的字符串文字。您可以使用多行字符串和字符串插值功能。”*
> 
> *— MDN*

*它可以在`tsconfig`中没有任何最小值`target`的情况下用于打字稿。*

```
*const logParam = (param: string) => {
  console.log(`param ${param}`);
}logParam('Hello World');*
```

*`ES5`的输出将为:*

```
*var logParam = function (param) {
    console.log("param " + param);
};logParam('Hello World');*
```

## *箭头功能*

> *“箭头函数表达式是传统[函数表达式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)的紧凑替代形式，但有局限性，不能在所有情况下使用。”*
> 
> *— MDN*

```
*const trueArrowFunction = () => true;console.log(trueArrowFunction();); // true*
```

*⚠️注意，滥用箭头函数可能会对内存使用产生一些影响。如果你想保持高性能，只在需要的时候使用它们。*

# *5.私有访问器*

*当处理类时，拥有一些私有属性可能是有用的。Typescript 允许通过使用`private`修饰符来使用它。这样，类中唯一的方法将访问该成员。*

*注意，这只是糖语法，属性将在 Javascript 对象中。如果您尝试访问 Typescript，它将在编译时失败。下面是一些代码示例:*

*这个特性很快就会在`ECMAScript`中出现。你可以在这里找到提案。它已经可供您在 Typescript 中使用；但是，您需要将`target`设置为`ES6`或更高。下面是一些代码:*

```
*Property '#lives' is not accessible outside class 'Cat' because it has a private identifier.*
```

*你可以选择使用`private`修改器或者使用`ECMAScript`方法。这取决于您的`target` javascript 版本以及您是否正在使用 polyfill。*

# *6.异步和等待*

*`async/await`的特点是围绕承诺的甜言蜜语。它允许您以同步的方式处理异步 JavaScript 代码。它防止你陷入回调地狱，并使你的代码更具可读性。Typescript 要求最少的`target`为`ES6`，少于该规范发布时的原始`ES7`。*

*`ES6`支持`97.29%`，所以你可以决定是否要为此添加一个多填充。*

*`async/await`的例子:*

```
*const fetchAllData = async () => {
  await Promise.resolve(true);
  await Promise.resolve(true); return true;
}*
```

*然而，`Top level async/await`是作为`ES2020`的一部分发货的。为了使用这个特性，您需要一些配置设置:*

*   *打字稿`3.8`或更高版本*
*   *`target`编译器选项必须是`ES2017`或更高版本*
*   *`module`编译器选项必须是`esnext`或`system`。*

*之所以需要移植到`ES2017`或更新的环境，是因为它需要一个支持`ES`模块的环境。*

*让我们来看一个`Top Level Await`的例子:*

```
*const featureEnabled = await Promise.resolve(true);export const isFeatureEnabled = () => featureEnabled;*
```

*点击[这里](https://www.typescriptlang.org/play?ssl=3&ssc=54&pln=1&pc=1#code/MYewdgzgLgBAZgUwIZQK4CcEFExIEYA2CAJjALwxIDuSAlrAArogC2tECAdJhCAQG4IAFFHSoEASgDcAKBkIAHgAcQ6WKEix2AMWRpMOfEVIUhE8gD54ejNlyESUoA)查看 Typescript Playground 上的现场演示。*

*⚠️你需要有一个`export/import`语句，因为这个特性只有模块支持。*

*⚠️如果您的 Typescript 配置没有正确设置，您将在使用顶级`async`时面临以下错误:*

```
*Top-level 'await' expressions are only allowed when the 'module' option is set to 'esnext' or 'system', and the 'target' option is set to 'es2017' or higher.*
```

*点击[查看 Typescript Playground 上的现场演示](https://www.typescriptlang.org/play?target=3#code/MYewdgzgLgBAZgUwIZQK4CcEFExIEYA2CAJjALwxIDuSAlrAArogC2tECAdJhCAQG4IAFFHSoEASgDcAKBkIAHgAcQ6WKEix2AMWRpMOfEVIUhE8gD54ejNlyESUoA)。*

*一个常见的用例是，当应用程序加载时，您希望延迟加载模块上的信息。这样，您可以生成一个较小的包，通过网络发送，并延迟加载所有可以等待的服务和功能。注意不要影响 UX 体验——滥用惰性加载是有害的。*

# *7.装修工*

> *随着 TypeScript 和 ES6 中类的引入，现在存在某些需要附加功能来支持注释或修改类和类成员的场景。Decorators 提供了一种为类声明和成员添加注释和元编程语法的方法。*
> 
> *—类型脚本语言*

*装饰器是一种众所周知的模式，在许多编程语言中都可以使用。他们目前处于第二阶段提案阶段，但他们已经在打字稿中存在了相当长的时间。这是谷歌和微软团队的首次合作之一。它们在`Angular`库中被大量使用。*

*让我们看看启用该功能所需的`tsconfig`参数:*

```
*{
  "[compilerOptions](https://www.typescriptlang.org/tsconfig#compilerOptions)":
  {
    "[target](https://www.typescriptlang.org/tsconfig#target)": "ES5",
    "[experimentalDecorators](https://www.typescriptlang.org/tsconfig#experimentalDecorators)": true
  }
}*
```

*有 3 种主要的装饰者:*

*   *类装饰者*
*   *方法装饰者*
*   *物业装饰*

*为了简单起见，我们将看到一个基本的方法装饰器。我们将创建一个装饰器来记录哪个`method`被调用及其参数。*

# *包裹*

*我们已经简要地看了开箱即用的 Typescript transpiles 的主要特性。这些`ECMAScript`特性出现在类型脚本世界比 Javascript 世界早得多。*

*Typescript 之所以出色有很多原因，能够使用前沿特性无疑是一个重要原因。不要认为 Typescript 只是将类型引入 JavaScript 代码，而是将类型+特性引入代码。*

*更多的 Typescript 内容将会出现。*

*干杯*

# *有关系的*

*[](/top-5-typescript-features-you-should-master-2358db9ab3d5) [## 您应该掌握的五大打字稿功能

### 使用这些必须知道的特性来提高您的打字稿知识

better 编程. pub](/top-5-typescript-features-you-should-master-2358db9ab3d5) [](https://medium.com/better-programming/getting-strict-with-typescript-2e906b48c0a) [## 对类型脚本越来越严格

### 使您的类型脚本代码更具可读性和类型安全

medium.com](https://medium.com/better-programming/getting-strict-with-typescript-2e906b48c0a)*