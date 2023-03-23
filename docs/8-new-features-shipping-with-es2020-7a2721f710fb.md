# ES2020 附带 7 项新功能

> 原文：<https://betterprogramming.pub/8-new-features-shipping-with-es2020-7a2721f710fb>

## GlobalThis、可选链接、类中的私有字段、nullish 合并运算符等等

![](img/bd020a275f3ad9220923bd5c0f7fe913.png)

托马斯·弗兰科斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你可能知道，从 2015 年 ES6 开始，每年都会发布 ECMAScript 的新版本，由 Ecma International 的 TC39。ECMAScript 2020 是 ECMAScript 语言规范的第 11 版。

这个新的迭代有它自己的一批新特性——让我们来回顾一下！

# 1.' globalThis '

JavaScript 语言现在非常流行，可以在各种各样的环境中使用——当然是在 web 浏览器中，但是 JavaScript 也可以在服务器端、智能手机上、机器人上运行，等等。

每个环境都有自己的对象模型和语法来访问全局对象。正因为如此，编写能在多种环境下工作的 JavaScript 代码可能会很困难:

```
// browser environment
console.log(**window**);

// node.js environment
console.log(**global**);

// Service worker environment
console.log(**self**);// ...
```

当然，通过编写一个检查当前环境的函数来跨多个平台标准化它是可能的，但是这将不再需要了！

`globalThis`属性现在是跨环境以一致的方式访问全局对象的标准方式。

# 2.' Promise.allSettled()'

`Promise.allSettled()`方法返回一个承诺，该承诺在所有给定承诺都已解决或被拒绝后解决，并带有一个对象数组，每个对象通过 status 属性描述每个承诺的结果。这使得过滤它们变得更加容易。

```
const p1 = new Promise((res) => res("🍕"));
const p2 = new Promise((res, rej) => rej("🍍"));
const p3 = new Promise((res) => res("🍺"));**Promise.allSettled**([p1, p2, p3]).then(data => console.log(data));
// [
//   { status: "fulfilled", value: "🍕" },
//   { status: "rejected", value: "🍍" },
//   { status: "fulfilled", value: "🍺" },
// ]
```

# 3.零融合算子

在执行属性访问时，当试图提供默认值时，nullish 合并运算符是工具箱中的一个新工具。它不同于在两个值之间用`||`符号化的*或*运算符，它处理空值的方式不同。

让我们通过比较这两个操作符来看看它是如何工作的:

```
const test = {
  null: null,
  number: 0,
  string: '',
  boolean: false
};const undefinedValue = test.dog || "Cat"; // "Cat"
const undefinedValue = test.dog **??** "Cat"; // "Cat"const nullValue = test.null || "Default"; // "Default"
const nullValue2 = test.null **??** "Default"; // "Default"const numberValue = test.number || 1; // 1
const numberValue2 = test.number **??** 1; // **0**const stringValue = test.string || "Hello"; // "Hello"
const stringValue2 = test.string **??** "Hello"; // **""**const booleanValue = test.boolean || true; // true
const booleanValue2 = test.boolean **??** true; // **false**
```

如您所见，当且仅当`??`左侧的表达式为`null`或`undefined`时，nullish coalsceing 操作符才会选择右侧的值。

# 4.可选的链接运算符

到目前为止，当查找深嵌套在对象中的属性值或使用返回 object 或 null/undefined 的 API 时，人们通常必须这样检查中间值:

```
// Checking for intermediate nodes:
const deeplyNestedValue = obj && obj.prop1 && obj.prop1.prop2;// Checking if the node exists in the DOM:
const fooInputEl = document.querySelector('input[name=foo]');
const fooValue = fooInputEl && fooInputEl.value;
```

可选的链接操作符允许我们以更方便的方式处理这些情况。重写上面的例子将给出:

```
// Checking for intermediate nodes:
const deeplyNestedValue = obj**?**.prop1**?**.prop2;// Checking if the node exists in the DOM:
const fooValue = document.querySelector('input[name=foo]')**?**.value;
```

## **很高兴知道**

可选的链接操作符是 short-circuiting，这意味着如果`?.`的左侧计算结果为空或未定义，它将停止计算该行:

