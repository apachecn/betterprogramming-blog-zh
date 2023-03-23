# 减小 JavaScript 包的大小

> 原文：<https://betterprogramming.pub/reducing-js-bundle-size-a6533c183296>

## 第二部分:精简和死码消除

![](img/c87506f8d018b02ec282182978a71349.png)

随着 2011 年左右像 [AngularJS](https://en.wikipedia.org/wiki/AngularJS) 这样的框架的出现和[单页面应用](https://en.wikipedia.org/wiki/Single-page_application)的普及，JavaScript 的使用在前端已经爆炸式增长。仅 2010 年一年，网络应用的平均规模[就超过三倍。由于 JavaScript 库和框架广泛用于开发这些 web 应用程序，缩小 JavaScript 包的能力变得前所未有的重要。](https://www.keycdn.com/support/the-growth-of-web-page-size)

在本系列的第二部分中，我们将深入探讨缩减和死代码消除。我们将重点介绍的主题有:

*   缩小和 DCE 的历史
*   什么是缩小
*   什么是死代码消除
*   我们如何使用工具在我们的代码上运行这些过程呢？
*   让它自动化！

如果您错过了[第一部分](https://medium.com/better-programming/reducing-js-bundle-size-58dc39c10f9c)，请随意先阅读一下关于捆绑的背景知识。

我们开始吧！

# 缩小，一段历史

首先，让我们回顾一下历史。

![](img/2849fa6d11326d8c12b1695871b04bab.png)

古老的遗址

网站大小和请求加载时间多年来一直是一个问题，只是加载时间慢的罪魁祸首已经转移了。静态网站通常更关心 HTML 的数量，最重要的是，图像的大小，而不是它们收缩包的能力，因为 JavaScript 在当时的使用更多的是补充性的！

随着 JavaScript 的使用随着像 [Moo-Tools](https://mootools.net/) 和 [JQuery](https://jquery.com/) 这样的工具的增加，缩小工具被开发来减少 CSS 和 JavaScript 的占用空间，从而减少初始请求加载时间。此外，我们将把 JQuery 这样的大型常用 JavaScript 工具转移到 CDN 上，以实现更快的内容交付。生产站点会指向这些库的缩小版本。

在我们更进一步之前，什么是代码精简？我们在[第一部分](https://dennyscott.io/reduce-js-bundle-part-one)中提到过，但是当我们缩小和丑化我们的代码时，我们会删除所有不需要的数据，比如创建一个更小的函数和变量名，删除换行符、注释、分隔符和字符间的空格，合并文件，可能还会优化调用。在我们下面的第一个例子中，我们将在任何死代码消除之前使用缩小，所以你将能够自己看到！

# 使用 Terser

由于 JavaScript 是一种动态脚本语言，它可能缺少静态编程语言中常见的许多工具。其中一个工具是使用静态分析来消除[死代码](https://en.wikipedia.org/wiki/Dead_code_elimination)。死代码消除通常是指删除我们代码库中不用的代码。为了更好地理解它，让我们看一个例子，并尝试消除死代码。

*注:本文使用的所有代码示例都可以在* [*Github*](https://github.com/DennyScott/minification-dce-blog) 上找到

让我们假设`selectClothes`在我们的应用程序中从不同的模块执行。在我们的例子中，不可能得到`Hat`对象作为我们的返回值。也许它曾被包含在内，也许这是一次重构，无意中留下了这段代码，或者有人认为用户不能购买帽子。剩下的就是所谓的“死代码”尽管这些代码很少，但是这些零碎的部分肯定会累积起来，并且它们会增加我们的包的大小。正如我们所知，增加的包大小会导致更长的加载时间，并需要更多的网络数据。

许多精简工具，如 [Terser](https://github.com/terser-js/terser) ，已经包含了死代码消除。它会发现任何从未执行过的代码，并自动将其从代码库中删除。让我们尝试通过 Terser 传递我们的代码示例，以便更好地理解库本身。

*注意:Terser 将自己描述为“用于 ES6+的 JavaScript 解析器和处理器/压缩器工具包”。*

首先，为此项目创建一个文件夹:

```
mkdir dce-example
cd dce-example
```

将上面的`selectClothes`代码添加到一个`dce-example/index.js`中。

最后，让我们创建一个`npm`包:

```
npm init
```

初始化之后，我们可以添加 Terser:

```
npm i terser — save-dev
```

现在，让我们用 Terser 运行第一遍:

```
npx terser index.js
```

输出应该如下所示:

```
function selectClothes(type){if(type===”shirt”){return{type:”shirt”,amount:”$10"}}else{return{type:”pants”,amount:”$12"}}return{type:”hat”,amount:”$5"}}selectClothes(“shirt”);
```

我们的代码被缩减了，但是死代码并没有从包中删除。怎么回事？我们一会儿就会谈到这一点，但是让我们先检查一下简化的代码。注意到所有的空白都被删除了吗？删除该空间会减小文件的大小。但是变量名/函数名呢，它们仍然是普通的名字，我们不是说过它们应该被缩小到尽可能小的尺寸吗？

Terser-CLI 知道我们可能会使用它并在我们的终端中查看它。当变量和函数被“破坏”时，阅读和理解我们的代码是非常困难的，所以默认情况下就不要去管它了。大多数 web 应用程序使用一个实际的模块捆绑系统来自动完成这项工作，因此它们永远也不需要阅读这些混乱的代码。不过，为了便于学习，我们应该先看一下它的样子，所以这次让我们用 Terser 再抛出几个命令:

```
npx terser — toplevel — mangle — mangle-props — index.js
```

这将为我们提供混乱的代码:

```
function t(t){if(t===”shirt”){return{type:”shirt”,t:”$10"}}else{return{type:”pants”,t:”$12"}}return{type:”hat”,t:”$5"}}t(“shirt”);
```

# 死代码消除

好吧，让我们回到我们之前的问题。为什么我们的死代码没有被淘汰？Terser 的默认选项不包括压缩，这是消除死代码的地方。所以让我们通过给它`-c`命令来添加它。

```
npx terser index.js -c
```

我们的结果应该是这样的:

```
function selectClothes(type){return”shirt”===type?{type:”shirt”,amount:”$10"}:{type:”pants”,amount:”$12"}}selectClothes(“shirt”);
```

看它变小了多少！它不仅删除了死代码(注意`hat`完全消失了)，还通过将`if`流改为三元流优化了我们的模块！

# 使用 Webpack 实现自动化！

在将我们的应用程序发布到网络上之前，我们曾经不得不通过像 triangular 这样的工具来传递我们的代码。但从那以后，像 [Rollup](https://github.com/rollup/rollup) 和 [Webpack](https://webpack.js.org/) 这样的捆绑工具接管了前端社区。这些捆绑工具通常很容易用像 Terser 这样的插件来扩展。在我们的例子中，我们将使用 Webpack 并添加 Terser 插件。

*注意:还有其他 web 应用捆绑包，所以不要认为这是一个令人疲惫的列表。这些只是现在用的主要的。* [*包裹*](https://parceljs.org/) *比较新，但是很刺激！*

因此，让我们将`Webpack`和`Webpack-CLI`添加到我们的项目中:

```
npm i webpack webpack-cli — save-dev
```

我们将在这里添加和移动几个文件。首先，我们将添加一个名为`dist`和`src`的文件夹:

```
mkdir dist
mkdir src
```

在`dist`内部，我们将添加`index.html`文件:

```
 <!doctype html>
 <html>
   <head>
    <title>Getting Started</title>
  </head>
  <body>
    <script src=”main.js”></script>
   </body>
 </html>
```

然后，我们将导航到项目根目录下的`/src`文件夹，并将`index.js`移动到其中:

```
mv index.js src/
```

最后，我们需要进入我们的`package.json`，删除`main`属性，改为添加`private: true`:

我们的项目已经设置好了，所以我们将返回到项目的根目录，并运行`webpack-CLI`工具。

```
npx webpack
```

之后，我们可以打开`/dist/main.js`来查看我们新捆绑的 JavaScript 文件。请记住，这是在我们的应用程序中添加一个捆绑系统，所以会有相当多的样板文件附加到文件中。对于只有一个 JavaScript 文件来说，这是一个很大的过度。但是随着我们的 web 应用变得越来越丰富，这种样板文件变成了微不足道的代码量。

让我们来看看这个包！

```
!function(e){var t={};function r(n){if(t[n])return t[n].exports;var o=t[n]={i:n,l:!1,exports:{}};return e[n].call(o.exports,o,o.exports,r),o.l=!0,o.exports}r.m=e,r.c=t,r.d=function(e,t,n){r.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:n})},r.r=function(e){“undefined”!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:”Module”}),Object.defineProperty(e,”__esModule”,{value:!0})},r.t=function(e,t){if(1&t&&(e=r(e)),8&t)return e;if(4&t&&”object”==typeof e&&e&&e.__esModule)return e;var n=Object.create(null);if(r.r(n),Object.defineProperty(n,”default”,{enumerable:!0,value:e}),2&t&&”string”!=typeof e)for(var o in e)r.d(n,o,function(t){return e[t]}.bind(null,o));return n},r.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return r.d(t,”a”,t),t},r.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},r.p=””,r(r.s=0)}([function(e,t){}]);
```

你可能有几个问题。首先，我们已经讨论过添加 Terser 插件，但是这个插件已经被缩小和压缩了。嗯，那是因为 Webpack 自动添加了用于缩小的 Terser 插件。此外，当我们运行 Webpack 时，您可能已经注意到了控制台中的警告。因为我们没有指定环境，所以它假定是生产环境。当环境设置为生产时，它将为我们执行缩小和压缩。

也许一个更关键的问题是，我们写的代码在哪里？嗯，最新版本的 Webpack 用`[esmodules](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)`包装你的文件，所以它可以对你的模块做更多的静态分析。它决定了我们写的代码对我们的代码库没有影响。该函数从未被外部调用(第三部分将详细介绍)，也从未执行过任何非纯路径。因此，整个文件都是死代码。这是正确的！我们从未在实际的前端中使用该代码。解决这个问题最简单的方法是添加一个控制台日志。所以让我们在`index.js`中这样做。

现在，当我们再次运行 webpack CLI 时:

```
!function(e){var t={};function n(r){if(t[r])return t[r].exports;var o=t[r]={i:r,l:!1,exports:{}};return e[r].call(o.exports,o,o.exports,n),o.l=!0,o.exports}n.m=e,n.c=t,n.d=function(e,t,r){n.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:r})},n.r=function(e){“undefined”!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:”Module”}),Object.defineProperty(e,”__esModule”,{value:!0})},n.t=function(e,t){if(1&t&&(e=n(e)),8&t)return e;if(4&t&&”object”==typeof e&&e&&e.__esModule)return e;var r=Object.create(null);if(n.r(r),Object.defineProperty(r,”default”,{enumerable:!0,value:e}),2&t&&”string”!=typeof e)for(var o in e)n.d(r,o,function(t){return e[t]}.bind(null,o));return r},n.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return n.d(t,”a”,t),t},n.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},n.p=””,n(n.s=0)}([function(e,t){const n=”shirt”===”shirt”?{type:”shirt”,amount:”$10"}:{type:”pants”,amount:”$12"};console.log(n)}]);
```

我们的代码现在存在于主包中。还要注意，就像以前一样，我们想要的死代码被删除了！让我们试着再抽象一点。在我们的`index.js`中，让我们将代码更新为:

现在的结果是:

```
!function(e){var t={};function n(r){if(t[r])return t[r].exports;var o=t[r]={i:r,l:!1,exports:{}};return e[r].call(o.exports,o,o.exports,n),o.l=!0,o.exports}n.m=e,n.c=t,n.d=function(e,t,r){n.o(e,t)||Object.defineProperty(e,t,{enumerable:!0,get:r})},n.r=function(e){“undefined”!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:”Module”}),Object.defineProperty(e,”__esModule”,{value:!0})},n.t=function(e,t){if(1&t&&(e=n(e)),8&t)return e;if(4&t&&”object”==typeof e&&e&&e.__esModule)return e;var r=Object.create(null);if(n.r(r),Object.defineProperty(r,”default”,{enumerable:!0,value:e}),2&t&&”string”!=typeof e)for(var o in e)n.d(r,o,function(t){return e[t]}.bind(null,o));return r},n.n=function(e){var t=e&&e.__esModule?function(){return e.default}:function(){return e};return n.d(t,”a”,t),t},n.o=function(e,t){return Object.prototype.hasOwnProperty.call(e,t)},n.p=””,n(n.s=0)}([function(e,t){const n=()=>({type:”pants”,amount:”$12"});const r=”shirt”===”shirt”?(()=>({type:”shirt”,amount:”$10"}))():n();console.log(r)}]);
```

注意，不仅对`buyHat`的调用被移除，实际的函数也被移除。Webpack 注意到该请求没有在任何地方使用，并删除了不可访问的代码。如果您只通过 Terser 运行同一个模块，而不使用 Webpack，您将得到以下输出:

```
const buyShirt=()=>({type:”shirt”,amount:”$10"}),buyPants=()=>({type:”pants”,amount:”$12"}),buyHat=()=>({type:”hat”,amount:”$5"});function selectClothes(type){return”shirt”===type?buyShirt():buyPants()}const clothes=selectClothes(“shirt”);console.log(clothes);
```

所以 Webpack 去掉了不用的功能，而 Terser 却做不到这一点，那是为什么呢？

这是因为 Webpack 可以假设我们正在使用`esmodule`系统。从 Webpack 4 开始，这是使用的默认模块系统。由于`buyHat`函数不是`exported`(稍后将详细介绍)，Webpack 知道在代码中唯一使用它的地方是我们的`selectClothes`函数。一旦 Terser 从那里删除了死代码，Webpack 就知道它在代码库中的任何地方都不再被使用，并且可以安全地删除。这是[摇树](https://webpack.js.org/guides/tree-shaking/)的早期演示。

[*在这里阅读我的后续文章*](https://medium.com/better-programming/introduction-to-tree-shaking-e94e57db081e)

# 结束语

干得好！我们完成了消除死代码的第一步。如果这是你第一次遇到像死代码消除这样的东西，并且你在理解它的时候有一点困难，我会在下面给你一些链接。不要担心第一次会有一点棘手，学习 JavaScript 代码本身是如何被分析和编译的会让人望而生畏。我们通常认为我们的系统更元。

另一方面，一旦你有了感觉，这实际上是一个非常简单的过程！这是一个关键的过程。我们将在下一部分进入摇树，这在很大程度上依赖于这个过程作为一个构建模块，所以我鼓励你用 Terser 做一点实验，并查看更多的细节。所以下次加入我们来学习现代 JavaScript 树摇动吧！

如果你有兴趣在这个博客上看到什么，或者你认为我应该看看，一定要联系我 [@gitinbit](http://twitter.com/gitinbit) 。这里的所有代码都可以在 Github 上找到。干杯！

# 参考资料和进一步阅读

[https://dennyscott.io/reduce-js-bundle-part-one](https://dennyscott.io/reduce-js-bundle-part-one)

[https://mootools.net/](https://mootools.net/)

[https://en.wikipedia.org/wiki/AngularJS](https://en.wikipedia.org/wiki/AngularJS)

[https://jquery.com/](https://jquery.com/)

[https://www.cloudflare.com/learning/cdn/what-is-a-cdn/](https://www.cloudflare.com/learning/cdn/what-is-a-cdn/)

[https://en.wikipedia.org/wiki/Single-page_application](https://en.wikipedia.org/wiki/Single-page_application)

https://www.keycdn.com/support/the-growth-of-web-page-size

【https://en.wikipedia.org/wiki/Dead_code_elimination 

[https://github.com/terser-js/terser](https://github.com/terser-js/terser)

[https://github.com/rollup/rollup](https://github.com/rollup/rollup)

[https://webpack.js.org/](https://webpack.js.org/)

[https://webpack.js.org/guides/tree-shaking/](https://webpack.js.org/guides/tree-shaking/)

[https://hacks . Mozilla . org/2018/03/es-modules-a-卡通-深潜/](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)

[https://developers . Google . com/web/fundamentals/performance/optimizing-JavaScript/tree-shaking/](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking/)