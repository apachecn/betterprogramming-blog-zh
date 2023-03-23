# 编写高效高性能 JavaScript 代码的 7 个技巧

> 原文：<https://betterprogramming.pub/7-tips-to-write-efficient-and-performant-javascript-code-bccbdb9662ae>

## 提升您的 JavaScript 应用程序的性能

![](img/bc899fcf0badf85636c8c1bded7af243.png)

肖恩·亨利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

专注于让事情运转起来而忘记性能和效率是一个常见的错误。这是初级和高级开发人员之间的一个关键区别。在编写 JavaScript 时，我们总是需要关注性能。我们希望我们的应用程序能够在任何可能访问它的设备上尽可能高效地运行。我们不能假设我们的网络应用总是在功能强大的设备上运行。

我们能做些什么来提高我们的 JavaScript 性能？今天，我们将了解一些提示，帮助您提升 web 应用程序的整体性能。它们将防止您的代码产生瓶颈，并确保向用户交付高质量的用户体验。

# 1.注意本地存储和会话存储

`LocalStorage`和`SessionStorage`是让我们在 web 应用程序中持久化数据的伟大特性。两者有什么区别？`SessionStorage`页面会话结束时，数据被清除。

> 这些机制可以通过`Window.sessionStorage`和`Window.localStorage`属性获得(更准确地说，在支持浏览器中，`Window`对象实现了`WindowLocalStorage`和`WindowSessionStorage`对象，而`localStorage`和`sessionStorage`属性挂起了这两个对象)，调用其中一个将创建一个`[Storage](https://developer.mozilla.org/en-US/docs/Web/API/Storage)`对象的实例，通过它可以设置、检索和删除数据项。不同的存储对象用于每个原点的`sessionStorage`和`localStorage`——它们分别运行和控制。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)

尽管这些 web APIs 很好也很有用，但是它们是同步的。那是什么意思？这意味着，如果你不小心，你可能会减慢你的应用程序。这些 API 将保存主线程。`LocalStorage`和`SessionStorage`只能存储字符串。这意味着存储大的或复杂的对象会损害性能，因为您需要使用`JSON.stringify`和`JSON.parse`操作。

让我们看一个虚拟的例子:

在这次测试中，我可以在 2017 款 MacBook Pro 上体验到 0.15-0.20ms 的执行时间。客观地说:这是一帧动画应该花费的时间。我们可以断定那是相当长的一段时间。

有哪些可能的解决方案？以下是一些想法:

*   仅存储所需的最少量数据。
*   为它创建你自己的抽象。您可以缓存对它的访问，从而更快地访问已经提取的信息。

*   思考存储数据的替代方法。例如，您可以使用 cookies 来代替。这取决于每一个特定的场景。

# 2.使用 Observer Web APIs

试图从浏览器持续监控事件会对我们的 web 应用程序产生显著的性能影响。这在听`scrolling`事件时尤其明显。

比方说，当一个元素在屏幕上变得可见时，您想要触发一些动作。大多数开发人员会连接到`scroll`事件，然后尝试判断该元素在屏幕上是否可见。这是非常低效的。我们怎样才能做到与众不同？

我们可以使用`IntersectionObserver`功能。它让我们反应性地观察元素。当这些元素在视口中变得可见或隐藏时，我们会收到事件通知。

这只是一个例子。让我们来看看我们本地支持的 observer Web APIs 的完整列表:

*   `IntersectionObserver`:报告广告的可见性，以便计算广告收入。
*   `MutationObserver`:监视 DOM 树变化的能力。
*   `ResizeObserver`:报告`Element`的内容或边框尺寸的变化，或者`SVGElement`的边框尺寸的变化。

浏览器对所有这些的支持都很好，而且对旧浏览器也有很好的填充。

# 3.拥抱早归模式

我们已经习惯了`if/else`模式，从来没有真正质疑过它。但是，通过经验，你认识到一个充满`if`和`else`的代码是:

*   无能的
*   难以阅读
*   难以维护

我们能做些什么来改善这种情况？我们可以拥抱*早归模式*。

“尽早返回”是一种模式，建议尽早返回结果，而不是使用`else`语句。

我们来做一个经典的`FizzBuzz`例子。下面的代码可能是一个解决方案:

如果我们应用提前返回模式，我们会得到下面的代码:

通过应用提前返回模式，我们的代码变成了:

*   更加做作
*   可读性更强
*   更有效率

# 4.主异步和延迟

如何在你的网站上加载脚本是至关重要的。它在关键的渲染路径中扮演着重要的角色。当用户导航到你的网站后，就要和时间赛跑，尽快提供内容。就此而言，理解如何以最高效的方式加载脚本至关重要。

你有三个选择:

*   `default`:获取并执行脚本，同时解析保持暂停，直到执行完成。
*   `async`:当`async`属性存在时，脚本将被并行获取并解析，一旦准备好就进行评估。脚本执行的顺序是不可预知的。
*   `defer`:当该属性存在时，它确实向浏览器表明该脚本应该在文档被解析之后，但在触发`DOMContentLoaded`之前执行。与`async`相反，这些脚本将按照它们在文档中出现的顺序执行。

