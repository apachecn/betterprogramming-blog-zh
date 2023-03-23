# 我最喜欢的 11 个 JavaScript 库和工具

> 原文：<https://betterprogramming.pub/my-11-favorite-javascript-libraries-and-tools-dd7e0cc952a1>

## 我经常回头看的库和工具的概述

![](img/1016aee5ef0086a207067a6079fd6026.png)

托德·夸肯布什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

你们中的一些人可能已经知道，我已经做了 20 多年的全栈高级开发人员。我很幸运地在世界上最大的软件公司之一工作。

今天，我将列出 11 个我最喜欢的用于 React 应用程序的工具和库。其中大部分是我偶然发现的工具，并且从未停止使用。

我认为下面的列表是我的个人工具箱。它们的排列没有特定的顺序。

我们来看看吧！

# 类名

小包装`[classnames](https://www.npmjs.com/package/classnames)`是我的最爱之一。它帮助你为你的元素定义动态类名。为了动态地确定元素的类名，我们以各种方式连接字符串:

让我们有条件地将类名连接在一起。函数将字符串和对象转换为一个字符串:

该软件包针对性能进行了高度优化，每周下载量超过 600 万次。我在所有的应用程序和文章演示中使用它。

# CSS 模块

CSS 模块是我的项目中的必备组件。如果您使用正确的文件名并安装所需的依赖项，它们可以与流行的框架一起工作，如 CRA (Create React App)和 Next.js。

通常，您可能会像这样导入 CSS:

但是这种方法有一个问题:类名`card`没有以任何方式限定作用域或命名空间。它很容易与其他组件或第三方库冲突。想象一下导入并使用第三方库，比如 Bootstrap 或 Semantic-UI。如果其中一个库公开了一个同名的类名(`.card`)，我们可能会遇到麻烦。这甚至可能在我们没有注意到的情况下发生。

而且，不用说，您可能稍后在您的存储库中用相同的类名创建另一个组件，而不记得您已经使用了`card`类名。麻烦就在前面！

如果我们将 CSS 模块与诸如 Webpack(例如 CRA 使用的)的模块捆绑器结合使用，我们将为我们的 CSS 类生成唯一的类名:

除了文件名(使用`*.module.css`是惯例)之外，我们的 CSS 文件在两个例子中都是相同的。但是用上面的语法导入它将导致生成一个唯一的(“限定范围的”)类名:

```
<div class="card_1hWc8">...</div>
```

如果您将组件共享为库，这将特别有用。而且在本地设置中，它将防止您遇到意外的麻烦。

# 以打字打的文件