```
// x is incremented if and only if 'a' is not null or undefined
a?.[++x]
```

最后一点:可选的链接操作符也适用于函数。

```
func?.(...args) // optional function or method call
```

# 5.' BigInt '

我们已经有了用 JavaScript 表示数字的`Number`。问题是，人数最多的是 2⁵。超过那个，就真的不靠谱了。

```
const x = Number.MAX_SAFE_INTEGER; // 9007199254740991
const y = x + 1; // 9007199254740992 • equal to 2^53
const z = x + 2; // 9007199254740992 • well, it's broken
```

是的:`BigInt`是一个新的原语，提供了一种表示大于 2 ⁵的数的方法。它是通过在一个整数的末尾追加一个`n`来创建的:

```
const aBigInteger = 9007199254740993**n**;
// There is also a constructor:
const evenBigger = BigInt(9007199254740994); // 9007199254740994**n**
const fromString = BigInt("9007199254740995"); // 9007199254740995**n**
```

## **很高兴知道**

一个`BigInt`与一个`number`的行为基本相同，但是它们不能在一个操作中一起使用:

```
let sum = 1n + 2, multiplication = 1n * 2;
// TypeError: Cannot mix BigInt and other types, use explicit conversions
```

可以使用构造函数`Number()`将一个`BigInt`转换成一个`Number`，但是在某些情况下，您可能会失去精度。因此，建议只在代码中需要大值时使用`BigInt`。

```
Number(900719925474099267n); // 900719925474099300 • 🤷‍♂️
```

# 6.动态导入()'

导入模块`import module from './module'`的当前形式是静态的，只接受一个字符串。它也只在运行前作为一个链接过程工作。

动态的`import(...)`允许开发者能够在运行时动态加载 JavaScript 应用程序的一部分。这带来了当前`import`不支持的多个优势:

*   加载用户的语言，而不是全部加载
*   延迟加载应用程序的路由(提高性能)
*   如果找不到模块，可以处理失败

## 它是如何工作的？

```
// Used as a function, import() returns a promise that can be handled the 2 usuals ways:// Using callback **import(**'/module.js'**)**
  .then((module) => {
    // Do something with the module.
  });// Using async / await
**async** function () {
  let module = **await import(**'/modules.js'**)**;
}
```

与通常的`import`声明相比，以下是显著的区别:

*   `import()`可以在脚本中使用，而不仅仅是在模块中
*   `import()`可以发生在任何地方任何高度，并且不被吊起
*   `import()`接受任意字符串(运行时确定的模板字符串，如此处所示)，而不仅仅是静态字符串

# 7.' String.protype.matchAll '

`matchAll()`方法返回所有匹配正则表达式(`RegExp`)的结果的迭代器，工作方式如下:

```
const regexp = RegExp('[a-z]*ball','g');
const str = 'basketball handball pingpong football';
const matches = str.**matchAll**(regexp);// Since it is an iterator, you can loop trought results this way:
let match = matches.next();
while (!match.done) {
 console.log(match.value);
 match = matches.next();
}
// ["basketball", index: 0, input: "basketb...", groups: undefined]
// ["handball", index: 11, input: "basketb...", groups: undefined]
// ["football", index: 29, input: "basketb...", groups: undefined]// One can also get an array of all results
let results = [...str.matchAll(regexp)];
```

## **很高兴知道**

当使用全局`/g`标志时，`match()`方法不会返回捕获组，而`matchAll()`会。

# 结论

今年到此为止！

Babel 已经有了几乎所有这些特性的插件。如果您迫不及待地想在您的项目中使用它们，以下是链接:

*   [@ babel/plugin-proposal-nullish-coalescing-operator](https://babeljs.io/docs/en/babel-plugin-proposal-nullish-coalescing-operator)
*   [@ babel/plugin-proposal-private-methods](https://babeljs.io/docs/en/babel-plugin-proposal-private-methods)
*   [@ babel/plugin-proposal-optional-chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)
*   [@ babel/plugin-syntax-bigint](https://babeljs.io/docs/en/babel-plugin-syntax-bigint)
*   [@ babel/plugin-语法-动态-导入](https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import)

感谢阅读！