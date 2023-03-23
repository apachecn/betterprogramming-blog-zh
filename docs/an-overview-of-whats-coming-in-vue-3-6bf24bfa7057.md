# Vue 3 即将推出的内容概述

> 原文：<https://betterprogramming.pub/an-overview-of-whats-coming-in-vue-3-6bf24bfa7057>

## Vue 3 是一个重要的版本——让我们看看接下来会发生什么

![](img/524d920ec9907a516dd21f8d034af46f.png)

伊恩·施耐德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Vue 的一个主要版本将在今年发布，在接下来的日子里，我们可以期待一个更快、更小、更易维护、更易用的 Vue 版本。虽然 Vue 3 没有固定的日期，但路线图显示，发布计划在 2020 年第二季度，新版本正在测试阶段。所以我们来看看 Vue 3 的一些特性和变化。

# 组合 API

以前，Vue 中的代码组织是使用选项来完成的。选项很棒，但是当试图匹配或访问 Vue 逻辑时，它有编译器缺陷。所以在 Vue 3 中，组合 API 将作为内置特性引入，这是处理代码组织的一个更好的解决方案。

它还允许您在 Vue 组件中更自由、更灵活地重用纯 JS 函数，并且还会减少代码行。

使用组合 API，我们可以通过将特定特性的代码放在一起来组织代码，这在 Options API 中是不可能的。此外，它使得代码共享更加容易。我们可以为某个特定的特性提取代码，并在多个地方使用它。下面给出了组合 API 的一个示例实现:

```
<script>
export default {
         setup() {
           return {
             getData(), 
             saveData(), 
             printData()
           }
         }
       } 
function getData() { } 
function saveData() { } 
function printData() { }
</script>
```

但是如果你认为你不需要复合 API，Vue 3 仍然会支持选项 API。但是我认为同时使用这两种 API 会使您的开发生活更加容易。

# 传送(传送门)

![](img/61fec6485bfe02b49c09c60715c2018a.png)

照片由[伊万·阿列克西奇](https://unsplash.com/@ivalex?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

transport 是一个特性，允许你将一个组件中的一部分代码呈现到另一个 DOM 树中的另一个组件中。

这个特性以前被称为门户，在 Vue 2 中有一个第三方库。在 Vue 3 中，门户特性是内置的，并被命名为 Teleport。顾名思义，Vue 3 包含一个名为`<Teleport>`的标签，这个标签里面的任何代码片段都将准备好被传送到某个地方。

# 焦虑

暂停是一个在主组件获取数据之前呈现默认组件的特性。将引入悬念来等待嵌套树中的嵌套异步依赖，它将很好地处理异步组件。

```
<template>
  <Suspense>
    <template #default>
      <div v-for="student in studentList" :key="student.id">
        <article>
          <h2>{{ student.name}}</h2>
          <p>{{ student.address}}</p>
        </article>
      </div>
    </template>
    <template #fallback>
      Contacts loading...
    </template>
  </Suspense>
</template>
<script>
import axios from 'axios'
export default {
  async setup() {
    let studentList= await axios
      .get('<api call>')
      .then(response => {
        console.log(response)
        return response.data
      })
    return {
      studentList
    }
  }
}
</script>
```

# 多个根元素

片段将在 Vue 3 中引入，我认为这是非常需要的，因为 Vue 模板只能有一个标签。例如，以下代码将在 Vue 2 中给出一个错误:

```
<template> 
 <div>Welcome to</div> 
 <div>Vue JS</div> 
</template>
```

但是在 Vue 的新版本中，上面的限制被取消了，上面的代码可以成功编译。

# 多个虚拟模型

一个`v-model`用于双向绑定，主要用于表单元素和定制组件。在 Vue 2 中，一个组件中只允许有一个`v-model`的限制。但是 Vue 3 将允许我们将任意数量的`v-model`绑定到我们的定制组件上。

# 全球安装

![](img/092e3ed9703fc8f76c6b9adb001d2829.png)

照片由 [Fernando @cferdo](https://unsplash.com/@cferdo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Vue 3 中，全局 Vue 实例不再用于安装插件和其他库。取而代之的是一种叫做`createApp`的方法。

```
import { createApp } from 'vue'
import App from './App.vue'
const myApp = createApp(App)
myApp.use(/* plugin name */)
myApp.use(/* plugin name */)
myApp.mount('#app')
```

有了这个特性，我们可以在一个特定的实例而不是全局对象上安装这些插件，它将保护 Vue 应用免受第三方库的影响，第三方库可能会覆盖或更改全局实例。

# 类型脚本支持

Vue 在 2.0 版本后期开始支持 TypeScript，在 Vue 3 中也会继续支持。您将能够在 Vue 3 中使用最新的 TypeScript 版本生成新项目。

# 表演

Vue 3 的一个主要目的是提高速度。据消息人士透露，Vue 3 相比之前的版本将会有显著的速度提升。它的更新性能将提高 2 倍，服务器端渲染速度将提高 3 倍。

组件初始化也将更有效，甚至有编译器通知的快速执行路径。此外，为了提高性能，虚拟 DOM 已经过重写。

# 结论

请注意，上述功能只是我们在 Vue 3 中可以期待的一个概述。虽然看起来所有的特性都已经完成了，但是在发布之前还是有一点改变的可能。

我认为 Vue 3 正在推出一些急需的功能来与竞争对手竞争。