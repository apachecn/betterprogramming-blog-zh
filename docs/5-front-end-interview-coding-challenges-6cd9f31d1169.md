# 5 个前端面试编码挑战

> 原文：<https://betterprogramming.pub/5-front-end-interview-coding-challenges-6cd9f31d1169>

## 求职面试中你可能会遇到的问题及其解决方法

![](img/4c1e431dbfcf7e966333b30c63a24da4.png)

[车头](https://unsplash.com/@headwayio?utm_source=medium&utm_medium=referral)在[档](https://unsplash.com?utm_source=medium&utm_medium=referral)拍照

去年，我面试了几家不同科技公司的软件工程师职位。因为大部分职位都是 web 开发，所以不出所料，我必须回答很多与客户端开发相关的问题。有些是简单的问题，如 [*什么是活动委托？*](https://github.com/yangshun/front-end-interview-handbook/blob/master/questions/javascript-questions.md#explain-event-delegation) 和 [*继承是如何在 Javascript 中实现的？*](https://github.com/yangshun/front-end-interview-handbook/blob/master/questions/javascript-questions.md#explain-how-prototypal-inheritance-works) ，还有一些更具挑战性的动手编程问题，我将在本文中分享我最喜欢的五个。

毫无疑问，面试成功的关键是做好准备。所以，不管你是在积极地面试，还是只是好奇在科技公司的前端面试中你可能会被问到什么样的问题，我希望这篇文章能帮助你为即将到来的面试做好准备。

# 目录

```
· [1\. Emulate Vue.js](#8e35)
· [2\. async series and parallel](#b65c)
· [3\. Draggable button that changes the background color](#742b)
· [4\. slide-out animation](#08e0)
· [5\. Giphy client](#ceba)
```

## 1.模拟 Vue.js

这个挑战是在一次电话采访中提出的。我被要求前往 [Vue.js 文档](https://vuejs.org/v2/guide/#Declarative-Rendering)，并将以下片段复制到我选择的编辑器中:

正如你所想象的，我们的目标是让`{{ message }}`被`Hello Vue!`取代，当然，不需要添加 Vue.js 作为依赖项。

*在你开始写代码之前，一定要向面试官澄清你对这个问题的任何疑问，确保你完全理解什么是输入，什么是输出，以及你需要考虑的任何边缘情况。*

首先，让我们创建我们的`Vue`类，并将其添加到 Javascript 代码片段之上。

```
class Vue {
    constructor(options) {
    }
}
```

有了它，我们的小项目至少应该运行无误。

现在，为了用提供的文本替换模板字符串，最简单的方法可能是，一旦我们访问了`#app`元素，就对其`innerHTML`属性使用`[String.replace()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)`:

这就完成了工作，但我们肯定可以做得更好。例如，如果我们有两个同名的模板字符串，这个实现就不会像预期的那样工作。只会替换第一次出现的内容。

```
<div id="app">
  {{ message }} and {{ message }}, what's the {{ message }}
</div>
```

这很容易解决——我们使用带有全局标志`*new RegExp(`{{ ${key} }}`, “g”)*` 的 [RegExp](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 来代替``{{ ${key} }}``。

另外，`innerHTML`很昂贵，因为值被解析为 HTML。我们应该用`textContent`或者`innerText`。要了解这三者之间的更多区别，请点击[此处](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent#Differences_from_innerText)。

简单地用`innerText`或`textContent`替换`innerHTML`对我们的简单标记有效，但是一旦我们的标记变得更复杂，它很快就会失效:

```
<div id="app">
  {{ message }}
  <p> another {{ message }} inside a paragraph </p>
</div>
```

您会注意到`<p>`标签将从 DOM 中移除。这是因为`innerText`和`textContent`只返回文本，当它被用作 setter 时，它只用文本替换标记。

处理这个问题的一个方法是[遍历](https://en.wikipedia.org/wiki/Tree_traversal)DOM，找到所有的文本节点，然后替换文本。

还有一件事我们应该改进。每当我们找到一个文本节点，我们就寻找模板字符串 *n* 次( *n，在本例中，*是数据条目的数量)。因此，如果我们有 200 个条目，即使我们的 DOM 节点看起来像这样:

```
<p>Nothing to see here</p>
```

我们仍然会迭代 200 次，试图找到模板字符串。

解决这个问题的一种方法是实现一个简单的状态机，它只查看文本一次，并在它运行时替换模板字符串(如果有的话):

这离生产就绪还差得很远，但你应该能在 30-45 分钟内完成。

一定要谈论你如何能进一步改进它，性能问题(好的 segway 来炫耀你的[虚拟 DOM](https://reactjs.org/docs/faq-internals.html) 知识)，如果你能谈论你将如何实现[循环和条件](https://vuejs.org/v2/guide/#Conditionals-and-Loops)和[处理用户输入](https://vuejs.org/v2/guide/#Handling-User-Input)的加分。

你可以看到上面的代码在下面的沙箱中运行:

## 2.异步串联和并联

在 [RxJs](https://rxjs-dev.firebaseapp.com/) 之前， [Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 和 [async/await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) 是行业标准，编写异步 Javascript 并不容易，你经常会成为[回调地狱](http://callbackhell.com/)(又名末日金字塔)的受害者。正因为如此，像 [async](http://caolan.github.io/async/v3/) 这样的库在那时被创建。

以下两部分挑战发生在一次现场面试中。我被要求带上自己的笔记本电脑，所以我知道会有一个现场编码会议。

[**async . series**](http://caolan.github.io/async/v3/docs.html#series)*连续运行* `*tasks*` *集合中的函数，每运行一次前一个函数就完成一次。如果系列中的任何函数向其回调函数传递错误，则不再运行任何函数，并立即使用错误值调用* `*callback*` *。否则，当* `*tasks*` *完成时，* `*callback*` *接收一个结果数组。*

让我们从创建异步对象开始:

```
const async = {
    series: (tasks, callback) => {}
};
```

这个挑战的主要问题是，我们需要确保一个功能接一个功能地执行。换句话说，我们只在前一个函数完成后执行它:

我们使用一个变量`i`来跟踪当前正在执行的函数，我们创建一个内部回调来检查错误，递增`i`并执行下一个函数。

为了简单起见，我们不验证输入或者使用 try/catch 来更好地处理错误，但是你应该总是和你的面试官讨论这个问题。

[**async . parallel**](http://caolan.github.io/async/v3/docs.html#parallel)*并行运行* `*tasks*` *函数集合，无需等待前一个函数完成。如果任何一个函数向它的回调函数传递了一个错误，那么主函数* `*callback*` *就会立即被调用，并返回错误的值。一旦* `*tasks*` *都完成了，结果就作为一个数组传递给最后的* `*callback*` *。*

让我们从向异步对象添加一个新的并行函数开始:

```
const async = {
    series: (tasks, callback) => {}
    parallel: (tasks, callback) => {}
};
```

并联不同于串联，因为我们可以同时启动所有的功能。我们只需要在收集结果的时候小心一点，这样它们就被放到了数组的正确位置。

我们从一个`done`标志开始，它防止在出错后调用回调，还有一个`count`标志，它跟踪已经完成了多少个函数，这样我们就知道什么时候停止。我们有一个内部回调，负责收集结果并调用用户的回调。最后，我们一次触发所有的功能。

最终代码可从以下网址获得:

## 3.改变背景颜色的可拖动按钮

在一次现场面试中，我被要求在屏幕中间实现一个可以拖动的按钮。当它向边缘移动时，背景颜色从白色变为红色。

在我们谈论一个可能的解决方案之前，请看这里的结果和代码。

让我们从创建标记开始:

```
<html>
  <body>
    <div id="overlay"></div>
    <div id="button" draggable="true"></div>
  </body>
<html>
```

`#overlay`将覆盖整个屏幕，它是我们用来改变背景颜色的元素。`#button`是我们的可拖动按钮。

这是 CSS，用来设计按钮和覆盖的样式:

```
#button {
    cursor: pointer;
    background-color: black;
    width: 50px;
    height: 50px;
    border-radius: 50px; position: absolute;
    top: 50%;
    left: 50%;
    transform: translateX(-50%) translateY(-50%);
}#overlay {
    background-color: red;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    opacity: 0;
}
```

我们改变颜色的方法是通过叠加不透明度。默认情况下是`0`(透明的)，我们会用 javascript 做相应的修改。

在这次挑战中，我被允许使用任何我想要的库。我知道公司用的是 [Typescript](https://www.typescriptlang.org/) 和 [RxJS](https://rxjs-dev.firebaseapp.com/) ，所以我决定用。我们需要做两件主要的事情——订阅和处理拖动事件，并根据事件的 X 和 Y 坐标确定覆盖不透明度。

我们将通过使用`[fromEvent](https://rxjs-dev.firebaseapp.com/api/index/function/fromEvent)`和`[subscribe](https://rxjs-dev.firebaseapp.com/api/index/class/Observable#subscribe)`来处理前者。这完全可以用普通的 Javascript 来完成(见`[addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)`)。

我们`filter`排除所有目标不是`#button`的拖动事件，并且用`distinctUntilChanged`抑制任何重复的事件。

为了解决后者，我们需要做一些数学计算。

`event.clientY`和`event.clientX`代表屏幕上可拖动按钮的位置。基于这些，我们需要计算一个介于 0 和 1 之间的数字作为覆盖的不透明度。

我们将`x`和`y`的最大值分别设置为`window.innerHeight`和`window.innerWidth`除以 2。我们将`x`和`y`归一化为介于 0 和它们的最大值之间的值。最后，我们计算`pY`和`pX`(这将是 0 和 1 之间的值)，并用较高的值设置不透明度。

## 4.滑出动画

以我的经验，关于元素如何动画化的问题很常见。在这个例子中，每当单击一个元素时，我被要求在不使用 CSS 动画和过渡的情况下实现滑出动画。

让我们从 HTML 开始:

```
<html>
  <body>
    <div id="box"></div>
</body>
<html>
```

和 CSS:

```
#box {
    width: 50px;
    height: 50px;
    background-color: blue;
}
```

用 Javascript 实现动画的方法不止一种。我推荐使用[window . request animation frame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame):

我们添加了一个点击事件监听器，这样每次点击`#box`时，就会用元素和动画的持续时间调用`slideOut`。

`slideOut`函数定义了`translateX`变换的`initial`和`target`值。创建一个`loop`并使用`requestAnimationFrame`调用它。循环将一直执行，直到`#box`到达屏幕末端。每一个新的`value`都是用线性方程计算出来的。

一个常见的后续问题是:你如何实现一个[缓和函数](https://easings.net/en#)？

幸运的是，我们已经有了[所有的参数](http://blog.moagrius.com/actionscript/jsas-understanding-easing/)我们需要用 [Penner 方程中的一个](http://robertpenner.com/easing/penner_chapter7_tweening.pdf)来交换我们的线性方程。让我们看看 easeInQuad:

```
easeInQuad = function (t, b, c, d) { return c*(t/=d)*t + b; };
```

我们将第 9 行改为:

```
const value = target * (time / duration) * (time / duration) + initial;
```

你可以在这里看到结果:

如果你对 Javascript 动画感兴趣，我写了一篇关于它的文章:

[](https://medium.com/better-programming/creating-a-proximity-graph-animation-an-introduction-to-html5-canvas-and-the-animation-loop-45719d82d1a3) [## 创建邻近图动画

### 2D HTML5 画布和动画循环介绍

medium.com](https://medium.com/better-programming/creating-a-proximity-graph-animation-an-introduction-to-html5-canvas-and-the-animation-loop-45719d82d1a3) 

## 5.Giphy 客户端

对于我们要看的最后一个挑战，我的任务是实现一个小的 web 应用程序，允许用户从 [Giphy API](https://developers.giphy.com/docs/api#quick-start-guide) 中搜索和浏览 gif。

我被给予了完全的自由来选择我觉得最舒服的框架和库。对于本文，我将使用 [React](https://reactjs.org/) 和`[fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)`。

让我们从创建一个简单的 React 组件开始，该组件带有一个处理用户输入的表单:

如果时间允许，你应该考虑创建子组件来保持你的代码有条理。通常，在面试中，时间对你不利。所以，即使你没有时间去做，也要让面试官知道你对改进代码有什么想法。

现在，为了使用 Giphy API，我们需要生成一个 [API 密钥](http://y1ZFwiomdYKWy80gtSxU4iEdv165yeOD)。一旦我们有了它，我们可以添加一个函数到我们的组件中，从[搜索端点](https://developers.giphy.com/docs/api/endpoint#search)获取数据。

为了简单起见，对于任何 API 异常都没有错误处理。

现在我们需要让`<form>`在用户点击**搜索**或点击**回车**时调用`search`方法。

```
<form
  onSubmit={e => {
    e.preventDefault(); // prevents the page from reloading
    search();
  }}
>
```

最后，我们扩充我们的组件来呈现来自搜索结果的 gif:

使用一些基本的 CSS，结果如下:

感谢阅读——我希望你今天学到了一些新东西。

保重，下次再见。