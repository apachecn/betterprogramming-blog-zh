# JavaScript 中的动态导入和树抖动

> 原文：<https://betterprogramming.pub/dynamic-import-and-tree-shaking-in-javascript-ddc2f3cd69f>

## 为什么动态导入不总是最佳解决方案

![](img/77be5bcb71854f9b518fcbe715bc9276.png)

[凯瓦尔](https://unsplash.com/@kewal?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

从 ES2020 年开始，我们正式能够使用动态导入功能。在最新版本的所有功能中，这个功能看起来尤为突出。一些人对我的另一篇文章发表了评论，谈到 ES2020 中的[新功能，他们一直期待着手中有这个功能。](https://medium.com/javascript-in-plain-english/new-features-in-es2020-you-should-check-b4974d9d7edc)

这篇文章讲述了当涉及到**树摇动**时，通常导入和动态导入的区别。我希望这是对你有用的信息。

# 什么是树摇动？

树摇动，死代码消除的另一个名字，随着 Webpack 进入 JavaScript 世界。嗯……它可能很久以前就存在了，但是是 Webpack 开始认真地使用这个术语。他们甚至给了你一个让 Webpack 执行死代码清除的方法。

树抖动可以减小捆绑的 JavaScript 文件的大小。它只适用于`import`和`export`语法。因此，不支持常见的 JavaScript 语法`require`。例如，Webpack 命名每个模块，它包括在构建时从其他文件调用到捆绑文件中的模块。

# 项目设置

这是我用于测试的项目。

`a.ts`文件中有四个函数，还有项目的主文件，从`a.ts`调用导出的函数。

```
import { pow } from './a';pow(1, 2);
```

`main.ts`只导入`add`并使用。这个项目中没有使用其他函数。

这是 Webpack 设置文件:

```
mode: 'development',
optimization: {
  usedExports: true
}
```

我将`mode`设置为`development,`这样捆绑文件就不会被缩小，并将`usedExported`设置为`true`用于树抖动。

```
"build": "webpack"
```

现在，我将在终端上运行这个脚本。

# 静态导入的结果(部分)

```
> npm run build
```

文件被打包了。`main.ts`用`import`关键字从`a.ts`中抓取`pow`函数。

```
// main.ts
import { pow } from './a';pow(1, 2);
```

在`bundle.js`中，你可以看到这个:

即使所有导出的函数都包含在包中，实际上只有`pow`被抓取。

`unused harmony export ...`只是一条消息，让您和 Webpack 知道哪些已经导出。

这样的结果呢？：

```
// main.ts
import { pow, multiply } from './a';pow(1, 2);
```

`multiply`不用，尽管是进口的。捆绑文件的结果是相同的，这意味着 Webpack 将实际使用的方法放在它的导出列表中。

# 静态导入的结果(完全)

我想知道如果我一次导入所有的函数会有什么不同:

```
// main.ts
import * as module from './a';module.pow(1, 2);
```

结果是一样的。

有`default`功能的情况呢？

```
export default divide;
```

现在，`divide`是默认的函数，当您没有指定要导入的函数时，它将被导出。

```
// main.js
import divide, { pow } from './a';divide(1, 2);
pow(1, 2);
```

结果略有不同:

现在，Webpack 增加了`divide`作为默认导出功能。

但关键是 Webpack *知道*哪些函数实际上被使用了，并以自己的方式对它们进行分类。

***注*** *在 Webpack 的官方文档中，它说决定哪些函数需要包含在捆绑文件中实际上是极其困难的。它还讨论了每个函数的副作用——例如，在其当前作用域的外部作用域中更改值。这个例子很容易测试。*

# 动态导入的结果

现在，让我们更改代码以使用动态导入:

```
import('./a').then(module => {
  const { pow } = module;
  pow(1, 2);
});
```

动态导入返回一个`Promise`，您可以链接`then`或`catch`方法。如果文件加载成功，结果将发送至`then`。它需要一个参数，通常命名为`module`。`module`包含每一个导出功能。

本质上，动态导入被认为是一个非树摇动支持特性。让我们看看这个包文件是什么样子的。

当我运行`npm run build`时，一个新的包文件被命名为`0.bundle.js`(我想如果你愿意，你可以改变它们的名字):

每个函数都包含在导出列表中。

# 模式:生产完全排除未使用的功能

你之所以能看到捆绑文件中也包含了未使用的函数，是因为你在`webpack.config.json`中的`mode`是`development`，而不是`production`。

按如下方式更改模式:

```
"mode": "production"
```

然后去掉这部分:

```
// remove
"optimization": {
  "usedExports": true
}
```

现在，运行`npm run build`你会看到这个。

## 使用静态导入

如我所说，动态导入返回一个`promise`，它链接了`then`，但是似乎唯一使用的函数也包含在捆绑文件中。

但是，还有一个文件:

每个功能都包括在内。

# 为什么动态导入不支持摇树

你可能会奇怪为什么这个功能不支持摇树。是因为进口时机不明确。例如，假设您只想在数量为奇数时导入:

```
if (isOdd(num)) {
  import('./odd').then(module => ...);
}
```

从 Webpack 的角度来看，它不知道该文件最终是否会被导入。因为`isOdd`并不能保证它永远是真的。

Webpack Github 库有一个[问题——维护者回答如下。](https://github.com/webpack/webpack/issues/7500)

![](img/fbf25aa198b543b8d783283cfae02397.png)

Florent Cailhol 说 Webpack 不支持树抖动

# 结论

我认为动态导入是一个很好的特性，因为它减少了运行时加载所有函数和模块的时间。当您不需要时，可以避免导入不必要的文件。

但是，由于动态导入不允许你做树抖动，它的所有功能都会被导入，所以你不应该滥用这个特性。

# 资源

*   [通过树抖动减少 JavaScript 负载——网络基础](https://developers.google.com/web/fundamentals/performance/optimizing-javascript/tree-shaking)
*   [摇树—网络包](https://webpack.js.org/guides/tree-shaking/#root)
*   [动态导入建议书— TC39](https://github.com/tc39/proposal-dynamic-import)