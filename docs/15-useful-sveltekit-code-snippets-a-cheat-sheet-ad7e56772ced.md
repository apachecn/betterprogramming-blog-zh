# 15 个有用的 SvelteKit 代码片段——一个备忘单

> 原文：<https://betterprogramming.pub/15-useful-sveltekit-code-snippets-a-cheat-sheet-ad7e56772ced>

## 使用 Svelte 和 Svelte-Kit 时遇到的有用功能的集合

![](img/5370387577ce22697a8056a4425f2748.png)

照片由[卢卡·布拉沃](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 1.延迟组件直到 Dom 加载完毕

我们通常希望在关注视频等资源密集型项目之前加载页面。这里我们使用了一个承诺，在加载那些组件之前，检查页面是否准备好了。

```
let domReady = (function () {
   return new Promise( function(resolve) {
      document.addEventListener('readystatechange', function() {    
          if(document.readyState != 'loading' && document.readyState 
              !='interactive') resolve();
   })})})()
```

现在，我们可以在添加组件时使用异步 await 条件:

```
{#await domReady}
   <MyComponent />
{/await}
```

# 2.包和脚本的外部加载

通常，我们可能希望从 CDN 加载一个库来进行测试，或者减少整个包的大小或编译时间。这里我们使用`svelte:head`选项来完成。

```
<svelte:head>
   <script defer src='http://...loading1.js'></script>
   <script defer src='http://...loading2.js'></script>
</svelte:head>
```

当然，如果我们愿意，我们也可以使用动态的`import(...)`或者甚至普通的 JavaScript 方法:

```
function loadScript(src) { let script = document.createElement('script')
   script.src = src; 
   script.async = false;
   document.body.append(script);}loadScript("loading1.js");
loadScript("loading2.js");
```

# 3.使用窗口函数处理事件

有时，我们可能希望阅读发生在窗口上的“事件”。一个简单的方法是将它们绑定到`svelte:window`:

```
<svelte:window on:keydown={keyed}/>
```

并使用回调函数将它们视为正常。

```
function keyed(event) {
  key = event.key;
  keyCode = event.keyCode; if (key === ' ' ) alert('you pressed space')
 }
```

# 4.跨多个路线/组件的共享变量

要跨多个组件更新一个变量，我们可以使用 svelte `stores`。为此，我们定义了一个共享文件(如`sharedvalues.js`)和变量:

```
import { writable } from 'svelte/store';export const contact = writable({name:'Dan', age:undefined});
```

然后在我们的每个简单脚本中，我们可以导入变量并更新它们。

```
import {contact} from './sharedvalues.js';console.log('hello', $contact.name)
```

我们还可以用以下内容更新这些内容:

`$contact = {name:’Dan’, 101}`或`contact.set({name:’Dan’, age:101})`

# 5.在来自另一个组件/页面的变量更新时自动触发回调函数

如果我们希望每次存储值改变时都运行一次更新，我们只需为它订阅一个回调函数。只要两条路线/组件共享同一个苗条商店，改变的位置就无关紧要。

```
function onChange(){
   console.log('hello', $contact.name)
   }contact.subscribe(onChange)
```

# 6.事件调度程序

svelt 组件之间的另一种通信方式是事件调度程序。在这里，我们创建一个自定义事件名称，并发送一个包含我们信息的对象。

```
import { createEventDispatcher } from 'svelte'; const dispatch = createEventDispatcher();dispatch('myEventName', {name:'Dan'});
```

然后，我们可以将其“绑定”到以下形式的组件:

```
<script>function onChange(event){
   console.log('hello', event.detail.name)
   }</script> 
<Component ...  on:myEventName={onChange} />
```

# 7.从应用程序级别读取“上下文”值

还有另一种方式来阅读信息，而不是传递信息，这就是使用苗条的上下文。如果您希望在脚本的应用程序级别定义一组配置参数，然后每个子组件都可以访问这些参数，那么这是非常有用的。

```
//App.svelte import {setContext} from 'svelte'; setContext('user', {name:Dan} );
```

我们可以从子组件中读取以下内容:

```
import {getContext} from 'svelte'; const user = getContext('user');
```

# 8.硬编码响应

在某些情况下，我们希望将事件绑定到对窗口对象的更改。这里我们使用了`svelte:window`对象并将一个变量绑定到它的宽度上。

```
<script>
 let w
</script><svelte:window bind:innerWidth={w}/><OneThirdComponent style={"width:{w/3}px"} />
```

# 9.滚动讲故事

以类似的方式，我们可以将我们的滚动位置绑定到我们的脚本，并触发对我们网站的更新:

```
<svelte:window bind:scrollY={y}/>{#if y > 100}{#each layers as layer}
  <parallaxComponent
   style="transform: translate(0,{-y * layer / (layers.length -  1)}px)" >
 {/each}{/if}
```

# 10.全局样式

svelte 的一个很好的特性是样式是有范围的，但是，当你想改变一个外部导入的组件时，这会引起问题。

解决方案是定义一个全局样式，它会影响与查询参数匹配的每个组件。

```
<style>
   :global(.myComponentClass){
      display:flex
    }
</style>
```

# 11.呈现 HTML

我们还能够直接呈现 HTML 字符串，而不需要将它们附加到现有的 DOM 元素中。

```
<script>
   var htmlString = `<img src="${img.src}"/>`
</script>{@html htmlstring}
```

# 12.时间

当创建一个组件时，我们可以在其中嵌套其他组件/ HTML 代码。这是使用插槽完成的。

```
*//Component.svelte*<div> <slot>Default content</slot> </div> 
```

然后，放置在组件的开始和结束标记之间的任何内容都会替换“默认内容”位置。

```
<Component>   
   <p>My super cool changed content</p> 
</Component>
```

如果需要多次替换，也可以命名插槽。

# 13.绑定组件

我们还可以绑定一个组件，而不需要设置 id 或可查询的特性。这是通过使用其自身(或此)引用来完成的，如下所示:

```
<script>
   let component;
</script><svelte:component this={component} />
```

这种方法的好处是，我们可以通过使用选择工具来动态地改变我们正在查看的组件。

# 14.简易动画

最后，我们可以使用 svelte 中的运动和放松库轻松地对值进行补间并向我们的网站添加动画。

# 15.类指令

在 UI 开发中，基于条件(例如`class="{status === 'loaded' ? 'loadedclass':''}"`)添加一个类是如此常见，以至于 Svlete 已经添加了自己的 class 指令。为了更清楚地处理条件类，我们现在可以在组件初始化中指定它们:

```
<Component class:loadedclass={status === loaded} />
```

# 摘要

我发现的一些有用的苗条技巧的集合。虽然这个列表可能会随着我的继续而更新，但是请随意在评论中添加任何您认为有用的内容。