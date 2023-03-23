# 使用提供和注入设计 Vue3 插件

> 原文：<https://betterprogramming.pub/designing-vue3-plugins-using-provide-and-inject-47b586d9ce4>

## 非常适合构建插件或避免道具钻孔

![](img/d4d31555a1c43e0ee036ac776c434eca.png)

[阿尔瓦罗·皮诺](https://unsplash.com/@alvaropinot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

虽然不经常使用，但是您可以使用两个内置方法来实现依赖注入:`provide`和`inject`。

看看组合 API 文档，使用 provide 和 inject 的依赖注入在 [Vue 3.0](https://learnvue.co/2019/12/what-does-vuejs-3-0-mean-for-web-development/) 中会更常见。这主要是因为插件将不得不切换到使用这种模式，因为组合 API 对`this`引用的改变。

在这篇文章中，我们将会看到在 Vue3 中使用 provide 和 inject，以及如何使用它来构建 [VueJS 插件](https://learnvue.co/2020/01/how-to-make-your-first-vuejs-plugin/)。

让我们直接跳进来吧！

# 为什么 Vue3 插件的工作方式不同？

在 Vue2 中，大多数插件都在`this`上注入属性。例如，通过`this.$router`访问 Vue 路由器。

然而，`setup()`方法不再包含对`this`的相同引用。这一变化的主要原因是[增加了类型脚本支持](https://learnvue.co/2019/12/5-things-you-can-do-to-prepare-for-vue-3-0)。

*那么，我们现在如何访问我们的插件呢？*

幸运的是，我们可以使用`provide`和`inject`来帮助在我们的 Vue 应用中注入依赖关系。

`[Provide](https://medium.com/js-dojo/the-magic-of-provide-inject-a27041df33b2)``[inject](https://medium.com/js-dojo/the-magic-of-provide-inject-a27041df33b2)`用于依赖注入，使我们能够在我们的 Vue app 的根中提供一个插件，然后将其注入到一个子组件中。

在[组合 API](https://learnvue.co/2019/12/a-first-look-at-vue3-a-vue-composition-api-tutorial/) 中，两种方法都只能在`setup()`方法期间调用。

## 提供和注入的是什么？

好的，我们知道我们必须使用`provide`和 `inject`，但是这是如何工作的呢？

基本上，我们所需要的是某种识别我们的依赖性的键。出于我们的目的，我们将使用一个 [JavaScript 符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)。

然后，我们 provide 方法将我们的符号与某个值相关联，我们的 inject 方法将使用相同的符号检索该值。

如果我们看一个例子，会更有意义:

有了这个模式，我们实际上可以用 [Vue3](https://learnvue.co/2019/12/what-you-need-to-know-about-vue3-in-2020/) 完成一些很酷的技巧。

# 我们可以在我们的应用程序中提供全局依赖

如果我们想在全球范围内提供一些东西，我们可以在声明 Vue 应用实例的任何地方使用`app.provide`。然后，我们可以像刚才那样注射:

# 我们可以使用 Ref 来提供反应数据

如果我们希望将反应数据传递给子组件，这也非常方便。我们所要做的就是使用`ref()`将一个反应属性传递给我们的 provide 方法:

我们如何对插件使用`provide`和`inject`？

设计一个插件实际上非常类似于我们刚刚看到的简单的提供和注入示例。

然而，我们想使用一个`Composition`函数，而不是提供一个奇异值。这是 Vue3 的巨大优势之一——能够根据功能组织和提取代码。

因为我们的代码*无论如何都应该用一个有组织的组合函数来编写，我们只需要创建这些提供和注入方法和 BAM——我们有一个插件。*

让我们快速看一下 [Vue3 组合 API](https://learnvue.co/2020/01/4-vue3-composition-api-tips-you-should-know/) 文档给出的假设插件:

然后，我们的实际组件会像这样使用它:

正如你所看到的，在一些根组件中，我们提供了我们的插件并传递给它一个复合函数。然后，无论我们想在哪里使用它，我们都必须将它注入到组件中。

组件不应该真的进行`provide` / `inject`调用，而是应该调用插件公开的`provideStore` / `useStore`方法。

## 那么我还能用我的旧插件吗？

简而言之:是的。

长回答:看你什么意思了。

由于组合 API 是纯粹的附加 API，你可以继续使用选项 API，并且对之前的`this`有相同的引用，所有的旧插件都将同样工作。

然而，向前看，跳到 Vue3 并利用它的所有特性绝对是值得的。

本质上，只要你想坚持 Vue2 options API，你的插件也会一样工作。但是大多数维护良好的插件和库应该会增加对 Vue3 的支持。

# 结论

`provide` / `inject`的正确使用，绝对是 Vue 开发中比较高级的话题。

虽然大多数典型的应用程序不会使用这些概念，但如果你对开发插件很认真，Vue3 组合 API 的变化意味着你必须使用`provide` / `inject`。

如果你想要更多的信息，查看[组合 API 文档](https://vue-composition-api-rfc.netlify.com/)。

编码快乐！

如果你有兴趣了解更多关于 VueJS 的知识，点击这里获得一份免费的 Vue 备忘单，这是每个 Vue 开发者都需要知道的基本知识。