理解这三种加载脚本的方式之间的区别变得非常重要。

根据经验，没有依赖关系的脚本应该用`async`属性执行，而其他的应该用`defer`属性标记。

# 5.推迟不重要的任务

复杂的 JavaScript 应用程序经常会运行大量代码。然而，可能有些任务不需要马上发生。对于那些低优先级的任务，我们能做的就是把它们推迟到浏览器不那么忙的时候。将你的浏览器从那些任务中解放出来将会导致更有效的执行。

如何做到这一点？通过使用浏览器的`requestIdleCallback`方法。

> “`***window.requestIdleCallback()***`方法将在浏览器空闲期间调用的函数排队。这使开发人员能够在主事件循环上执行后台和低优先级工作，而不会影响动画和输入响应等延迟关键事件。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestIdleCallback)

在很多情况下，您都可以应用这一策略。最常见的一种是发送分析信息。我们不介意数据是早是晚。这是一个非关键任务，因为它将由另一个服务异步处理。

让我们来看一个使用示例:

在本例中，我们将等待浏览器空闲，以执行我们的分析代码。如果在接下来的两秒钟内没有发生这种情况，它将被执行。`timeout`选项是可选的。它帮助您进一步定制代码的执行。如果该选项不存在，它将只在浏览器有时间时执行。

使用`[cancelIdleCallback()](https://developer.mozilla.org/en-US/docs/Web/API/Window/cancelIdleCallback)`方法可以取消回调。

# 6.优化您的动画

如果做得好，动画是一个非常酷的功能。它们向用户提供了事物的来源和发展方向。如果处理得当，它们的用处怎么强调都不为过。

然而，如果做错了，它们会对性能产生严重的负面影响。自定义的 JavaScript 动画可能会很慢，并损害您的用户体验。这在 React 本地应用程序上尤其明显，JavaScript 桥是一个很大的瓶颈。

为什么不依靠浏览器来帮我们完成繁重的工作呢？CSS 动画以更高性能的方式运行。由于新的网络动画 API，CSS 动画框架也可以通过 JavaScript 访问。

但是，如果您需要创建一些自定义动画，请避免使用`setTimeout`:

```
setTimeout(() => {
  triggerAnimation();
}, 16.6)
```

`setTimeout`回调不会每隔`16.6ms`运行一次。超时时间只是一个近似值。相反，你应该使用`requestAnimationFrame`。

> `***window.requestAnimationFrame()***`方法告诉浏览器你想要执行一个动画，并请求浏览器在下一次重画之前调用一个指定的函数来更新动画。该方法将回调作为在重画之前调用的参数。— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)

让我们重写上面的例子:

现在，我们的动画方法将在每个绘制周期之前运行。你现在的工作是专注于拥有一个高性能的动画代码。

# 7.留意一下记忆

JavaScript 不提供任何内存管理原语。相反，内存是由 JavaScript VM 通过内存回收过程来管理的。这个过程被称为*垃圾收集*。

什么是内存泄漏？它是软件无法回收的一块已分配的内存。

JavaScript 本身不会泄漏内存。相反，这是由开发人员无意的内存保留造成的。只要代码是整洁的，并且我们不忘记自己清理，就不会发生泄漏。

![](img/30c0a7c5b7d6c87f072bac1e186c6d2a.png)

内存泄漏代码的示例

内存泄漏会对我们的 web 应用程序的性能产生很大的影响。代码将耗尽用户的计算机资源。使用的内存越多，垃圾收集器运行的时间就越长，频率就越高。这降低了网页的响应性和交互性，损害了用户体验。

我们必须牢记的关键领域是什么？

*   事件监听器:确保所有计时器在不需要的时候已经从文档中清除。
*   计时器监听器:确保所有间隔和超时一旦不再需要就被取消。
*   观察者:确保任何观察者在不需要的时候都是断开的。
*   `Window`对象:确保我们没有增加我们的全局内存使用。

# 最后的想法

我们已经看到了很多可以提高 JavaScript 性能的地方。您需要选中所有这些框，以使您的代码具有高性能:

*   正确使用存储功能
*   良好的内存管理
*   依靠浏览器的 API 来管理元素的状态
*   明智地加载我们的脚本
*   尽可能在 CSS 上做动画
*   避免纠缠`if/else`的逻辑
*   将不重要的 JavaScript 代码推迟到不太忙的时候

我希望这些技巧有助于让您的应用程序更高效地运行。

# 相关文章

[](/10-javascript-array-methods-to-boost-your-code-performance-acb57b455189) [## 提高代码性能的 10 种 JavaScript 数组方法

### Every、some、includes 和其他数组方法

better 编程. pub](/10-javascript-array-methods-to-boost-your-code-performance-acb57b455189) [](/5-common-javascript-memory-mistakes-c8553972e4c2) [## 5 个常见的 JavaScript 内存错误

### 避免应用程序内存泄漏的技巧

better 编程. pub](/5-common-javascript-memory-mistakes-c8553972e4c2)