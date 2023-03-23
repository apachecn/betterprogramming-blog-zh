# 在 Kotlin 中构建订阅感知流

> 原文：<https://betterprogramming.pub/subscription-aware-flows-in-kotlin-aab4fb026e56>

## 订阅感知流可以允许开发者在现代反应式编程中无缝地使用带有注册回调的遗留 API

![](img/d128993f1d960c2af9f43d3ed059d028.png)

一段时间以前，Android 开发者开始推广一种[生命周期感知组件](https://developer.android.com/topic/libraries/architecture/lifecycle)模式——基于活动或片段的当前生命周期状态自动调整其行为的组件。我们希望组件已经意识到这一点，而不是明确地告诉组件它处于这种或那种状态。

在本文中，我将演示如何构建订阅感知组件的类似模式——类似于生命周期感知组件的组件可以根据它们的订阅状态做出决策。

# 例子

获取某些组件更新的传统命令式方法如下所示:

我在这里使用 Android[shared preferences](https://developer.android.com/reference/android/content/SharedPreferences)作为例子，但是原则上这里没有什么是 Android 特有的:在普通的 JVM 世界中也有类似的**`registerListener`/`unregisterListener`API。**

**随着代码的发展，它们给开发人员带来了很多麻烦。我们必须小心内存泄漏:也许`registerListener(listener)`会存储一个对监听器的引用，因此也是对定义它的类的引用。也许我们会忘记在某个地方注销它，或者如果没有，也许我们的同事会忘记这样做。**

**有些问题可以通过引入流程来自然解决:**

**所以现在，我们可以告诉我们所有的同事只使用`sharedPreferencesFlow`，忘记`register` / `unregister`回调。我们[反转了控件](https://en.wikipedia.org/wiki/Inversion_of_control)，我们可以不再关心谁订阅了`sharedPreferencesFlow`以及订阅是如何被处理的:很难让它泄漏内存。**

**但是，**现在我们在范围**上有了一个问题:不是让每个开发人员决定何时开始和停止更新数据(这在计算上可能有点昂贵)，我们现在为每个人做出决定，并且我们尽可能在最大范围内更新流程，以便每个人都得到保证的更新。**

# **解决方案:订阅感知流程**

**幸运的是，有一个名为`[subscriptionCount](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html)`的`[MutableSharedFlow](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-shared-flow/subscription-count.html)`字段。它代表许多订阅者，它本身是一个我们可以订阅的 StateFlow。**

**该字段在将[冷流](https://elizarov.medium.com/cold-flows-hot-channels-d74769805f9)转换为[热流](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow#sharein)的`[shareIn](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/share-in.html)`方法下使用，用于定义开始行为策略:是否`SharedFlow`应该工作`[Eagerly](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-sharing-started/-companion/-eagerly.html)`(从声明的那一刻开始并且永不停止)`[Lazily](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-sharing-started/-companion/-lazily.html)`(当某人开始订阅并且永远不停止)，或者`[WhileSubscribed](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-while-subscribed.html)`。但是在我们的例子中，我们必须手动使用它，因为我们正在调用遗留的`register` / `unregister`回调:**

**瞧啊。我们有一个改变 SharedPreferences 的流程(以地图的形式呈现)。应用程序中的任何组件都可以简单地订阅它，当有任何东西订阅它时，更新就会被交付。，之后，资源将会自动释放。**

**回到 Android 世界，我们可以在一个可组合的:**

**而且一旦`SomeComposable`停止渲染，`unregisterOnSharedPreferenceChangeListener`就会自动发生。轻松点。**

**关于代码的几点说明:**

*   **`applicationScope` —这是一个比片段、活动或视图模型更长久的范围。尽管不鼓励使用[global scope](https://developer.android.com/kotlin/coroutines/coroutines-best-practices#global-scope)，但是创建自己的将贯穿整个应用程序生命周期的协同作用域并没有什么错。您可以在测试中轻松地覆盖或控制这个范围。**
*   **[协程名称(标签)](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-coroutine-name/) —这只是出于礼貌，以便当您想知道在任何给定时刻正在执行什么协程，或者给定挂起函数的协程上下文是什么时，这个名称会在调试过程中对您有很大帮助。**
*   **[匕首/刀柄](https://dagger.dev/hilt/)此处隐含使用。我将让读者来实现缺少的部分:-)**

# **结论**

**将遗留代码与新的模式和方法结合起来通常是一个挑战，但是订阅感知流允许我们在注册回调的遗留 API 和 Kotlin 流之间架起一座桥梁。这种模式不仅有助于这个遗留`SharedPreferences`代码的例子，而且几乎有助于任何使用注册回调方法的代码。**

**感谢您的阅读！**