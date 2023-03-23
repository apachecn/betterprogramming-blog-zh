# React 挂钩入门

> 原文：<https://betterprogramming.pub/getting-started-with-react-hooks-a932809a3834>

## 或者至少了解它们是什么

![](img/ed748502868bb967828fe85ec656e164.png)

照片由 [Zyanya BMO](https://unsplash.com/@zyan_bmo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

当我第一次开始学习 React 时，有一件事我很确定:如果你想使用它的状态和生命周期方法，你需要使用类组件——而不是功能组件。

# 为什么要反应钩子？

根据[文档](https://reactjs.org/docs/hooks-overview.html)，钩子让你不用写类就可以使用状态和其他 React 特性。

理解钩子为什么会出现可能会令人困惑。如果你有一些时间，我推荐你看一下 2018 年 React 大会上 Dan Abramov 的[关于钩子的介绍](https://youtu.be/dpw9EHDh2bM?t=684)以获得更多的背景知识。他在引导你理解钩子方面做得很好，就好像这个概念是你自己想出来的一样。

在不涉及类与函数的细节的情况下，对于大多数简单的情况，构建功能组件更简单，但是缺少状态意味着它们的 React 功能有限。

反作用力挂钩有助于弥合这一差距。

# React 钩子如何工作

钩子是一个特殊的函数，可以让你“钩入”React 特性。例如，`useState`是一个钩子，可以让你给函数组件添加反应状态。

要开始使用它，您需要从 React 导入`useState`钩子:

```
import React, { useState } from ‘react’;
```

这就是我如何将我的名字添加到功能组件中的状态:

```
const [name, setName] = useState(‘Natalie’)
```

`useState`返回一个数组，我们正在析构它以获得状态和状态设置函数。

如果我要更改我的名字，下面是我如何在 state 中更新它:

```
setName(‘Miss Cheese’)
```

就是这样！比把功能组件改成类组件再做`this.setState({ name: 'Miss Cheese' })`容易多了。

如果我想使用我的名字，我只要使用变量`name`，React 就会知道该怎么做。

# 尽管如此，还是有一些警告

钩子只能在顶层调用，不能在循环、条件或嵌套函数中调用。

所以你必须无条件地爱钩子(Ryan Florence 开的[玩笑](https://twitter.com/thekitze/status/1055869060200706048))。

# 结束语

要不要用钩子由你决定。

在我目前的项目中，我已经用钩子将我的一些类组件转换成功能组件，这使得我的组件更短、更简单。从现在起，我将使用钩子——因为我知道它们是什么了。

# 资源

*   钩子文档:[https://reactjs.org/docs/hooks-overview.html](https://reactjs.org/docs/hooks-overview.html)
*   Dan Abramov 在 React Conf 2018 上对 Hooks 的介绍: