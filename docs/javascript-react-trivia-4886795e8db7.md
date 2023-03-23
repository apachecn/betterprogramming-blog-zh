# JavaScript/React 琐事

> 原文：<https://betterprogramming.pub/javascript-react-trivia-4886795e8db7>

## 你在这篇文章中独自玩的游戏

![](img/fbd42d7313ce25b8f6e39a29a6e56fe2.png)

Sebastian Davenport-Handley 在 Unsplash 上的照片

你有多精通 JavaScript？你想让你的编程生涯更上一层楼吗？

我整理了 10 个 JavaScript 琐事问题，看看你是否能在不使用浏览器控制台的情况下回答它们。希望你能学到一些东西。

# 1.下面的代码控制台输出什么？

```
var bar = null;console.log(typeof bar === "object");
```

a)错误

b)正确

c)空

d)抛出一个错误

# 2.下面的代码控制台输出什么？

```
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
```

a)真，假

b)假，真

c)真实，真实

d)假，假

# 3.**任何事物在发生反应时都是一个 _ _ _ _ _ _ _ _ _。**

a)模块

b)组件

c)包装

d)等级

# 4.在 React 的最新版本中，下列哪一项已被弃用？

a) `componentWillMount`

b) JavaScript

c) `componentDidMount`

d)脸书邀请

# 5.**巴别塔是什么？**

A)运输工具

b)编译器

c)翻译

d)编译程序和传输程序

# 6.什么是虚拟 DOM？

a.真实 DOM 的精确 HTML 副本。

b)本地浏览器功能。

c)元素和数据的 JavaScript 对象。

d)从`react.render`方法返回的元素和数据的 JSON 字符串。

# 7.关于渲染虚拟 DOM，下列哪个陈述是错误的？

a)虚拟 DOM 实现总是比 DOM 操作更快。

b)当发生任何变化时，整个 UI 都会在虚拟 DOM 中重新呈现。

c)重新绘制 DOM 是渲染中最慢的部分。

d)在重新渲染之后，真实的 DOM 仅用计算出的变化来更新。

# 8.对/错:useLayoutEffect 在 React 渲染组件后运行，并确保您的效果回调不会阻止绘画

# 9.以下哪一项不是 JavaScript 中的保留字？

a)违约

投掷

c)最后

d)未定义

# 10.这些函数将返回什么？

```
function foo1() {
  return {
    bar: 'hello'
  };
}function foo2() {
  return
  {
     bar: 'hello'
  };
}
```

一)`undefined`，`{ bar: “hello” }`

b) `{ bar: “hello” }`、`undefined`

c) `undefined`，`undefined`

d) `{ bar: “hello” }`，`{ bar: “hello” }`

# 答案

## 问题 1

```
b) A common pitfall is to forget that null actually is considered to be an object. Make sure that whenever you are checking type with typeof, you also check that the variable is not null.
```

## 问题 2

```
b) This is actually defining b in the global scope a and in the scope of the function. So, b = 3 and a = undefined. To avoid this, make sure you are using the strict JavaScript tag ( use strict ).
```

## 问题 3

```
b) Everything in React is a component. The basic building blocks of the web.
```

## 问题 4

```
a) Yes, it's been announced. We've got to stop using it.
```

## 问题 5

```
d) This one can be debated but on Babel's website, they call themselves a compiler. The real question here is if a transpiler is a compiler.
```

## 问题 6

```
c) The virtual DOM is just a JavaScript object with all the data needed to compare with the actual DOM.
```

## 问题 7

```
a) You actually can make it slower. Avoid using deeply nested objects and use memoization when needed.
```

## 问题 8

```
False. useEffect does this.
```

## 问题 9

```
d) undefined is actually not a reserved word and can be reassigned.
```

## 问题 10

```
b) The return needs to be followed by something, otherwise an implied semicolon is inserted.
```

我希望你喜欢那个测验。如果你不是什么都知道，那很好，因为这意味着你学到了一些东西。

下次见。

干杯！

准备好第二部分了吗？看看这里。