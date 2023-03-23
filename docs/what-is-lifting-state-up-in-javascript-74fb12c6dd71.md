# React 中的提升状态是什么？

> 原文：<https://betterprogramming.pub/what-is-lifting-state-up-in-javascript-74fb12c6dd71>

## 使用 React.js 演示提升状态

![](img/dc60b2ba977e6baf71f40cb9ee17250b.png)

提升状态并不那么美妙，但它确实将应用程序状态限制在适当的上下文和范围内。(由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[卡莱布·尼姆兹](https://unsplash.com/@kalebnimz?utm_source=medium&utm_medium=referral)拍摄)

> 在 React 中，共享状态是通过将它向上移动到需要它的组件的最近的公共祖先来完成的。这被称为“提升状态”— [反应文件](https://reactjs.org/docs/lifting-state-up.html)

最近，我一直在想 Redux 会在 2019 年的一个全新的 React 应用程序中用于什么，因为 React 本身可以处理状态管理。

我最近看到了 Kent C. Dodds 的这篇文章，他说他只使用过一次 Redux，原因是一样的——React 处理状态。

然后他继续介绍*提升状态*，他称之为解决跨组件共享状态问题的“坚如磐石”的解决方案。

让我们完成一个代码示例，看看*提升状态*是什么意思。

因此，提升状态意味着将状态从一个级别提升到下一个级别，而不必使状态全局化。这就是我认为状态管理的工作方式，因为它类似于变量、函数和块范围。

很高兴知道 React 可以处理状态管理和提升状态，而不需要额外的库依赖(Redux)。

# Vue 和 Angular 中的提升状态

当然，上下文和范围的概念适用于整个 JavaScript，看看这些提升状态的其他演示:

*   Vue 提供了额外的默认组件传递，根据[关于状态管理的 Vue 文档](https://medium.com/u/3cb21c06e2fa#ad)很好地介绍了 Vue.js 中的状态:

[](https://vuejs.org/v2/guide/state-management.html#ad) [## 状态管理- Vue.js

### 大型应用程序通常会变得越来越复杂，因为许多状态分散在许多组件中，而且…

vuejs.org](https://vuejs.org/v2/guide/state-management.html#ad) 

# 了解何时使用 Redux 或 Vuex

当然，一些用例可能会从 Redux 中受益，这些指南可以帮助您了解 Redux 何时会是更好的选择:

*   [漫画替身](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6)作者[林克拉克](https://medium.com/u/d3391efe481a?source=post_page-----74fb12c6dd71--------------------------------):

[](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6) [## Redux 的卡通介绍

code-cartoons.com](https://code-cartoons.com/a-cartoon-intro-to-redux-3afb775501a6) 

*   [由](https://levelup.gitconnected.com/an-unforgettable-way-to-learn-redux-f36afd38c966) [Ohans Emmanuel](https://medium.com/u/aea1cff96976?source=post_page-----74fb12c6dd71--------------------------------) 对 Redux 进行视觉引导:

[](https://levelup.gitconnected.com/an-unforgettable-way-to-learn-redux-f36afd38c966) [## 一个难忘的学习 Redux 的方法——可视化指南

### 以下是学习 Redux 的可视化指南。一个专注于核心 Redux 原则的，令人难忘的解释…

levelup.gitconnected.com](https://levelup.gitconnected.com/an-unforgettable-way-to-learn-redux-f36afd38c966) 

*   Vuex (Vue 相当于 Redux)，由 [Adam Jar](https://medium.com/u/f8e4f20476ed?source=post_page-----74fb12c6dd71--------------------------------) 直观解释:

[](https://medium.com/vue-mastery/vuex-explained-visually-f17c8c76d6c4) [## Vuex 形象地解释道

### 在一个充满组件的应用程序中管理状态可能很困难。脸书艰难地发现了这一点，并创造了…

medium.com](https://medium.com/vue-mastery/vuex-explained-visually-f17c8c76d6c4) 

*   根据@malcoded，Redux 也可以与 Angular 一起使用:

[](https://malcoded.com/posts/angular-ngrx-guide/) [## 使用 NgRx 存储学习角度中的 Redux

### 你可能会同意我的观点，随着角度应用变得越来越大，它们变得很难被忽略…

malcoded.com](https://malcoded.com/posts/angular-ngrx-guide/) 

编码快乐！

[德里克·奥斯汀博士](https://www.linkedin.com/in/derek-austin/)是《职业规划:如何在 6 个月内成为一名成功的 6 位数程序员 一书的作者，该书现已在亚马逊上架。