# 使用回调的异步 JavaScript 模式

> 原文：<https://betterprogramming.pub/async-js-patterns-using-callback-5c388ac45ac5>

## 看看回调设计模式

![](img/029ede3dd2db0a32024647c6c4218fd7.png)

Firmbee.com 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[拍照](https://unsplash.com/@firmbee?utm_source=medium&utm_medium=referral)

JavaScript 是当今世界使用最多的计算编程语言之一[ [*参考*](https://www.statista.com/statistics/793628/worldwide-developer-survey-most-used-languages/) ]，与 Java 或 C++相比，它经常因简单而受到批评。但毫无疑问，这恰恰是使它如此强大和全球传播的特征之一。

通过这一系列管理 JavaScript 并发性的文章，我们将讨论一些我们在进行 JavaScript 开发时可能都应该拥有的工具。这些系列的主要目的就是训练你在战场上使用这些武器。

需要强调的是，即使有新的解决并发问题的奇特方法，每种机制都有自己的优点/缺点和用例。

在第一篇文章*，*中，我们将讨论回调，这是一种很好的解决并发任务的初始方法。

JavaScript 引擎的行为与电力非常相似，它总是寻找阻力较小的路径，它会跳过任何看起来很慢的东西(`setTimeouts`、API 调用、渲染等)，并疯狂地执行最快的指令，除非我们使用机制来处理它(如回调、thunks、承诺、生成器函数、异步函数)。

# **回调**

这一切都来自于函数式编程，早在 1930 年，一些杰出的人就引入了现在所知的 lambda 演算——这个主题对于一篇独立的文章来说已经足够深入了。

让我们来看看下一个函数，它基本上需要两个参数并打印出它们的和，很简单，不是吗？

```
**function foo**(a, b) {
 console.info(a + b); // 3
}
**foo**(1,2);
```

但是发送一个函数的引用而不是一些变量呢？—会发生什么？

```
**function sum**(a, b){
 return a + b;
}// A function that orchestrates
**function foo**(callback, a , b){
 return callback(a,b) // this line will call our sum() 
} // we pass the reference of the function sum() and two numbers**const resp** = foo(sum, 1, 2)
console.info(resp); // 3```
```

哦，我们刚刚以一种非常棒的方式链接了函数，所以我们也可以添加一个新函数`subtract` ，这样只要改变对新函数的引用，我们就可以`sum`或`subtract`。

```
**function** sum(a, b){
 return a + b; 
}// New Subtract function
**function sub**(a, b){
 return a — b; 
}**function foo**(callback, a , b){
 return callback(a,b) // this line will call either sum() or sub()
}**const respSum** = foo(sum, 1, 2);
**const respSub** = foo(sub, 1, 2); // We can now subtract too!console.info(` Sum: ${respSum}` ); // 3
console.info(` Sub: ${respSub}`); // -1 
```

回调模式非常常用，但在考虑异步性时，它甚至更强大。

希望我们的应用程序需要与其他 API 通信来获取、保存、更新数据，这给游戏带来了一些新玩家；当试图执行尽可能多的指令时，我们需要处理 JavaScript 引擎产生的混乱，而不用担心
顺序/阻塞执行和人的合理性；亲爱的读者，这就是我所说的异步心碎。

你可以复制、粘贴并执行下一段代码，你会看到 JavaScript 引擎如何先打印出`Second Line`，再打印出`First Line`；与我们真正想要的顺序相反。

```
 **function fetchFoo**() {
 setTimeout(() => {
 console.info(‘First Line’)
 }, 2000)
 console.info(‘Second Line’)
}
**fetchFoo**() 
```

真正发生的是，当 JavaScript 引擎执行`**fetchFoo**()`时，它将首先尝试读取并解决第一行，但是当`**setTimeout**`等待 2 秒时，它将跳转到下一条指令并首先打印`Second Line`。

从理论上讲，解决这个难题的一种方法是手动传递异步任务，因为它们是同步的，这样 JavaScript 引擎就没有任何其他的代码分支需要优先处理，一旦完成，我们就传递新的任务，等等。让我们修改下一个代码。

```
// Chaining callbacks as a way of forcing sequential execution**function fooBar**() {
 cb1()
 function cb1() {setTimeout(**function** () {
 console.info(‘First Line’)
 cb2()
 }, 1000)**function cb2()** {
 console.info(‘Second Line’)
   }
 }
}**fooBar()** 
```

我们基本上是强制一个连续指令的链接，这样 JS 引擎甚至不需要考虑一个更容易的路径，除了等待 1 秒钟并打印出我们最初想要的`First Line`和`Second Line`之外，它没有更多的选择。

下一段代码是使用相同原理的更干净、更漂亮的方法。

```
**function fooBar**(cb1) {
 setTimeout(**function** () {
 // The function cb1() expects another callback — the cb2()
 cb1(function cb2 () {
 console.info(‘Second Line’)
 })
 }, 1000)
}// The function cb1() will eventually read and execute another callback that is not still defined.
// We will eventually define it with the next sequential instructions.**fooBar**(function cb1 (cb2) {
 console.info(‘First Line’)
 cb2()
})
```

我们基本上是在说:嘿，JavaScript 引擎，你知道吗？我一会儿会把另一个函数传给你们，我现在不能传给你们，因为你们会试图用我不想要的方式执行它。

所以不要担心，只管做你的事，我稍后打电话给你。当计时器到达 1 秒并且打印出`First Line`时，我们传递给它一个全新的代码(带有字符串`Second Line`)来执行，这基本上被称为延迟执行。

> 回调是一种延迟执行的机制。

就像生活中的几乎所有事情一样，有些事情需要仔细考虑。当使用回调时，我们需要同意控制反转，把程序的控制权交给别人。

Kyle Simpson 在一次关于回调的谈话中说，我们可能都必须使用这种机制调用外部 API，并生活在期待 API 不会失败的恐惧中，因为 API 没有办法通知我们，所以我们可以编写一个*紧急出口，*我们基本上失去了对程序的*控制*:

> “我的程序中有一部分是由我控制执行的。然后，我的代码中还有一部分我无法控制执行。”

还有另一个问题…信任，让我们想象一下，不知何故`setTimeout`最终开始失败，有时不是计时 1 秒，而是持续 100 秒！—这是一个真正的痛苦—我们的程序将比我们最初预期的时间长得多，或者更糟，想象它永远不会停止—回调缺乏某种机制，当出现问题时通知我们，以便我们可以编写一个紧急出口并保护我们的计算能力。

其中的一部分。Kyle Simpson 在他的一次演讲中说，回调还有另一个问题，它们不符合我们顺序阻塞的思维方式:

> 当代码不像我们的大脑那样工作时，错误就会出现。

我们解决并发问题的自然方式不同于回调工作的方式，我们可能需要另一种模式来解决异步任务，这种模式不会迫使我们失去对程序的控制(控制反转),也不会在涉及第三方 API 时缺乏信任，顺便说一下，这是一种更合理的方法。

回调的真正问题不仅仅是回调地狱。

在下一篇文章中，我们将讨论 Thunks，一种稍微不同的回调模式。

# **参考文献**

*   〖书〗你不知道 JS 异步&性能
*   MDN