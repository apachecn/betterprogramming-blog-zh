# 如何用 Vue.observable 管理 Vue 的状态

> 原文：<https://betterprogramming.pub/how-to-manage-vues-state-with-vue-observable-25988a88938b>

## 让不必要的样板文件消失吧

![](img/c3b8d47f37eec89bc45b3560ad01a7ce.png)

保罗·斯科鲁普斯卡斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

我已经使用 Vue 至少三年了，从一开始，我就使用 Vuex 来控制我的应用程序中的常见状态，因为这是一个官方和核心支持的功能。对于大型应用程序，使用像 Vuex 这样的解决方案来保持代码的集中化、组织化和整个团队的可理解性是完全有意义的。但是对于较小的应用程序，在我看来，仅仅为了控制少量的数据，就必须编写太多的代码(状态、动作、突变、getters)。

总而言之，状态只是你的组件之间共享的一个对象，对吗？因此，也许我们可以用导出状态对象的 JavaScript 文件开始编写我们的解决方案:

```
// store.js
const state = { counter: 1 };
export default state;
```

现在我们只需导入它并将其作为计算属性，这样我们的组件就可以访问它的值:

到目前为止，我们还好。但是如果我们想用这样的方法来更新存储值呢？

```
 ,methods: {
    increment() {
      store.counter ++
    }
  }
```

正如您所想象的，存储将得到更新，但由于它没有意义告诉我们的组件值已经改变，我们的组件仍将显示值 0。

为了解决这个问题，我们可以实现类似于`pubsub`模式的东西，我在我的文章[写你自己的 JavaScript 状态管理库](https://medium.com/swlh/write-your-own-javascript-state-management-library-3687d3c09aae)中描述过，或者尝试在一些库的帮助下使用 observables，比如 [RxJS](https://rxjs-dev.firebaseapp.com/) 。Observables 似乎是一种更现代、更简洁的解决问题的方式，但在这两种情况下，我们都必须编写更多的代码或使用一些外部库，这可能不是更适合小型应用程序的解决方案。

因为我们已经在使用 Vue 来处理我们的 UI，所以我们可以利用它的`Vue.observable` API 函数来让我们的小商店具有反应性。如 [Vue 文档](https://vuejs.org/v2/api/#Vue-observable)所述:

> “Vue.observable 使一个对象具有反应性。在内部，Vue 在由`data`函数返回的对象上使用它。
> 
> 返回的对象可以直接在[渲染函数](https://vuejs.org/v2/guide/render-function.html)和[计算属性](https://vuejs.org/v2/guide/computed.html)中使用，变异时会触发相应的更新。对于简单的场景，它还可以用作最小的、跨组件的状态存储。"

这看起来很适合我们的案子。所以现在我们的商店看起来像这样:

```
// store.js
import Vue from 'vue';
const state = Vue.observable({ counter : 1})
export default state;
```

现在，当进行任何更改时，导入该存储的所有组件都将得到新值的通知。我发现使用这种方法的一个问题是，业务登录必须直接写在组件本身中，这使得更新和共享变得困难。为了解决这个问题，我们可以创建另一个文件，该文件利用存储并包含所有规则——或者因为我们的存储非常小，我们可以将它们写在那里作为命名导出:

通过这样做，我们可以更新我们的组件来导入它们，并添加按钮来实现我们的 UI 和商店状态之间的交互:

现在，我们有了一个反应性的、简单的商店，并且我们的组件从业务逻辑中分离出来。

这里用到的代码在 [CodeSandbox](https://codesandbox.io/s/vue-state-management-751pq?file=/src/App.vue) 上有，可以自己测试。

您可能认为这个项目有太多的代码，本地状态就足够了，我同意这一点。这个例子更适合我之前说过的，你必须在非父子关系之间共享状态，并且不想使用高阶组件或者在多个级别之间传递道具和事件的情况。这可以在瞬间将应用程序从简单变得过于复杂。

最后一点注意:这个例子并不意味着 Vuex 是一个发电站，或者是 Vuex 在所有项目上的替代品。对于需要强大功能的大型项目，您应该使用 Vuex 或任何其他类似的解决方案。

我希望你喜欢这篇文章。下一集见。