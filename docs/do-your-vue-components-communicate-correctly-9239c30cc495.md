# 您的 Vue 组件是否能正确通信？

> 原文：<https://betterprogramming.pub/do-your-vue-components-communicate-correctly-9239c30cc495>

## 七个图案，挑一个合适的

![](img/c06270c3b3f8b834f3bd359b0e66647a.png)

[刘汉宁·内巴霍](https://unsplash.com/@hannynaibaho?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

随着应用程序的成熟，组件之间的通信会越来越多，因此选择正确的通信模式至关重要。

选择正确的通信模式不仅简化了调试，而且有助于快速的特性开发，因为模式是重复的，代码库是可预测的。

在本文中，我们将介绍在组件之间传递信息时可以添加的七种通信模式。

# 道具向下，事件向上—父级到子级

支持下降-事件上升当然应该在列表的顶部，因为它是最常见的模式。

父级和子级之间的单向数据流是您的团队成员对数据如何在您的应用程序中流动达成共识的基础。

此外，仅在定义的地方改变数据*(也就是避免直接改变道具)确保了变化变得可预测和可预期。*

*一个典型的用例是呈现一个项目列表，并在项目点击时修改它。这里我们有一个用户列表，我们想在用户名被点击时修改列表:*

```
*// UserList -- Parent pass user prop down
<user v-for="user in users" :key="user.id" @change="updateUser" :user="user" />//User -- Child emits change event up
<div @click="$emit('change', user)"> {{user.name}} </div>*
```

# *插槽—父到子*

*另一个实现亲子沟通的强大方法是使用插槽，它暴露了几个要填充内容的出口。*

*这里我们定义了一个带有后备内容`Default Text`的出口/占位符。*

```
*// base-button
<button>
  <slot>
    Default Text
   </slot>
<button>*
```

*之后，我们通过提供所需的内容来消费组件:*

```
*// consuming component
<base-button>
  overriding text 
</base-button>*
```

*由于可以传递标记，使用插槽可以为您提供比 props 更高的灵活性，因此您可以这样做:*

```
*// consuming component
<base-button> 
  <icon name="save"></icon>
  <b>SAVE</b>  
</base-button>*
```

# *作用域插槽—子到父*

*作用域插槽能够中断单向数据流(向下传递属性)，而是将内容暴露给父对象。*

*当您在填充插槽时想要使用可插入插槽的组件中的一些数据或方法时，这尤其有用:*

```
*// User Component
<span>   
  <slot> {{ user.lastName }} </slot>
</span>*
```

*在消费时，我们可能希望显示来自`User`组件的附加信息——例如名字:*

```
*<user>
  {{ user.firstName }}  {{ user.lastName}}  // Won't Work
</user>*
```

*不幸的是，这是行不通的，因为对象用户还没有定义消费组件的范围。幸运的是，我们可以通过作用域插槽来实现:*

```
*<span>   
  <slot v-bind:user="user"> // expose user object to consumer
     {{ user.lastName }}   
   </slot> 
</span>*
```

*使用`v-bind:user`将用户暴露为老虎机道具后，我们可以在消费者中使用它:*

```
*<user> 
  <template v-slot:default=”slotProps”> 
   {{ slotProps.user.firstName }} {{ slotProps.user.lastName }}
  </template> 
</user>*
```

# *注入/提供-组件子树*

*随着应用程序的发展和构建更多可重用的组件，组件树层次结构会因为组件的嵌套而变得更深。*

*通过在父组件中使用`provide`和在其子组件中使用`inject`可以将道具传递给一个远的后代，而不管它在层次结构中有多深。*

*尽管这种模式在 Vue 社区中名声不佳，但它便于提供具有“全局”依赖性的组件子树(想想 React 中的上下文 API)，，所以它在插件中被大量使用。*

***注意:**记住提供的数据*不是*反应性的，除非你经过一个可观察的物体。*

*你可以以[这个模式](https://michaelnthiessen.com/provide-inject-not-dependency-injection)为契机，更深入的了解[反应性](https://medium.com/js-dojo/understand-vue-reactivity-implementation-step-by-step-599c3d51cd6c)和[插件](https://alligator.io/vuejs/creating-custom-plugins/)*

*[](https://vuejs.org/v2/api/#provide-inject) [## API — Vue.js

### Vue.config 是一个包含 Vue 全局配置的对象。您可以在…之前修改下面列出的属性

vuejs.org](https://vuejs.org/v2/api/#provide-inject)* 

# *事件总线—旁路通信*

*不同组件树中的组件可能想要通信。在这种情况下,`EventBus`模式很方便。*

*受网络中[总线拓扑](https://en.wikipedia.org/wiki/Bus_network)的启发，`EventBus`是一种创建发布者-订阅者模式的简单方便的方法，允许组件一起通信，而不管它们之间的关系如何*

*你需要做的就是创建一个 Vue 实例`EventBus`，并将其导入到所有依赖于总线的组件中。*

*稍后，任何组件都可以使用`EventBus.$emit(‘eventName’)`向`EventBus`发布新事件:*

```
*import EventBus from '...'//subscribe
mounted() { EventBus.$on(‘eventName’, () => { //… }) }//cleanup
beforeDestroy() { Eventbus.$off('eventName', () => {// ...})*
```

*这种通信方法虽然有效，但在应用程序扩展时会带来一些问题，比如名称空间、时间和开发工具支持。*

*最好使用状态管理解决方案，并且只在极少数情况下使用`EventBus`。*

# *门户—任何*

*虽然这不是 Vue.js 核心的一部分，但它是由 Vue.js 核心团队的成员 Linus Borg 构建的。*

*值得一提的是，它有利于处理流行的用例，比如模态/通知。*

> *Vuejs 的一个门户组件，用于在组件之外、文档中的任何地方呈现 DOM。*

*它的工作方式与插槽类似，主要区别在于实现方式，因为它不需要父/子关系:*

```
*<portal to="destination">
  <p>This slot content will be rendered wherever the <portal-target> with name 'destination'
    is  located.</p>
</portal><portal-target name="destination">
  <!--
  This component can be located anywhere in your App.
  The slot content of the above portal component will be rendered here.
  -->
</portal-target>*
```

# *国家管理—集中化*

*如前所述，`EventBus`可以走很长的路，但它最终会引入足够多的问题，你最好使用状态管理。*

*通过有一个集中的**单一的真理来源**、**、**所有组件访问这种反应性的全局状态。*

*首选方法是 Vuex，它带有开发工具支持和一个状态管理模式，由最佳实践、状态、突变、动作、getters 和所有这些可爱的东西强制执行！*

# *摘要*

*组件可以使用几种模式进行通信。在大多数情况下，所有这些模式都可以完成工作。*

*目标不仅是促进交流，而且减少调试时间，使修改代码库变得容易。*

*当你选择正确的模式时，这里有一些问题需要回答。*

*   *需要一起通信的组件之间的关系是什么？*
*   *谁拥有这些数据？*
*   *在这种交流中，时机有多重要？*