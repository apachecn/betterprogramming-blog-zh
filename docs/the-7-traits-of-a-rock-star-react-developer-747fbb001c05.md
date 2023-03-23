# 摇滚明星 React 开发者的 7 个特质

> 原文：<https://betterprogramming.pub/the-7-traits-of-a-rock-star-react-developer-747fbb001c05>

## 造成差异的习惯

![](img/22db01d78781dd7d507dabac5c4eb7ff.png)

照片由[丹尼尔·伊德里](https://unsplash.com/@ricaros?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programmer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

完成工作是软件开发中最重要的事情。但是怎么把自己和别人分开呢？根据我的经验，我可以说区别在于方法。

这些年来，我和几个 React 开发人员一起工作过。这里有一些区分优秀开发人员和普通开发人员的东西。

# 1.他们不使用索引作为关键字

当我们呈现一个列表时，我们通常会得到一个警告，说我们需要为每个列表项提供一个键。我们这些懒人经常用索引作为键。

大概是这样的:

坏榜样

但是在 React [文档](https://reactjs.org/docs/lists-and-keys.html)中，他们这样说:

> "如果项目的顺序可能会改变，我们不建议对键使用索引。这可能会对性能产生负面影响，并可能导致组件状态出现问题。

使用唯一属性作为键总是明智的，或者如果您的数据没有任何唯一属性，那么您可以考虑使用`nanoid`，它会生成一个唯一键。

我们之前的例子可以改写成这样:

好榜样

> **更新:**根据更新的 nanoid 文档，不再建议使用 nanoid()来解决性能问题。所以你真的需要为你的列表找一个合适的键。

# 2.他们在 div 上使用片段

我们都知道 React 使用虚拟 DOM，并将其与实际 DOM 进行比较以进行渲染。但是如果有不必要的节点比较，对性能不好。

例如，如果我们想要呈现三个组件，我们将它们组合在一个`div`中。

坏榜样

但是如果我们仔细注意，那么这里使用的`div`什么也没做。此外，它还在我们的虚拟 DOM 中添加了一个额外的节点。React 片段解决了这个问题。文档中说的是:

> React 中的一个常见模式是一个组件返回多个元素。片段让您可以对一系列子节点进行分组，而无需向 DOM 添加额外的节点。

因此，让我们修改我们之前的例子:

反应片段的良好使用

这样，我们可以在不添加额外节点的情况下对多个组件进行分组。

# 3.最小依赖性

`Props`都很精彩。但是初学者有时不能理解仅仅传递一堆道具就能产生许多问题。例如，看看下面的代码。

坏榜样

这里有许多不好的事情正在发生:

*   我们通过一个名为`data`的道具。我们不知道它从何而来，也不知道它是否有效。
*   `isLoading`表示可能正在进行某种异步操作
*   只有上帝知道如何处置`searchData`。可能是有人加了，忘了去掉。

所以过了某个点，这就成问题了。现在，您必须沿着树向上，找到这些数据各自的用途。

## 有什么推荐的

*   优秀的开发人员会尽量减少道具的数量。仅传递与呈现 UI 相关的属性。
*   对于其他目的，使用某种中央数据存储(如 Redux 或 Context)

我们之前的例子可以是这样的:

所以现在你有了一个不再依赖于它的父组件的组件。

# 4.他们有效地利用图书馆

图书馆是为了让我们的生活更容易。但是如果你开始盲目地使用大量的包，你的项目很快就会被大量不必要的代码弄得臃肿不堪。

有经验的开发人员会寻找完成工作的最小的库。

比如我们都知道并且热爱 [Moment.js](https://momentjs.com/) ，但是你的项目需要所有的本地化吗？我们大多数人都不知道。

仅导入库中您需要的部分。

这里有一个[很好的资源](https://github.com/GoogleChromeLabs/webpack-libs-optimizations)告诉你如何去做。

# 5.他们不在渲染中定义函数

React 允许我们在 JSX 内部定义函数。像下面这样:

像这种简单的操作，没什么大不了的。但这是一种不好的做法。它损害了可读性。相反，你可以做的是:

坏榜样

# 6.他们理解 webpack

React 使用令人敬畏的实用程序 Create React App，使得从头开始搭建项目变得很容易。

在此之后，我们一般不需要使用其他任何东西。我们只要`npm install`和`npm run build`，我们就可以出发了。

但是在引擎盖下，所有的魔法都发生在 webpack、Babel 和其他库上。

在我们的日常生活中，我们不需要那么频繁地接触这些东西，但是在某些情况下(比如代码拆分)，我们需要了解这些东西是如何工作的。

webpack 的知识在这里派上了用场，有经验的开发人员对此至少有一个最低限度的了解。

[](/6-webpack-concepts-for-advanced-react-developers-d016da2cad52) [## 面向高级 React 开发人员的 6 个 Webpack 概念

### 帮助您理解从内部做出反应的概念

better 编程. pub](/6-webpack-concepts-for-advanced-react-developers-d016da2cad52) 

# 7.他们理解和解

React 的性能非常好，因为它不在实际的 DOM 上进行操作。它创建自己的虚拟 DOM，并且只在必要时更新 DOM 的一部分。

这个过程叫做*r*[趋化](https://reactjs.org/docs/faq-internals.html) *。*

尽管 React 在默认情况下性能很高，但是理解这些过程有助于提高性能。

# 结论

你可能在日常生活中不需要所有这些。但是拥有这些概念可以让你走得更远。

如果你喜欢这篇文章，这是你下一个 React 项目的另一篇文章

祝您愉快！

```
**Have something to say? Get in touch with me via** [**LinkedIn**](https://www.linkedin.com/in/56faisal/)
```

[](https://javascript.plainenglish.io/20-essential-parts-of-any-large-scale-react-app-ee4bd35436a0) [## 任何大型 React 应用程序的 20 个基本部分

### 如果您正在编写企业级代码，您需要了解这一点

javascript.plainenglish.io](https://javascript.plainenglish.io/20-essential-parts-of-any-large-scale-react-app-ee4bd35436a0)