# JavaScript 中不需要 void 0

> 原文：<https://betterprogramming.pub/you-dont-need-void-0-f9d4cc1d0e1f>

## 这里有一个更好的选择

![](img/6e094c0e735c4ff228875fd14c00221b.png)

在 JavaScript 代码中，尤其是在旧的遗留代码中，有时会发现表达式`void 0`。

[void 运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void)计算一个表达式并返回原始值[未定义的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)。`void 0`评估`0`，它什么也不做，然后返回`undefined`。它实际上是`undefined`的别名。

# 为什么 void 0 被用作 undefined 的别名？

JavaScript 的一个怪癖是`undefined`不是保留关键字。
相反，它是[全局对象](https://developer.mozilla.org/en-US/docs/Glossary/Global_object)的一个属性。

在 ES5 (2009)之前，可以修改`undefined`全局属性，这可能会导致`undefined`的意外行为。

考虑下面的例子(在现代 JavaScript 引擎中不起作用):

```
// Pre-ES5 example - does not work in modern JS engines:// changes property 'undefined' on the global object:
undefined = "something else"; // potentially in some other JavaScript file or script section:
if (aVariable === undefined) {
  // aVariable is equal to "something else", 
  // but not to the primitive value 'undefined'
  doSomething();
}
```

修改`undefined`全局甚至可以发生在第三方代码中，例如通过脚本标签导入的库。由于`void 0`总是返回实际的原始值`undefined`，它通常在 ES5 之前使用，以使代码对`undefined`全局属性的重新定义具有故障保护。

# ES5 之后的全局属性“未定义”

在全局对象上修改`undefined`的问题太大了，以至于 JavaScript 标准被改变了。

在 ES5 中，全局属性`undefined`变成了只读的。

在现代 JavaScript 中，试图更改该值没有任何作用:

```
globalThis.undefined = "something else";
console.log(undefined); // prints undefined in modern browsers
```

# Undefined 仍然可以被局部变量隐藏

然而，虽然不再可能更改`undefined`全局属性，但是`undefined`仍然不是 JavaScript 中的保留关键字。因此，它仍然可以被局部变量隐藏:

```
const undefined = "something else";
let check = aVariable === void 0; // void 0 is needed here
```

使用名为`undefined`的局部变量是您想要避免的一个缺陷。ESLint 规则 no-undefined 不允许使用 undefined 作为变量名，并防止隐藏问题。

# void 0 有助于减少捆绑包大小吗？

表达式`void 0`比`undefined`短。JavaScript 包的大小对于创建快速加载的网站至关重要，去掉几个字节会有所帮助。

然而，最好是**将基本的代码大小优化留给 minifiers** ，例如 [Terser](https://github.com/terser/terser) 作为产品捆绑过程的一部分。它们可以执行许多不同的优化，并且源代码保持更好的可读性，而不需要任何手动的代码大小优化，比如使用`void 0`而不是`undefined`。

# 避免现代 JavaScript 中的 void 0

总而言之，在现代浏览器和 JavaScript 引擎中，没有理由再使用`void 0`:

*   在 ES5 和更新的环境中，全局属性`undefined`不能改变
*   使用 [ESLint 规则 no-undefined](https://eslint.org/docs/rules/no-undefined) 可以禁止名为`undefined`的局部变量
*   当创建生产包时，迷你编辑器可以用`void 0`替换`undefined`

相反，`void 0`使得 JavaSCript 代码更难阅读和理解，因为你需要知道`void 0`的含义，并为同一个概念(`undefined`)处理不同的术语(`void 0`、`undefined`)。

TLDR:你可以使用`undefined`并移除不必要的`void 0`并发症。