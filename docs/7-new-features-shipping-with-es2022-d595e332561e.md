# ES2022 附带 6 项新的 JavaScript 功能

> 原文：<https://betterprogramming.pub/7-new-features-shipping-with-es2022-d595e332561e>

## 一个顶级 await，`.at()`方法在所有内置的可索引上，等等

![](img/99f406be50b1e0f5f90c7815ce340217.png)

[通风视图](https://unsplash.com/@ventiviews?utm_source=medium&utm_medium=referral)对[防溅罩](https://unsplash.com?utm_source=medium&utm_medium=referral)的拍照

你可能知道，从 2015 年 ES6 开始，Ecma International 的 TC39 每年都会发布 ECMAScript 的新版本。ECMAScript 2022 是 ECMAScript 语言规范的第 13 版。

这个新的迭代有它自己的一批新特性——让我们来回顾一下！

# 1.顶级等待

顶层`await`使模块能够充当大型异步函数。

有了顶级的`await`，ECMAScript 模块(ESM)可以`await`资源，导致其他模块`import`它们，在它们开始评估自己的身体之前等待。

```
// say this is my-apple.js
await Promise.resolve('🦥') *// '*🦥*'*
```

## 用例

什么时候让一个模块等待异步操作加载才有意义？

**动态依赖路径**

```
const strings = await import(`/i18n/${navigator.language}`);
```

这允许模块使用运行时值来确定依赖性。这对于开发/生产分割、国际化、环境分割等非常有用。

**资源初始化**

```
const connection = await dbConnector();
```

这允许模块表示资源，也允许在模块永远无法使用的情况下产生错误。

**依赖倒退**

```
let jQuery;
try {
  jQuery = await import('https://cdn-a.com/jQuery');
} catch {
  jQuery = await import('https://cdn-b.com/jQuery');
}
```

# 2.`.at()`对所有内置可索引的方法

目前，要从可索引对象的末尾访问一个值，通常的做法是编写`arr[arr.length - n]`。

这需要命名 indexable 两次，此外，为`.length`增加了七个字符，并且对匿名值有敌意。

此外，您不能使用这种技术来获取函数返回值的最后一项，除非您首先将它存储在一个临时变量中。让我们看看我们能用`.at()`做些什么:

```
const users = ['👷‍♀️', '🧟‍♂️', '👶', '🧝', '🙋‍♂️'];// Until today
users[users.length - 1]; // '🙋‍♂️'
users[1]; // '🧟‍♂️'
users[1000]; // *undefined*// With the .at() method;
users.at(-1); // '🙋‍♂️'
users.at(1); // '🧟‍♂️'
users.at(1000); // *undefined* ["🍍", "🍏"].at(0); // "🍍"
```

# 3.更易访问的`hasOwnProperty:` Object.hasOwn

如果你不直接拥有一个对象中定义的每个属性，你不能 100%确定调用`.hasOwnProperty()`就是调用内置方法。让我们看看这个例子:

```
let object = {
  hasOwnProperty() {
    throw new Error("gotcha!")
  }
}object.hasOwnProperty("foo")
// Uncaught Error: gotcha!
```

`Object.hasOwn`具有与`.hasOwnProperty()`完全相同的行为，并且实现方式如下:

```
let object = { foo: false }
Object.hasOwn(object, "foo") // truelet object2 = Object.create({ foo: true })
Object.hasOwn(object2, "foo") // falselet object3 = Object.create(null)
Object.hasOwn(object3, "foo") // false
```

## 很高兴知道

ESLint 有一个[内置规则](https://eslint.org/docs/rules/no-prototype-builtins)，禁止使用类似`hasOwnProperty`的原型内置。

# 4.正则表达式匹配索引

ECMAScript `RegExp`匹配索引提供了捕获的子字符串相对于输入字符串开头的起始和结束索引的附加信息。

让我们来看看它的实际应用:

## 很高兴知道

出于性能原因，只有指定了`d`标志，`indices`才会被添加到结果中。

# 5.错误原因

为了帮助进行意外行为诊断，需要用上下文信息(如错误消息和错误实例属性)来补充错误，以解释当时发生了什么。

如果错误是从深层的内部方法中抛出的，那么在没有适当的异常设计模式的情况下，抛出的错误可能不会直接有效地处理。

现在，`Error()`接受了一个额外的选项参数:`cause`属性。`cause`然后将作为一个属性分配给错误实例。这意味着可以链接错误，而无需在条件中包装错误的不必要和过于繁琐的手续:

# 6.针对私人领域的人体工程学品牌检查

这个新特性提出了一种检查对象是否有给定私有字段的简洁方法。

# 结论

今年到此为止！很好的完成了这篇文章的结尾！

作为一名开发人员，跟上语言的发展是你成长的关键！

这些特性中哪一个最让你兴奋？

感谢阅读！