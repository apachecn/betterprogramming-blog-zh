# Vue 3 的组合 API 快速入门指南

> 原文：<https://betterprogramming.pub/the-quick-start-guide-for-vue-3s-composition-api-fc0913fa9d97>

## 等待终于结束了！Vue 3 的海贼王已经发布了

![](img/c362488ab46c06c4db49d23c6ca8bb12.png)

图片来自 [Vue.js](https://news.vuejs.org/) 。

现在，我们可以放手一搏，开始自信地摆弄组合 API，以编写更好的可维护和可重用代码。

组合 API 是这个版本的基本特性，有几篇很棒的文章解释了相对于“旧”选项 API 的[动机](https://css-tricks.com/an-early-look-at-the-vue-3-composition-api-in-the-wild/)和[好处](https://auth0.com/blog/getting-started-with-vue-3-composition-api/)。甚至还有一个关于这个话题的视频课程。

本指南的目标读者是已经了解组合 API 并希望快速入门的开发人员。

# 心态和心智模式

在我们直接进入代码示例之前，让我们回顾一下什么是组合 API。

这是一个纯粹的添加剂，并建议混合替代品。这个 API 是为逻辑组织、封装和代码重用而设计的，它提供了完整的 TS 支持。

# 设置

`setup`函数是组件的入口点。在这里，您可以*组合*组件的功能，并向模板或呈现函数公开任何变量或方法。

`setup`函数在`beforeCreated`生命周期挂钩之前被调用，因此无法访问您通常所期望的`this`。

它接收`props`和`context`作为参数。

*注意:* `*props*` *对象是反应性的，析构它会使其失去反应性。*

就像常规函数一样，为了使它异步，您在方法声明前添加了`async`。这与新的`Suspense`特性配合得非常好，你可以在文档中读到更多关于[的内容。](https://vuejsdevelopers.com/2020/07/13/vue-async-components-suspense/)

如果您正在使用 Vue 的 SFC，您可以利用语法上的 sugar [RFC](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md#using-setup-arguments) 并将变量暴露给模板，如下所示:

# 反应

Vue 3 中许多令人兴奋的事情之一是新的反应系统。它不仅被抽象成一个单独的包，而且还解决了在 Vue 2 中与数组或对象交互时的一些问题

## **状态**

既然我们已经理解了如何将变量从`setup`方法传递到模板，那么让我们看一下如何定义一个反应变量。

以前，使用选项 API，您将在数据选项中定义它。现在你有两个选择:`ref`或者`reactive`。

两者都允许您创建对对象或原始数据的被动引用。

由于 JavaScript 的工作方式，像`string`、 `number`和`boolean`这样的原语类型是通过值传递的，而对象和数组是通过引用传递的——因此引入了`ref`。

相应地，您可以将`ref`用于图元，将`reactive`用于对象。

*注意:* `*refs*` *总是被包裹在一个对象中，所以如果你想在你的* `*setup*` *方法中访问值，你就得这样访问*`*.value*`*:* `*firstName.value = 'Batman'*`*。*

## 计算

这里变化不大。它如你所料的那样工作。

## **观察者**

与 Options API 类似，您可以观察其他属性。

```
import { watch } from 'vue'watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* react to change */
})
```

## **方法**

为了创建一个方法，您只需编写一个常规的 JavaScript 函数。只要确认你的`return`或`export`功能，如果你希望它在你的模板中可用。

## 模板参考

如前所述，没有`this`，因此您可能想知道如何访问`this.$refs`。

## 生命周期挂钩

您可以通过调用`onMounted`和`onXXX`来访问除`created`和`beforeCreated`之外的所有生命周期挂钩。

```
import { onMounted, onUnmounted } from 'vue'onMounted(() => { /* do stuff here */ }
```

## 创建可组合的

我们已经看到了如何使用新的组合 API。让我们探索如何使用它来组成组件。

假设我们想要在几个组件中重用鼠标值 *X* 和 *Y* ，我们可以编写一个简单的 composable。

然后，我们可以在组件中使用这个可组合的组件。

```
<template> {{ x }}, {{ y }} </template><script setup>
import { useMousePosition } from './useMousePosition';export const { x, y } = useMousePosition();
</script>
```

# 实践和贡献

现在我们已经介绍了基础知识，您可以开始摆弄和创建一些 composables。

这也是一个为开源做贡献的绝佳机会，因为还没有太多的组件。如果你想要一些灵感，你可以看看[react——使用](https://github.com/streamich/react-use),将一些钩子移植到这个包中。

[](https://github.com/antfu/vueuse) [## 安特富/乌尤斯

### 基本 Vue 组合 API 的集合(受 react-use 启发)参考函数列表或文档了解更多…

github.com](https://github.com/antfu/vueuse) 

# 额外资源

以下是关于 Vue 3 的组合 API 的有用资源列表:

*   [公文](https://composition-api.vuejs.org/)
*   [用 Composition API 写的博客](https://levelup.gitconnected.com/developing-a-full-app-using-the-vue-3-composition-api-4fd9431f2136)
*   [视频解说](https://www.youtube.com/watch?v=Jy6M1LHsh5c)
*   [最佳实践模式视频](https://www.youtube.com/watch?v=6D58SI9P-aU&list=PLCxzy-hmQq9FnnpckFDeDtbpsUqxdheQn&index=15)

我希望这个指南对你有所帮助。感谢阅读，享受 Vue 3！