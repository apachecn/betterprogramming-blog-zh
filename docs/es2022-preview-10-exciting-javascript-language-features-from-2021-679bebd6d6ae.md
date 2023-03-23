# ES2022 预览版:2021 年的 10 个激动人心的 JavaScript 语言特性

> 原文：<https://betterprogramming.pub/es2022-preview-10-exciting-javascript-language-features-from-2021-679bebd6d6ae>

## 顶级 await、RegExp 等等

![](img/bce17bd307577f0de6a13942796095f9.png)

照片由[丹尼尔·阿克谢诺夫](https://unsplash.com/@nebe3etogo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 发展很快。在 2021 年，几个[提案](https://www.proposals.es/)已经移动到 [TC39 流程](https://tc39.es/process-document/)的第 4 阶段，并将包含在 ES2022 中。他们给 JavaScript 增加了以下特性:

## **类和实例**

*   私有实例字段、方法和访问器
*   私有字段的存在性检查
*   公共静态类字段
*   私有静态类字段和方法
*   静态类初始化块

## **模块加载**

*   顶级`await`

## **内置物体**

*   错误:`.cause`
*   数组、字符串和类型数组:`.at()`
*   对象:`.hasOwn()`
*   正则表达式:匹配`.indices` (' `d`'标志)

这篇博客文章描述了每个功能，展示了如何使用它的示例，并查看了当前的浏览器和 Node.js 支持(截至 2021 年 12 月)。让我们开始吧。

# 私有实例字段、方法和访问器

封装是面向对象编程的核心原则之一。它通常使用可见性修饰符来实现，如`private`或`public`。

私有实例字段、方法和访问器特性[ [1](https://github.com/tc39/proposal-class-fields) 、 [2](https://github.com/tc39/proposal-private-methods) ]为 JavaScript 增加了硬性可见性限制。前缀`#`将类中的字段、方法或访问器标记为私有，这意味着您不能从实例本身之外访问它。

下面是一个私有字段和方法的例子；访问器的工作方式类似:

```
class Example {
  #value; constructor(value) {
    this.#value = value;
  } #calc() {
    return this.#value * 10;
  } print() {
    console.log(this.#calc());
  }
}const object = new Example(5);
console.log(object.#value);    // SyntaxError
console.log(object.#calc());   // SyntaxError
object.print();                // 50
```

[大多数浏览器](https://caniuse.com/mdn-javascript_classes_private_class_fields)(2021 年 12 月使用率:~90%)和 Node.js 12+都支持私有实例字段。在浏览器中[对私有方法和访问器的支持更加有限](https://caniuse.com/mdn-javascript_classes_private_class_methods)(2021 年 12 月使用率:~80%)。Node.js 从 14.6 版本开始支持该特性。你可以[用 Babel](https://blog.saeloun.com/2021/06/24/babel-enables-class-field-and-private-methods.html) 传输你的代码，在不直接支持私有类字段和方法的环境中使用它们。

# 私有字段的存在性检查

因为试图访问一个对象上不存在的私有字段会抛出异常，所以需要能够检查一个对象是否有给定的私有字段。`[in](https://github.com/tc39/proposal-private-fields-in-in)` [操作符可用于检查对象](https://github.com/tc39/proposal-private-fields-in-in)上的私有字段是否可用:

```
class Example {
  #field static isExampleInstance(object) {
    return #field in object;
  }
}
```

对于在私有字段上使用 `[in](https://caniuse.com/mdn-javascript_classes_private_class_fields_in)` [操作符的](https://caniuse.com/mdn-javascript_classes_private_class_fields_in)[浏览器支持是有限的(2021 年 12 月使用率:~70%)。Node.js 从 16.4 版开始支持该功能。您可以通过](https://caniuse.com/mdn-javascript_classes_private_class_fields_in)为私有字段 `[in](https://babeljs.io/docs/en/babel-plugin-proposal-private-property-in-object)` [操作符](https://babeljs.io/docs/en/babel-plugin-proposal-private-property-in-object)[传递文件用法。](https://babeljs.io/docs/en/babel-plugin-proposal-private-property-in-object)

# 公共静态类字段

[静态类字段](https://github.com/tc39/proposal-static-class-features#static-public-fields)是向类对象添加属性的一种方便的符号。

```
// without static class fields:
class Customer {
  // ...
}
Customer.idCounter = 1;// with static class fields:
class Customer {
  static idCounter = 1;
  // ...
}
```

[大部分浏览器](https://caniuse.com/mdn-javascript_classes_static_class_fields)(2021 年 12 月使用率:~90%)和 Node.js 12+都支持公共类字段。

# 私有静态类字段和方法

与私有实例字段和方法类似，封装和可见性限制在类级别上是有帮助的。[私有静态方法和字段特性](https://github.com/tc39/proposal-static-class-features)使用`#`前缀为类级别的字段和方法添加了硬可见性限制。

```
class Customer {
  static #idCounter = 1; // static private static #getNextId() { // static private
    return Customer.#idCounter++;
  } #id; // instance private constructor() {
    this.#id = Customer.#getNextId();
  } toString() {
    return `c${this.#id}`;
  }
}const customers = [new Customer(), new Customer()];
console.log(customers.join(' ')); // c1 c2
```

浏览器和 Node.js 支持类似于上面的私有实例字段和方法。

# 静态类初始化块

有时对静态类字段进行更复杂的初始化工作是必要的或方便的。对于上面的私有静态字段特性，这种初始化甚至必须发生在类内部，因为私有字段不可访问。

[静态初始化块特性](https://github.com/tc39/proposal-class-static-block)提供了一种在类定义评估期间执行代码的机制。初始化类时，执行带有`static`关键字的块语句中的代码:

```
class Example {
  static propertyA;
  static #propertyB; // private static { // static initializer block
    try {
      const json = JSON.parse(fs.readFileSync('example.json', 'utf8'));
      this.propertyA = json.someProperty;
      this.#propertyB = json.anotherProperty;
    } catch (error) {
      this.propertyA = 'default1';
      this.#propertyB = 'default2';
    }
  } static print() {
    console.log(Example.propertyA);
    console.log(Example.#propertyB);
  }
}Example.print();
```

[浏览器对静态类初始化块](https://caniuse.com/mdn-javascript_classes_static_initialization_blocks)的支持是有限的(2021 年 12 月:~70%)。Node.js 从 16.4 版开始支持该功能。你可以[用 Babel](https://babeljs.io/docs/en/babel-plugin-proposal-class-static-block) 用静态初始化器块传输代码。

# 顶级等待

ES2017 中引入了异步函数和`await`关键字，以简化承诺的处理。然而，`await`只能在`async`函数内部使用。

ES 模块的[顶级](https://github.com/tc39/proposal-top-level-await) `[await](https://github.com/tc39/proposal-top-level-await)` [特性使得在 CLI 脚本中使用`await`(例如，使用`.mjs`源和](https://github.com/tc39/proposal-top-level-await) [zx](https://github.com/google/zx) )以及动态导入和数据加载变得非常容易。它将`await`功能扩展到模块加载器中，这意味着从属模块将等待异步模块(带有顶级`await`)被加载。

这里有一个例子:

```
// load-attribute.mjs 
// with top-level await
const data = await (await fetch("https://some.url")).text();
export const attribute = JSON.parse(data).someAttribute;// main.mjs 
// loaded after load-attribute.mjs is fully loaded
// and its exports are available
import { attribute } from "./load-attribute.mjs";
console.log(attribute);
```

顶级 await 是现代浏览器(2021 年 12 月使用率:~80%)和 Node.js 14.8+支持的[。它只适用于 ES 模块，而且 CommonJS 模块是否会得到顶级的 await 支持也是个疑问。带有顶层`await`的代码可以在捆绑阶段进行传输，以支持更老的浏览器，比如](https://caniuse.com/mdn-javascript_operators_await_top_level) [Webpack 5](https://webpack.js.org/configuration/experiments/) `[experiments.topLevelAwait = true](https://webpack.js.org/configuration/experiments/)`。

# 错误:。原因

错误通常被包装以提供有意义的消息并记录错误上下文。然而，这意味着原始错误可能会丢失。出于日志记录和调试目的，需要将原始错误附加到包装错误上。

[错误原因特性](https://github.com/tc39/proposal-error-cause)提供了一种将原始错误附加到包装错误的标准化方法。它向`Error`构造函数添加了`cause`选项，并添加了一个用于检索原始错误的`cause`字段。

```
const load = async (userId) => {
  try {
    return await fetch(`https://service/api/user/${userId}`);
  } catch (error) {
    throw new Error(
      `Loading data for user with id ${userId} failed`, 
      { cause: error }
    );
  }
}try {
  const userData = await load(3);
  // ...
} catch (error) {
  console.log(error); // Error: Loading data for user with id 3 failed
  console.log(error.cause); // TypeError: Failed to fetch
}
```

当前浏览器对错误子句功能的支持是有限的(2021 年 12 月使用率:~70%)。Node.js 从 16.9 版本开始支持该功能。您可以使用[错误导致 polyfill](https://github.com/es-shims/error-cause) 立即开始使用该功能，即使在不支持该功能的 JS 环境中。

# 数组、字符串和类型数组:。在()

从数组或字符串的末尾获取元素通常需要从数组的长度中减去，例如，`let lastElement = anArray[anArray.length - 1]`。这要求数组存储在临时变量中，并防止无缝链接。

`[.at()](https://github.com/tc39/proposal-relative-indexing-method)` [特性](https://github.com/tc39/proposal-relative-indexing-method)提供了一种在没有临时变量的情况下从字符串或数组的开头(正索引)或结尾(负索引)获取元素的方法。

```
const getExampleValue = () => 'abcdefghi';console.log(getExampleValue().at(2));    // c
console.log(getExampleValue()[2]);       // cconst temp = getExampleValue();
console.log(temp[temp.length - 2]);      // h
console.log(getExampleValue().at(-2));   // h - no temp var needed
```

[浏览器对。at 特性](https://caniuse.com/mdn-javascript_builtins_array_at)目前受限(2021 年 12 月使用率:~70%)，仅在 Node.js 16.6+中可用。可以用[。同时在()polyfill 从核心 JS](https://github.com/zloirock/core-js#relative-indexing-method) 开始。

# 对象:。hasOwn()

`[Object.hasOwn](https://github.com/tc39/proposal-accessible-object-hasownproperty)` [特性](https://github.com/tc39/proposal-accessible-object-hasownproperty)是一种更简洁、更健壮的检查属性是否直接在对象上设置的方法。这是使用`hasOwnProperty`的首选替代方案:

```
const example = {
  property: '123'
};console.log(Object.prototype.hasOwnProperty.call(example, 'property'));
console.log(Object.hasOwn(example, 'property')); // preferred
```

[浏览器支持目前有限](https://caniuse.com/mdn-javascript_builtins_object_hasown)(2021 年 12 月使用率:~70%)，需要 Node 16.9+才能直接使用`hasOwn`。同时， `[hasOwn](https://github.com/zloirock/core-js/#accessible-objectprototypehasownproperty)`有一个[核心 JS polyfill。](https://github.com/zloirock/core-js/#accessible-objectprototypehasownproperty)

# RegExp:匹配索引(' d '标志)

默认情况下，正则表达式匹配记录匹配文本的开始索引，但不记录其结束索引，也不记录其捕获组的开始和结束索引。对于文本编辑器语法或搜索结果突出显示等用例，将捕获组匹配索引作为正则表达式匹配的一部分会很有帮助。

使用[正则表达式匹配索引特性(‘d’标志)](https://github.com/tc39/proposal-regexp-match-indices)，匹配和捕获组索引在正则表达式结果的`indices`数组属性中可用。
匹配文本位置和匹配索引位置是相同的，例如，完全匹配的文本是匹配数组和索引数组中的第一个值。命名的捕获组的索引记录在`indices.groups`中。

这里有一个例子:

```
const text = "Let's match one:1.";
const regexp = /match\s(?<word>\w+):(?<digit>\d)/gd;for (const match of text.matchAll(regexp)) {
    console.log(match);
}
```

上面的示例代码有以下输出:

```
[
  'match one:1',
  'one',
  '1',
  index: 6,
  input: "Let's match one:1.",
  groups: { word: 'one', digit: '1' },
  indices: {
    0: [6,17],
    1: [12,15],
    2: [16,17],
    groups: { 
      digit: [16, 17],
      word: [12, 15]
    }
  }
]
```

对于 RegExp 匹配索引特性的[浏览器支持目前是有限的](https://caniuse.com/mdn-javascript_builtins_regexp_hasindices)(2021 年 12 月使用率:~80%)。在 Node.js 中，可以用`--harmony-regexp-match-indices`标志激活这个特性，但是默认情况下是禁用的。您可以同时使用 [RegExp 匹配索引 polyfill](https://github.com/rbuckton/regexp-match-indices) 。

# 结论

来自 2021 的新 JavaScript 特性有助于使开发更加方便和健壮，其中大多数已经可以在最新的浏览器和 Node.js 环境中工作。

然而，许多用户仍然使用不完全支持 ES2022 的浏览器和环境。对于生产使用，检查目标环境并根据需要使用 polyfilling 和 transpiling，或者在使用新功能之前等待一段时间，这是非常重要的。

2022 年编码快乐！