我爱[打字稿](https://www.typescriptlang.org/)。一开始，我有点不知所措。我认为这很复杂，它让我的生活更加困难，而不是提供好处。这些错误很难修复(当时它们非常神秘)，并且没有多少库定义了它们的类型。

我敢说现在的 TypeScript 已经很成熟了。但这是什么呢？可以这样想:它是 JavaScript，带有可选的静态类型。确实是可选的，因为常规 JavaScript 也是完全有效的类型脚本代码。

TypeScript 是一个静态的打字工具，它将有助于减少你犯的错误。一个简单的例子可以很好地说明这一点——假设我们的状态中有一个对象:

我们将这个对象存储在我们的状态中，并将其注入到组件树中的其他组件中。然后我们将读取并使用我们的`data`对象的值。通常，我们必须记住(或者更常见的是，查找)对象具有哪些属性:`name`、`age`和`country`。三个属性不难记，但是如果我们的对象有 15 个属性呢？

这就是 TypeScript 发挥作用的地方。我们可以键入我们的对象，以便我们的 IDE(例如，VSCode、PhpStorm 等。)知道对象的类型:

现在，当传递数据对象时，我们的 IDE 知道两件事:对象具有哪些属性(提供自动完成功能)以及这些属性的值是什么类型。如果我试图像这样操作数据对象:

```
data.age = "Fifty years";
```

TypeScript 会直接告诉我们这是不可能的。age 属性有一个`number`类型，只有数字可以分配给这个特定的属性。

记住，我们不需要明确地输入所有的值和代码。TypeScript 非常聪明，它可以自动从代码中派生类型。如上所述，键入是可选的——它可以丰富您现有的代码。它不是一种全新的编程语言，而是 JavaScript 的超集。它是 JavaScript，有一点额外的东西(静态类型)。

也就是说，它确实有一点学习曲线。但是一旦你习惯了，它会让你的编程生活变得更容易。

# 萨斯(Scss)

帮你自己一个忙，忘掉顺风吧。我在最近的一篇文章中对此进行了论证:

[](https://medium.com/better-programming/tailwind-css-is-probably-overhyped-5272e5d58d4e) [## 顺风 CSS(可能)被夸大了

### 对流行实用框架 Tailwind CSS 的批判性审视

medium.com](https://medium.com/better-programming/tailwind-css-is-probably-overhyped-5272e5d58d4e) 

我知道这是一个强烈的观点，但我袖手旁观它。在我看来，学习常规的 CSS 并使用像 [Sass](https://sass-lang.com/) (“拥有超能力的 CSS”)这样的预处理器更有益(尤其是从长远来看)。

萨斯很棒。它确实做到了它所宣传的:它让 CSS 变得更棒。一个例子是您可以嵌套您的类，这使您的 CSS 代码更有组织性:

在上面的小片段中，你还可以看到我们如何定义和使用变量，以及我们如何通过使用&符号(`&`)来扩展一个类。

除了上面这个简单的例子，Sass 还可以提供更多的东西。我鼓励你去探索他们的[文档](https://sass-lang.com/documentation)。

像 CRA 和 Next.js 这样的框架提供了现成的支持。但是您必须自己安装 Sass 的实现。推荐使用`[sass](https://www.npmjs.com/package/sass)`，但是也有[替代实现](https://www.npmjs.com/package/node-sass)。

# 语义-用户界面-反应

我会马上说，我们公司不使用`[semantic-ui-react](https://www.npmjs.com/package/semantic-ui-react)`。但它是一组非常实用的组件(按钮、网格、表格、图标等)。)您可以用它来快速构建某样东西的原型。这正是我使用它的目的。我在 Medium 上的大多数文章都附带了包含小型应用程序的存储库和包含语义 UI 组件的演示。

[文档](https://react.semantic-ui.com/)非常棒，很容易在您的项目中使用:

```
$ yarn add semantic-ui-react semantic-ui-css
```

# Redux

当您的应用程序变大时，您确实需要适当的状态管理。专用状态管理工具好处的两个例子是，您的代码变得更容易测试，您的状态更容易持久化。

我更喜欢 [Redux](https://redux.js.org/) (还有旁边的； [Redux Toolkit](https://redux-toolkit.js.org/) )胜过其他任何东西。与任何观点一样，这是主观的，但我可以通过提及我们在非常大的 React 应用程序中使用 Redux 来证明这一点，没有任何问题。它为我们的存储库提供了清晰性，这是使用钩子和依赖注入解决方案(如 React Context)永远无法实现的。如果你想更多地了解这个话题，可以考虑阅读我今年早些时候写的另一篇文章:

[](https://medium.com/swlh/react-hooks-and-redux-are-friends-not-enemies-cb0ecfa613ed) [## React Hooks 和 Redux 是朋友，不是敌人

### 他们相处得很好，目的不一样。

medium.com](https://medium.com/swlh/react-hooks-and-redux-are-friends-not-enemies-cb0ecfa613ed) 

尽管 React 挂钩和上下文有时非常有用，但它们无法为大型应用程序提供 Redux 所能提供的相同功能和成熟度。

# Luxon(日期和时间管理)

多年来，我们一直使用著名的库 [Moment.js](https://momentjs.com/) 进行日期和时间操作，并获得了成功。然而，在 2020 年下半年，Moment 的维护者认为该项目是一个遗留项目。它没有死，但它已经完成，不再接收新功能。

他们建议使用替代的、更轻便的库，更好地支持今天的原生浏览器 API。他们的头号推荐是[卢克森](https://moment.github.io/luxon/docs/index.html)。

> 鲁迅可以被认为是瞬间的进化。它是由《时刻》的长期撰稿人艾萨克·坎布朗撰写的。
> 
> — Moment.js 维护者

我写了两篇关于鲁迅的文章，深入解释了时区和图书馆本身。这是该系列的第一篇文章:

[](https://js.plainenglish.io/dates-and-time-zone-awareness-in-your-react-application-with-luxon-a-moment-js-successor-d747b2cc0713) [## 认识一下 Luxon，新的 Moment.js 杀手

### 使用 Luxon 在 React 应用程序中设置日期和时区

js .平原英语. io](https://js.plainenglish.io/dates-and-time-zone-awareness-in-your-react-application-with-luxon-a-moment-js-successor-d747b2cc0713) 

# 其他工具和库

最后，我将列出几个我最喜欢的工具和库，它们不需要太多介绍，因为你可能已经听说过它们了。它们和我们已经讨论过的工具一起，构成了我最喜欢的库、框架和工具。

## **创建 React 应用程序(CRA)**

如果你以前用过 React，你可能已经用过 CRA 了。它是任何新项目的框架，并附带了一些现成的功能，如热重装、CSS 模块等等。本质上，这是一个非常好的预配置 Webpack 设置。如果您在运行 create-react-app 命令时使用`**npx**`(推荐),您将确保始终使用最新、最好的:

```
$ npx create-react-app MyApp
```

## **Next.js**

如果你已经熟悉了 CRA，我强烈推荐给 [Next.js](https://nextjs.org/) 一个机会。在我看来，这是一个进步，真的，这并不难学。我可能很快会就此单独写一篇文章。

Next.js 也是 React 应用程序的框架，它附带了诸如 SSR(服务器端呈现)、类型脚本支持、基于文件的路由等功能。

我再怎么大力推荐它也不为过——比起盖茨比等竞争对手，我更喜欢它——所以试一试吧。

**Webpack**
CRA 和 Next.js 都在后台使用 web pack 进行传输文件和模块捆绑。如果您经常使用这些工具，那么多了解一些 Webpack 本身的来龙去脉可能是明智的。配置 Webpack 起初看起来令人望而生畏，但是一旦你掌握了核心原则，就没那么难了。然而，在本文描述的所有工具中，这无疑是最难学习的东西之一。

Webpack 及其所有插件是一个非常强大的工具。

## **网络生活**

如果您正在为您的(POC)应用程序寻找免费托管，请不要再找了。使用 [Netlify](https://www.netlify.com/) 是一次爆炸，你将在眨眼之间启动并运行你的应用程序。更进一步，把你的一个域名连接到他们的基础设施也不难。我对网络生活的体验是积极的。

你有它！我个人最喜欢的 JavaScript 和 React。使用这些工具和库为你创造任何你想要的东西提供了坚实的基础。他们已经帮助我和我的团队很多年了，我只能想象未来会给我们带来什么。

感谢您的宝贵时间！