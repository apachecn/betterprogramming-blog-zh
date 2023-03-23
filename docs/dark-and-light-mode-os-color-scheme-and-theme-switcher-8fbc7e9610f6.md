# 明暗模式:一个操作系统配色方案和主题切换器

> 原文：<https://betterprogramming.pub/dark-and-light-mode-os-color-scheme-and-theme-switcher-8fbc7e9610f6>

## 如何将继承 OS 配色方案的主题切换器作为默认状态添加到应用程序中

![](img/d2b03205c7d2f2632eb01a5766849adc.png)

[真诚媒体](https://unsplash.com/@sincerelymedia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

有很多关于“黑暗模式”主题的好教程，但是在我看来，很少有一个教程涵盖了在实际应用中实现这种模式的两个主要目标。它们分别让用户能够在主题之间切换，同时默认继承平台提供的操作系统配色方案，特别是谷歌和苹果都开始在产品中推出这种模式。

我写这篇文章来分享我们在贡献者的帮助下实现的解决方案，在我们用于演示的开源 web 编辑器 [DeckDeckGo](https://deckdeckgo.com) 中。

# 入门指南

为了实现我们的解决方案，我们依赖于两个令人惊叹的软件:

*   idb-keyval :一个超级简单的、小型的、基于承诺的 keyval 存储，用 IndexedDB 实现，我们用它来保存用户选择。
*   RxJS:JavaScript 的反应式扩展库，我们用它来存储和传播内存中表示主题的状态。

我们将使用一个运行时值来切换主题。因此，也有可能使用 [React Redux](https://react-redux.js.org) 存储状态来实现这样的解决方案。不要犹豫，平我，如果你想得到这样的例子；在我目前正在开发的另一个新应用程序中，我也准备了一个。

# 单身服务

为了处理我们的主题状态，我们创建了一个普通的单例服务。如果您正在使用像 [Angular](https://angular.io) 这样的框架，那么像往常一样创建一个根范围的服务，或者如果您喜欢使用函数和静态值，那么就这样做。重要的是，只在内存中加载和保存一个代表所有应用程序的应用主题的状态。

在我们的服务中，我们声明了一个布尔值`darkTheme`，它被设置为`true`，意味着“黑暗主题激活”，而`false`表示“光明主题激活”如果有两个以上的主题可用，我们可以使用一个`enum`。这种扩展的可能性是这种实现的一个很酷的资产。

注意，我们使用了一个`ReplaySubject<1>`，因为我们想在内存中保存一个没有值的状态，直到我们决定应用什么主题。

# 观察和切换状态

现在我们的服务已经准备好了，我们必须公开应用程序的状态，并为用户提供切换主题的方法。我们还必须保存选择，以便下次应用程序重新启动时加载它。

# 初始偏好

在启动时，我们必须按照以下步骤加载主题:

*   用户是否已经有了偏好的主题集？
*   我们是否无法读取此信息(是否出现错误)？
*   是否应该使用平台提供的 OS 默认配色？

回答完这些问题后，我们创建一个实现这个逻辑的函数，并使用我们之前的`switch`方法来传播主题。

# 将主题应用于 DOM

我们特意没有在服务中应用“图形化”主题。因此，我们现在必须决定在哪里对 DOM 进行修改。

在我们的项目中，当我们用[模板](https://stenciljs.com) web 组件开发应用程序时，我们已经开始在根组件(`app-root.tsx)`中初始化。

此外，我们正在观察同一个组件的变化，因为在应用程序关闭之前它不会被破坏。这样，在发出每个新状态时，我们修改 DOM，更准确地说是修改`body`元素，以应用或删除 CSS 类名(在我们的例子中是`dark`)。

# 主题切换器

到目前为止，我们的应用程序只能处理操作系统首选的配色方案，并没有给用户切换主题的能力。这就是为什么我们将创建一个新的组件，例如，它公开了一个 [Ionic toggler](https://ionicframework.com/docs/api/toggle) 来在亮暗模式之间切换。

# 式样

你可以使用 CSS 变量或者仅仅是属性来设计主题。在这两种情况下，重要的是根据我们刚刚在`body`元素上分别设置的类名`dark`来应用主题。

如果你使用 Ionic，你将能够在你的`variables.scss` ( `:root`没有选择器作为灯光主题)中设计一个特定的背景和文本颜色:

或者另一个普通 HTML/CSS 的例子:

# 一起

我们的项目 DeckDeckGo 是开源的。所以，我用我们遥控器的一些 GitHub 参考来和大家分享一下上面的实现:

*   单身服务:[主题服务](https://github.com/deckgo/deckdeckgo/blob/master/remote/src/app/services/theme/theme.service.tsx)。分时（同 timesharing）
*   初始化并观察: [app-root.tsx](https://github.com/deckgo/deckdeckgo/blob/master/remote/src/app/app-root.tsx)
*   切换器的例子: [app-general-settings.tsx](https://github.com/deckgo/deckdeckgo/blob/master/remote/src/app/components/settings/app-general-settings/app-general-settings.tsx)
*   样式: [variables.scss](https://github.com/deckgo/deckdeckgo/blob/master/remote/src/global/theme/variables.scss)

![](img/c3070f0461c6278f440d4981ce3379db.png)

在明暗主题之间切换

# 蛋糕上的樱桃

也许您实际上对将代码分成不同的层并不感兴趣，而只是希望有一个单独的 web 组件来处理所有的事情。

你猜怎么着？我们也有这样一个组件，一个我们在给开发者的文档[中实现的组件。](https://docs.deckdeckgo.com)

# 信用

如上所述，我们在贡献者的帮助下开发了这个功能，更准确地说是在格兰特·赫尔曼的帮助下。他甚至帮助我们在三个不同的应用程序中实现了它。

肯定不是所有英雄都穿披风；谢谢你格兰特。