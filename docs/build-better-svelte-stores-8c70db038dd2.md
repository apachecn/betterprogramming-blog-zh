# 建造更好的苗条商店

> 原文：<https://betterprogramming.pub/build-better-svelte-stores-8c70db038dd2>

## 利用 Svelte 的内置商店

![](img/866b0045408a1ecfc3a483651080050a.png)

照片由[你好我是尼克🎞](https://unsplash.com/@helloimnik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/build?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Svelte 是构建用户界面的一种全新方法。

尽管 React 和 Vue 等传统框架在浏览器中完成大部分工作，但 Svelte 将这些工作转变为编译步骤，在构建应用程序时进行。

这意味着您的包将没有依赖项，并且会变得更小。除此之外，Svelte 的编译器会生成高度优化的代码，这意味着与 React、Vue 和 Angular 相比，你的应用程序可能会有更好的整体性能。

如果你没有听说过它们，Rich Harris 的视频“[反思反应性](https://www.youtube.com/watch?v=AdNJ3fydeao)”和“[‘少写多做’的回归](https://www.youtube.com/watch?v=BzX4aTRPzno)”可能是一个很好的开始。如果你对学习感兴趣，你可以从这本优秀的[教程](https://svelte.dev/tutorial/basics)开始。

Svelte 提供的开箱即用的特性之一是`store`，这是一种在组件之间共享数据的方式，无需使用道具，也不会陷入臭名昭著的*渲染道具地狱。*

Svelte 的团队在提供一种非常简单和容易的方法来实现这一点方面做得很好。有两种`store`类型:`writable`和`readable`。第一个可从外部更新，而另一个不可。大多数情况下，你最终会使用`writable`类型，如下例所示。该代码创建一个初始值等于零的新商店。

```
// file store.js
import { writable } from 'svelte/store'
const store = writable(0)
export default store;
```

这个商店可以进口和使用任何苗条的组件。

我们可以通过使用`subscribe`方法和`set`或`update`各自的方法来监听状态变化。

为了`subscribe`，我们提供了一个回调函数，该函数接收新的存储状态值，该值可用于更新组件的局部变量。`set`方法用传递给它的参数覆盖存储状态值。`update`方法接收一个回调函数作为参数。回调将现有的存储值作为其参数，并返回要设置到存储区的新值。

您可以在下面的代码中看到这三个方法的运行:

```
// file App.svelte
<script>
import { onDestroy, onMount } from 'svelte';
import store from './store.js'
let counter = 0;
let unsubscribe;
onMount(() => unsubscribe = store.subscribe(state => (counter = state)));
onDestroy(() => {if (unsubscribe) unsubscribe()})
const reset = () => (store.set(0));
const increment = () => store.update(state => state + 1)
</script>
<main>
    <span>The counter value is {counter}</span>
    <hr />
    <button on:click={increment}>Increment</button>
    <button on:click={reset}>Reset</button>
</main>
```

在`script`标签中，首先我们导入先前创建的商店。然后，我们使用两个 Svelte 的生命周期事件(`onMount`和`onDestroy`)来订阅商店并用其值更新计数器，并在组件被销毁时取消订阅。我们还定义了两个方法— `reset`，它将值设置回零，以及`increment`，它将存储的值加 1。

在 HTML 部分，我们得到了一个显示计数器值(`{counter}`)的 span 和两个触发我们在`script`标签中创建的方法的按钮。

它当然非常容易理解和使用，但是，正如我们从代码中看到的，所有的业务逻辑都直接写在组件中，这使得它很难与应用程序可能具有的其他组件共享。

为了解决这个问题，我们可以利用 Svelte 团队提供的优秀文档，特别是“[商店合同](https://svelte.dev/docs#Store_contract)”部分。根据定义，惟一的要求是商店必须有一个返回`unsubscribe`方法的`subscribe`方法。我们已经在 Svelte 的`store`中实现了这一点，所以我们可以在我们的自定义商店中使用它，如下面的代码所示:

```
// file counter-store.js
import { writable } from 'svelte/store';
const store = writable(0);
const counterStore = {
  subscribe: store.subscribe,
  set: store.set,
  update: store.update
};///
export default counterStore;
```

我们的自定义存储将像 Svelte 的`writable`存储一样工作，因为它只是返回一个转发`subscribe`、`set`和`update`方法的对象。但是为了做得更好并从我们的组件中移除业务逻辑，我们可以在商店内部实现`reset`和`increment`方法:

```
// file counter-store.js
import { writable } from 'svelte/store';
const store = writable(0);
const counterStore = {
  subscribe: store.subscribe,
  set: store.set,
  update: store.update,
  reset: store.set(0),
  increment: store.update(state => state + 1)
};
export default counterStore;
```

这样，我们的组件可以被重构和简化:

```
// file App.svelte
<script>
import { onDestroy, onMount } from 'svelte';
import store from './counter-store.js'
let counter = 0;
let unsubscribe;
onMount(() => unsubscribe = store.subscribe(state => (counter = state)));
onDestroy(() => unsubcribe())
const reset = () => store.reset();
const increment = () => store.increment();
</script>
<main>
    <span>The counter value is {counter}</span>
    <hr />
    <button on:click={increment}>Increment</button>
    <button on:click={reset}>Reset</button>
</main>
```

由于我们将业务逻辑放在商店内部，所以最好不要转发`set`和`update`方法，以避免组件干扰商店数据。因此，我们最终的商店代码如下所示:

```
// file counter-store.js
import { writable } from 'svelte/store';
const store = writable(0);
const counterStore = {
  subscribe: store.subscribe,
  reset: store.set(0),
  increment: store.update(state => state + 1)
};
export default counterStore;
```

通过这样做，任何其他需要与商店数据交互的组件都可以通过`reset`和`increment`方法，而不用担心它们背后的逻辑。

这里提供的代码的一个工作示例可以在这个代码沙箱的[中找到，供你使用，在我的 GitHub 账户库](https://codesandbox.io/s/medium-article-svelte-store-32cy4) [svelte-createstore，](https://github.com/n0n3br/svelte-createstore)你可以找到这个代码的一个更高级的版本，支持同步/异步动作和模块。

下次见。