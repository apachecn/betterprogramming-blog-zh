# 12 面向专业开发人员的 Vue.js 最佳实践

> 原文：<https://betterprogramming.pub/12-vuejs-best-practices-for-pro-developers-28d1f629018c>

## 改进您的 Vue 游戏

![](img/34f722110ba4b01975a00cb7dada7956.png)

[随着 VueJS 继续变得越来越普遍](https://learnvue.co/2019/12/what-you-need-to-know-about-vue3-in-2020/)，出现了一些最佳实践并成为标准。

对于这篇文章，我从一些很棒的资源中收集了一些技巧。

希望这些最佳实践能帮助你写出更好的 Vue.js 代码。这不仅会让你所有的开发伙伴都喜欢你，而且你肯定会感谢自己让你的生活变得更轻松。

好了，介绍够了。让我们开始吧。

# 1.始终使用:v-for 内部的键

将 key 属性与`v-for` [指令](https://learnvue.co/2020/01/creating-your-first-vuejs-custom-directive/)一起使用有助于您的应用程序在您想要操作数据时保持不变并具有可预测性。

这是必要的，这样 Vue 就可以跟踪你的组件状态，并对你的不同元素有一个持续的引用。键非常有用的一个例子是当使用动画或 [Vue 过渡](https://learnvue.co/2020/01/how-you-can-use-vue-transitions-right-now)时。

如果没有键，Vue 将尽可能地提高 DOM 的效率。这可能意味着`v-for`中的元素可能出现无序，或者它们的行为不太可预测。如果我们对每个元素都有一个惟一的键引用，那么我们就可以更好地预测我们的 Vue 应用程序将如何处理 DOM 操作。

```
<!-- BAD -->
<div v-for='product in products'>  </div>

<!-- GOOD! -->
<div v-for='product in products' :key='product.id'>
```

# 2.在活动中使用烤肉串盒

当涉及到发射自定义事件时，最好使用 kebab case。这是因为在父组件中，我们使用相同的语法来监听该事件。

所以为了我们组件的一致性，也为了让你的代码更易读，坚持在两个地方都使用 kebab case。

```
this.$emit('close-window')// then in the parent<popup-window @close-window='handleEvent()' />
```

# 3.用 camelCase 声明道具，在模板中使用 Kebab Case

这种最佳实践只是简单地遵循每种语言的惯例。在 JavaScript 中，camelCase 是标准的，在 HTML 中，它是 kebab case。因此，我们相应地使用它们。

幸运的是，Vue.js 为我们在 kebab case 和 camelCase 之间转换，所以除了实际声明它们之外，我们不必担心任何事情。

```
 BAD!
<PopupWindow titleText='hello world' /> props: { 'title-text': String } GOOD!
<PopupWindow title-text='hello world' /> props: { titleText: String }
```

# 4.数据应该总是返回一个函数

当声明组件数据时，数据选项应该总是返回一个函数。如果没有，我们只是简单地返回一个对象，那么这个数据将在组件的所有实例中共享。

```
BAD!data: {
  name: 'My Window',
  articles: []
}
```

然而，大多数时候，目标是构建可重用的组件，所以我们希望每个组件返回一个唯一的对象。我们通过在函数中返回数据对象来实现这一点。

```
GOOD!data () {
  return {
    name: 'My Window',
    articles: []
  }
}
```

# 5.不要将 v-if 与 v-for 元素一起使用

为了过滤数组的元素，很容易想把`v-if`和`v-for`一起使用。

```
BAD!<div v-for='product in products' v-if='product.price < 500'>
```

这样做的问题是 Vue.js 优先考虑`v-for`指令，而不是`v-if`指令。所以在引擎盖下，它循环遍历每个元素，然后*检查`v-if`条件。*

```
this.products.map(function (product) {
  if (product.price < 500) {
    return product
  }
})
```

这意味着即使我们只想呈现列表中的一些元素，我们也必须遍历整个数组。

这不好。

更聪明的解决方案是迭代一个[计算属性](https://learnvue.co/2019/12/mastering-computed-properties-in-vuejs/)。上面的例子看起来像这样。

```
<div v-for='product in cheapProducts'>

computed: {
  cheapProducts: () => {
    return this.products.filter(function (product) {
      return product.price < 100
    })
  }
}
```

这有几个好处。

*   渲染的效率要高得多，因为我们不需要循环遍历每一个项目
*   只有当依赖关系改变时，过滤列表才会被重新评估
*   它有助于将我们的组件逻辑从模板中分离出来，使我们的组件更具可读性

# 6.用好的定义来验证你的道具

这可以说是最重要的最佳实践。

为什么重要？

嗯，基本上是把未来的你从现在的你中拯救出来。设计大型项目时，很容易忘记道具的确切格式、类型和其他惯例。

如果你在一个更大的开发团队中，你的同事不会读心术，所以让他们明白如何使用你的组件。

因此，省去了每个人不得不费力地跟踪组件以确定属性格式的麻烦，请只编写属性验证。

查看 Vue 文档中的这个例子。

```
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```

# 7.使用烤饼盒或烤肉串盒包装组件

组件的常见命名约定是使用 PascalCase 或 kebab case。无论你为你的项目选择哪一个，最重要的是你始终保持一致。

在我看来，PascalCase 的效果最好，因为它得到了大多数 IDE 自动完成功能的支持。

```
# BAD

mycomponent.vue
myComponent.vue
Mycomponent.vue

# GOOD

MyComponent.vue
```

# 8.基本组件应该相应地加上前缀

另一个命名惯例关注于命名基本组件——或者纯粹是表示性的组件，帮助在应用程序中建立通用的样式。

根据 Vue 样式指南，*基本组件*是仅包含以下内容的组件:

*   HTML 元素
*   附加基本组件
*   第三方用户界面组件

命名这些组件的最佳实践是给它们加上前缀`Base`、`V`或`App`。再说一次，只要你在整个项目中保持一致，使用其中任何一个都是可以的。

```
BaseButton.vue
BaseIcon.vue
BaseHeading.vue
```

这种命名约定的目的是让您的基本组件在文件系统中按字母顺序分组。此外，通过使用 webpack 导入功能，您可以搜索与您的命名约定模式匹配的组件，并将它们作为全局组件自动导入到您的 Vue 项目中。

# 9.声明和使用一次的组件应该有前缀“the”

与基本组件类似，*单实例组件*(每页使用一次，不接受任何道具)有自己的命名约定。

这些组件特定于你的应用程序，通常是页眉、侧边栏或页脚。

该组件应该只有一个活动实例。

```
TheHeader.vue
TheFooter.vue
TheSidebar.vue
ThePopup.vue
```

# 10.与你的指示速记保持一致

Vue 开发人员的一个常用技巧是使用简写指令。例如:

*   `@`是 `v-on`的简称
*   `:`是`v-bind`的简称
*   `#`是`[v-slot](https://learnvue.co/2019/12/using-component-slots-in-vuejs%e2%80%8a-%e2%80%8aan-overview/)`的简称

在你的 Vue 项目中使用这些人手很棒。但是为了在你的项目中创造某种惯例，你要么总是使用它们，要么**从不**使用它们。这将使你的项目更有凝聚力和可读性。

# 11.不要在“创建的”和“观察的”上调用方法

Vue 开发人员常犯的一个错误(也许只有我犯了)是他们不必要地调用了`created`和`watch`中的方法。这背后的思想是我们希望在组件初始化后立即运行`watch`钩子。

```
BAD!created: () {
  this.handleChange()
},
methods: {
  handleChange() {
    // stuff happens
  }
},
watch () {
  property() {
    this.handleChange()
  }
}
```

然而，Vue 对此有一个内置的解决方案。这是我们经常忘记的观察者的特性。

我们所要做的就是稍微重构一下我们的观察器，并声明两个属性:

1.  `handler (newVal, oldVal)` —这是我们的 watcher 方法本身。
2.  `immediate: true` —这使得我们的处理程序在实例创建时运行。

```
GOOD!methods: {
  handleChange() {
    // stuff happens
  }
},
watch () {
  property {
    immediate: true
    handler() {
      this.handleChange()
    }
  }
}
```

# 12.模板表达式应该只有基本的 JavaScript 表达式

想在模板中添加尽可能多的内联功能是很自然的。但是这使得我们的模板声明性更差，更加复杂。这意味着我们的模板变得非常混乱。

为此，让我们看看 Vue 风格指南中另一个规格化字符串的例子。看看这有多混乱。

```
BAD!{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```

基本上，我们希望模板中的所有内容都能直观地显示出来。为了保持这一点，我们应该将复杂的表达式重构为适当命名的组件选项。

分离出复杂表达式的另一个好处是，这意味着这些值可以重用。

```
GOOD!{{ normalizedFullName }}

// The complex expression has been moved to a computed property
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```

# 结论

现在你知道了。

这是 12 个最常见的最佳实践，它们会让你的 Vue 代码更易维护、更易读、更专业。

希望这些提示对你有用(因为它们绝对是我一直想要记住的东西)。

如果你想开始你的 Vue.js 开发，我整理了一个我一直在用的 VueJS 备忘单。它有我厌倦查找的重要信息，所以我把它编辑成一个整洁的三页小 PDF。