# 在 React 应用中编写更简洁代码的 14 个有益技巧

> 原文：<https://betterprogramming.pub/14-beneficial-tips-to-write-cleaner-code-in-react-apps-a167798fa1ba>

## 通过书写清洁器成为卫生编码员

![](img/24e4b5fc46b50c4432cf4625c508c8fe.png)

照片由 Kobu Agency 在 Unsplash 上拍摄

在你职业生涯的某个阶段，写干净的代码是必须的，尤其是当你试图获得你的第一份开发工作的时候。

这是让你成为一名团队成员的基本要素，在求职面试中，你可以成功，也可以失败。你如何写代码是他们在做出雇佣决定之前想要考虑的事情之一。你的代码应该是人类可以理解的，而不仅仅是机器。

你的项目越大，这篇文章中列出的东西就越适用，对于小项目来说*可能*就没必要了。用你最好的判断力。

以下是在 React 应用中编写更简洁代码的 14 个有益技巧。

# 1.摧毁你的道具

析构你的道具是一个很好的方法，有助于使你的编码器更干净，更易维护。这是因为您清楚地定义或声明了某个东西(如组件)正在使用什么。

它不强迫开发人员通读组件的实现来找出与组件相关的所有道具。

它还让您能够声明默认值。您可能已经多次见过默认值:

JavaScript 中关于析构的最酷的事情之一是它允许你支持不同的参数变化。

例如，假设您有一个 *authenticate* 函数，该函数使用一个`token`作为参数来认证用户。

现在，由于新的服务器响应结构，您希望它接收`jwt_token`。您可以轻松地支持这两个参数，而无需对代码做太多更改:

当代码到达`token`时，`jwt_token`将被求值，因此如果`jwt_token`是有效令牌，而`token`是`undefined`，那么`token`的值将成为`jwt_token`的值。

如果`token`已经是某个真值(一个真实的令牌)，它将保持自己。

# 2.文件夹化您的组件

让我们看看下面的目录结构:

```
src
  components
    Breadcrumb.js
    CollapsedSeparator.js
    Input
      Input.js
      index.js
      utils.js
      focusManager.js
    Card
      Card.js
      index.js
      CardDivider.js
    Button.js
    Typography.js
```

众所周知，面包屑作为其核心功能之一，与某种分隔符相关联。`CollapsedSeparator`组件是在`Breadcrumb.js`内部导入的，所以我们知道它们都与实现相关。

然而，不了解这些信息的人可能会认为`Breadcrumb`和`CollapsedSeparator`是两个完全独立的组件，彼此之间没有任何关系。

如果`CollapsedSeparator`没有提供任何与面包屑相关的明确指示，例如前缀为*的面包屑* ( `BreadcrumbCollapsedSeparator.js`)，情况尤其如此。

知道它们是相关的，我们可能会问为什么它们不在像`Input`和`Card`这样的文件夹中。我们可能会开始做出奇怪的假设，比如:“我想知道是否有人把它放在那里，看看我是否会成为一个好撒玛利亚人，然后把它拿出来……”

干净代码实践的效果应该是相反的——开发人员应该能够阅读您的代码，并很快理解这种情况！

*文件夹化*面包屑看起来像这样:

```
src
  components
    Breadcrumb
      Breadcrumb.js
      index.js
      CollapsedSeparator.js Input
      Input.js
      index.js
      utils.js
      focusManager.js
    Card
      Card.js
      index.js
      CardDivider.js
    Button.js
    Typography.js 
```

现在，不管此后创建了多少与面包屑相关的组件，只要它们位于同一个目录中，我们就知道它们与`Breadcrumb`相关:

```
src
  components
    Breadcrumb
      Breadcrumb.js
      index.js
      CollapsedSeparator.js
      Expander.js
      BreadcrumbText.js
      BreadcrumbHotdog.js
      BreadcrumbFishes.js
      BreadcrumbLeftOvers.js
      BreadcrumbHead.js
      BreadcrumbAddict.js
      BreadcrumbDragon0814.js
      BreadcrumbContext.jsInput
      Input.js
      index.js
      utils.js
      focusManager.js
    Card
      Card.js
      index.js
      CardDivider.js
    Button.js
    Typography.js
```

# 3.使用标准命名约定

使用标准约定命名组件使得其他开发人员更容易阅读您的代码。

