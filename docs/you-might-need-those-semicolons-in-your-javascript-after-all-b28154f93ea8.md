# 毕竟，您可能需要在 JavaScript 中使用这些分号

> 原文：<https://betterprogramming.pub/you-might-need-those-semicolons-in-your-javascript-after-all-b28154f93ea8>

## 把它们留在外面看起来很漂亮，但是可能会弄坏东西

![](img/7e7860f37ddfeacfee88d9ea056e5d39.png)

妮可·沃尔夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当我第一次开始写 JavaScript 时，我认为分号是强制性的。当时我正在学习 jQuery，我正在阅读的所有文档都在每条语句的末尾显示了一个分号。

我有一些其他编程语言的背景，这与我已经知道的一致——分号是用来帮助计算机区分指令的。有道理。

没过多久我就知道了 JavaScript 的**自动分号插入(ASI)** 。基本上，如果您省略分号，JavaScript 会自动为您添加分号。所以，它们毕竟不是强制性的。

这背后的历史很有意思。JavaScript 最初并不像今天这样是编程语言的重量级。一开始，它的目的是让非专业人士能够轻松掌握。ASI 是为了让外行人对这门语言更友好而加入的一个特性。至少，这是我的想法。

为了避免破坏互联网，JavaScript 需要向后兼容。我们可以添加新功能(ES6、ES7、ES8 等)，但我们不能取消功能，因为它们已经被使用和依赖。不管是有意还是无意，数百万行 JavaScript 需要 ASI 才能正常工作。所以，我们都被它困住了，但是我们中的一些人选择用不同的方式处理它。

一个阵营编写包含分号的代码。他们的代码看起来更像是需要分号的语言，比如 C 或 PHP。

另一个阵营没有使用分号，而是依赖 ASI 自动添加分号。他们的代码看起来更像 Python 或 Ruby。

JavaScript 社区在这个问题上仍然存在分歧。例如， [React](https://reactjs.org/docs/components-and-props.html) 在其文档中仍然包含分号，但是 [Vue](https://vuejs.org/v2/guide/computed.html) 没有。

在 jQuery 和 React 之后，Vue 是我在 JavaScript 框架中的下一站，我决定采用文档中的无分号编码风格。我很喜欢它(它让你的代码看起来干净多了！)并开始将这种风格应用于 Vue 之外的其他 JavaScript 工作，例如 Node.js。

不幸的是，我开始遇到问题…

# 依赖分号的自动插入是如何破坏事物的:一个具体的例子

我想为我正在从事的项目自动化端到端测试。我的想法是编写一个基本的机器人，它会进入我的网页，并把不同类型的输入放到一个表单中。如果机器人得到的输出是我所期望的，很好。如果没有，我可能有一个错误。

[木偶师](https://github.com/puppeteer/puppeteer) (Headless Chrome Node.js API)似乎是一个很好的方法，所以我试了一下。这些文档提供了以下代码片段来帮助您入门:

```
const puppeteer = require('puppeteer');(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await page.screenshot({path: 'example.png'}); await browser.close();
})();
```

本质上，这段代码的思想是导入 Puppeteer，然后实现一个`async`[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)(立即调用的函数表达式)。这允许您使用`await`关键字在开始下一步(例如加载网页)之前等待异步操作(例如启动浏览器)完成。

经过测试，一切正常。木偶师去网页，按预期截图。太棒了。

但是我为这个项目编写的其余代码都是以无分号风格完成的。因此，为了保持一致，我去掉了分号:

```
const puppeteer = require('puppeteer')(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://example.com')
  await page.screenshot({path: 'example.png'})await browser.close()
})()
```

不幸的是，这一更改导致我的代码抛出了一个错误，而当包含分号时它并没有抛出:

```
/Users/chris/Documents/code/sandbox/nodejs/puppeteer-test/index.js:3
(async () => {
^TypeError: require(...) is not a function
```

这令人困惑。代码运行得很好——然后就不好了。但是在深入调查之后，它开始变得有意义了。

Node.js 不知道它应该在`const puppeteer = require('puppeteer')`之后停止——因为它没有分号可循。相反，它看到下面出现了一个左括号，并将其解释为一个函数调用。它认为代码是这样的:

```
const puppeteer = require('puppeteer')(async () => {
  const browser = await puppeteer.launch()
  const page = await browser.newPage()
  await page.goto('https://example.com')
  await page.screenshot({path: 'example.png'})await browser.close()
})()
```

如果没有分号，这段代码是不明确的。

对于无分号阵营来说，这是一个大问题。IIFEs 是 JavaScript 中的一种常见模式，而不是一种模糊的边缘情况，我们的代码样式需要与它们一起工作。仅仅为了避免分号而避免省略号是非常愚蠢的。

这种情况并不经常出现——但有时会出现*和*, JavaScript 开发人员应该为此做好准备。

只有在绝对必要的时候才可以插入分号(例如在上面的 require 语句之后)，但是这样做很尴尬，原因有两个:

*   您可能并不总是事先知道哪里需要分号。事实上，当事情发生时，你可能根本不知道你正在处理一个 ASI 问题。默认情况下包含分号可能会为您节省大量调试时间。
*   只是在一些地方有分号而在另一些地方没有看起来是错误的——我的强迫症无法处理这种不一致。

另一个解决方案是根据您当前的环境遵守不同的规则。可以说是随波逐流。

例如，在编写普通的 JavaScript、jQuery 和 Node.js 时，可以包含分号，而在编写 React 和 Vue 时，可以不使用分号。这种不一致仍然困扰着我，但是我更喜欢这个解决方案，而不是上面提到的那个。

最后，我们可以简单地在任何地方，任何时候都使用分号。尽管我很喜欢没有分号的 JavaScript 的外观，但默认情况下包含分号还是很有意义的。

# 摘要

不管是好是坏，JavaScript 最初被设计成包括**自动分号插入(ASI)** ，我们被它困住了。

自从这种语言被发明以来，已经过去了将近 25 年，但是社区仍然没有就分号的最佳实践达成共识。一些开发者喜欢包含它们，一些开发者喜欢不包含它们。

无论您属于哪个阵营，重要的是要理解省略分号会不时地伤害您，并且您可能没有预料到这一点。例如，对于无分号用户来说，IIFEs 可能是一个有问题的模式。

最后，分号不完全是强制性的，但也不完全是可选的。