例如，[高阶元件](https://reactjs.org/docs/higher-order-components.html)通常以`with`为前缀，大多数人习惯于:

如果你决定做一些不同的事情，比如:

它是完全有效的 JavaScript，这样命名没有任何问题。但是，`use`已经有了一个标准的命名约定，它已经和[的 React 钩子](https://reactjs.org/docs/hooks-reference.html)一起出现了。

只是在分享代码时要小心，尤其是在寻求帮助时。人们可能已经适应了每天看到约定俗成的东西。

# 4.避免布尔陷阱

在决定输出时，要格外小心原始布尔，它用于确定某些东西的输出值。

众所周知，这是一种代码味道，它迫使开发人员查看组件的源代码/实现，以便能够对结果做出准确的假设。

例如，假设我们声明了一个排版组件，它采用了这些可用选项:`'h1'`、`'h2'`、`'h3'`、`'h4'`、`'h5'`、`'h6'`、`'title'`、`'subheading'`。

当它们像这样传入时，你怎么知道它们将如何被应用？

```
const App = () => (
  <Typography *color*="primary" *align*="center" *subheading* *title*>
    Welcome to my bio
  </Typography>
)
```

那些对 React(或者更恰当地说，JavaScript)更有经验的人可能已经猜到`title`将会超越`subheading`。订单的工作方式是，最后一个订单会覆盖前一个订单。

但问题是，如果不看源代码，我们将无法真正说出`title`或`subheading`会应用到什么程度。

例如:

即使`title`在这种情况下获胜，`text-transform: uppercase` CSS 行仍然不会被应用。这是因为`subheading`在其实现中用`text-transform: none !important;`声明了更高的特异性。

如果我们不够小心，调试样式问题可能会变得非常困难，特别是当它不会向控制台显示任何警告/错误时。这可能会使组件的签名复杂化。

这里只是一个更干净的替代方案的例子，它重新实现了解决这个问题的`Typography`组件:

```
const App = () => 
<Typography *variant*="title">Welcome to my bio</Typography>
```

排版:

现在，当我们在`App`组件中传递`variant="title"`时，我们将确信只有`title`会被应用。它省去了我们必须查看源代码来确定结果的麻烦。

你也可以做一个简单的 if/else 来计算道具:

但是这样做的最大好处是，您可以只做这个简单、干净的一行程序，然后就可以收工了:

```
const result = styles[variant]
```

# 5.使用粗箭头函数

使用粗箭头函数是在 JavaScript 中声明函数的一种更短、更简洁的方式。在这种情况下，将它们命名为*函数表达式*更合适。

然而，有些时候你不想在函数表达式上使用粗箭头函数，比如当你需要提升的时候。

在 React 中，同样的概念也适用。但是，如果不需要提升，使用箭头语法是一个更好的替代方法:

但是在这个例子中，您很难看出它的好处…当您使用简单的一行程序时，arrow 函数的魅力大放异彩:

俏皮话让每个人都开心！

# 6.把独立的函数放在你的定制钩子之外

我看到一些人在他们的自定义钩子中声明不需要的函数。

这使得定制钩子变得有点臃肿，随着它变得越来越长，越来越难阅读，因为一些开发人员可能会开始质疑钩子是否真的依赖于钩子内部的函数。

如果没有，最好把它移到外面，这样就能清楚地知道什么依赖于钩子，什么不依赖于钩子。

这里有一个例子:

看这个例子，`removeFalseyImages`不需要在定制钩子里面。相反，它可以被提取出来，并且仍然可以在钩子内部使用，不会有任何问题，因为它不与它的状态交互。

# 7.保持一致

保持一致也是 JavaScript 中普遍推荐的方法。

至于 React，保持一致:

1.  进出口。
2.  命名组件、挂钩、悬挂夹具、`classNames`。

在导入和导出组件时，当我想在两者之间进行导出时，我有时喜欢使用以下语法:

```
*import* App *from* './App'*export* { *default* *as* Breadcrumb } *from* './Breadcrumb'*export* *default* App
```

但是，我同样喜欢这个语法:

```
*export* { *default* } *from* './App'*export* { *default* *as* Breadcrumb } *from* './Breadcrumb'
```

无论你喜欢哪一个，只要确保你坚持为每个项目选择一个，这样就简单了。

与命名约定保持一致也是一条非常重要的规则。

当你定义一个钩子，比如`useApp`，重要的是用前缀`use`命名你的下一个钩子，比如`useController`。

如果你不这样做，你最终会这样做:

导入两个挂钩:

不太明显的是，`basicController`是一个定制的 React 挂钩，就像`useApp`一样，迫使开发人员查看代码内部以找出真相。

如果我们保持一致，我们可以让它变得明显:

```
const app = useApp()const controller = useBasicController()
```

# 8.组件化重复元素

组件化只是将重复元素转换成可重用组件的一种花哨说法。

每个人都有在 React 中编写重复代码的理由，无论是有意还是无意。

不管是什么原因，不要让大量的二拍子保持原样是个好主意。

首先，你可能会养成重复做的习惯，因为你不在乎之前的重复代码。你这样做是如何成为一名团队成员的？

将来你会给你的队友增加负担，因为他们可能会因为看到重复的元素而感到沮丧。他们甚至可能会感到困惑，尤其是当他们被安排去做编辑的时候。

更糟糕的是，你的队友可能会因为重复的代码而受到批评，而他们甚至没有写代码。当他们这么做的时候，他们只是代表你为团队做了一件事。通过避免将来的重复来回报他们！

让我们看看下面的代码，并将重复的部分组件化:

现在，如果有人告诉你将网格尺寸从`xs={12} sm={6}`改为`xs={12} sm={4}`，这将是一个麻烦，因为你必须改变它们四次。

组件化的美妙之处在于，您只需做出一个更改，它就会反映到所有的网格中:

在最基本的提取层面上，这对于人类来说变得更容易阅读和维护，同时仍然保持正常的实现！

# 9.保持你的组件简单

我在制作 web 应用时学到的不是保持组件简单，而是避免让组件变得复杂。

下面是一个不必要复杂的组件示例:

`ConfirmAvailability.js`

该组件原本是一个简单的组件，但是由于逻辑是紧密耦合的，所以它负责多种事情。

在编写这段代码的时候，React 钩子还没有发布，但是仍然有高阶组件和渲染道具。

因此，我们将仅使用其中一种模式来简化这一点，以演示如何使您的组件更简单(而不改变功能):

`SelectTimeZone.js`

`TimeZonePicker.js`

现在，我们有了一个更清晰的方法，我们从它的对应表示中提取了逻辑。对这些组件进行单元测试现在变得容易多了！

# 10.如果使用状态变得复杂，请使用 useReducer

当跟踪多个状态时，使用`useState`开始变得难以管理。

这可能看起来像这样:

如果你把它转换成一个`useReducer`，这将变得更容易管理:

可以说，当你看的时候，这可能不会比`useState`方法更干净。然而，当您使用`useReducer`版本实现定制挂钩时，管理起来更容易。

您不必担心在钩子的多个部分跟踪状态更新，因为您将在`reducer`中的一个地方定义所有的状态更新。

我们现在还定义了一组官方规则，说明如何在`reducer`函数中操作`state.frogs`。我们也有一个直接的，更清晰的逻辑分离。

换句话说，如果我们继续使用`useState`，将不会有预定义的实体。这与`useReducer`不同，在`useReducer`中，所有的逻辑都放在`reducer`中。

在`useState`版本中，我们必须在钩子内部声明函数来计算状态的下一部分。

这是编写逻辑之外的工作。而在`useReducer`版本中，我们不必这样做。相反，我们将它们移到了`reducer`函数中。

我们只需要调用动作的类型，这就是它需要担心的。

# 11.在枯燥的地方使用函数声明

一个很好的例子是`useEffect`清理处理器:

```
React.useEffect(() => {
  setMounted(true)return () => {
    setMounted(false)
  }
}, [])
```

作为 React 开发者，我们知道这是做什么的，所以这不是问题。

但是，如果你假设其他人会阅读你的代码，那么在使用函数声明时，像这样明确地使用代码是一个好主意，因为我们可以根据自己的优势来命名它们。

例如:

```
React.useEffect(() => {
  setMounted(true)return function cleanup() {
    setMounted(false)
  }
}, [])
```

这更清楚地描述了当你返回函数时会发生什么。

# 12.使用更漂亮的

[更漂亮的](https://prettier.io/)帮助你和你的团队保持代码格式的一致性。它节省了时间和精力，并减少了在代码评审中讨论风格的需要。

它还强制执行干净的代码实践，您可以根据感觉什么是正确的，什么是不正确的来配置。

# 13.在大片段上使用小片段

小片段:

```
const App = () => (
  <>
    <FrogsTable />
    <FrogsGallery />
  </>
)
```

大片段:

```
const App = () => (
  <React.Fragment>
    <FrogsTable />
    <FrogsGallery />
  </React.Fragment>
)
```

# 14.把东西整理好

写代码时我喜欢做的一件事是把事情按顺序排好，比如导入文件时(除了`react`导入):

```
import React from 'react'
import { useSelector } from 'react-redux'
import styled from 'styled-components'
import FrogsGallery from './FrogsGallery'
import FrogsTable from './FrogsTable'
import Stations from './Stations'
import * as errorHelpers from '../utils/errorHelpers'
import * as utils from '../utils/'
```

你们中的一些人可能会想，这甚至没有按照字母顺序排列。这只是这个订购方案的一部分。

我喜欢以一种干净的方式订购我的进口产品，按照优先顺序使用这些指南:

1.  反应导入。
2.  库导入(按字母顺序)。
3.  从项目绝对导入(按字母顺序)。
4.  相对进口(按字母顺序排列)。
5.  `import * as`
6.  `import './<some file>.<some ext>'`

而且，我还喜欢用其他方式给变量排序:

遵循指南有助于创建更干净的代码库。

# 结论

这就结束了！我希望你发现这是有用的，继续关注更多！