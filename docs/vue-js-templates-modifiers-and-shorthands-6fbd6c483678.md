# Vue.js 模板:修饰符和简写

> 原文：<https://betterprogramming.pub/vue-js-templates-modifiers-and-shorthands-6fbd6c483678>

## 我们看指令的修饰语和简写

![](img/d858cf0f8af9624c181dfaec51307748.png)

科林·梅纳德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 是一个易于使用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将看看指令的修饰符和一些有用的模板简写。

# 修饰语

修饰符用于以特殊的方式绑定指令。

## 事件修改器

例如，`v-on`指令的`.prevent`修饰符将自动对设置为值的事件处理函数运行`event.preventDefault`。

`.prevent`修改器可以如下使用:

```
<form v-on:submit.prevent="onSubmit"> ... </form>
```

然后，`event.preventDefault()`将在`onSubmit`处理程序运行时运行，然后`onSubmit`的其余代码运行。

其他事件修饰符包括:

*   `.stop`
*   `.capture`
*   `.self`
*   `.once`
*   `.passive`

`.stop`在其余事件处理程序代码运行之前运行`event.stopPropagation()`。

`.capture`让我们捕捉事件。也就是说，当我们在内部元素中运行事件处理程序时，相同的事件处理程序也会在外部元素中运行。

例如，如果我们在`src/index.js`中有以下内容:

以及`index.html`中的以下内容:

然后，当我们单击 *Click Me* 时，我们会看到“clicked”警告框，因为在父`div`元素上调用了`onClick`处理程序。

只有当`event.target`是元素本身时`.self`才会触发事件处理程序。

`.once`最多会触发一次事件处理程序。

例如，如果我们在`src/index.js`中有以下内容:

以及`index.html`中的以下内容:

那么即使我们多次点击“点击我”,我们也只会看到一次“点击”警告框。

`.passive`会将`addEventListener`的`passive`选项设置为`true`。`passive`设置为`true`意味着`preventDefault()`永远不会被调用。

它用于提高移动设备的性能。

## 模型修改器

`v-model`有修饰词。他们是:

*   `.lazy`
*   `.number`
*   `.trim`

`.lazy`修改器将在改变事件而不是输入事件之后使模型同步。

例如，当我们有`src/index.js`:

```
new Vue({
  el: "#app",
  data: { msg: "" }
});
```

并在`index.html`中写入以下内容:

然后，当我们将焦点从输入移开时，我们只看到呈现的`msg`。

`.number`修改器会自动将输入的内容转换成数字。

例如，如果我们在`src/index.js`中有以下内容:

```
new Vue({
  el: "#app",
  data: { num: 0 }
});
```

以及`index.html`中的以下内容:

然后我们会得到一个`num`的数字。

`.trim`修饰符将从输入的内容中删除空白。

我们可以如下使用它:

```
<input v-model.trim="msg" type="number" />
```

![](img/499e54b95d6781b0237c7e3b109e4842.png)

Roxanne desgagnes 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 人手不足

有些指令有简写。

## v 型装订

我们可以把`v-bind:`缩写成`:`。例如，我们可以重写:

```
<a v-bind:href="url">Link</a>
```

收件人:

```
<a :href="url">Link</a>
```

如果我们使用 Vue 2.6.0 或更高版本，我们还可以放入一个动态参数:

```
<a :[key]="url">Link</a>
```

## v-on

我们可以把`v-on:`缩短为`@`。

例如，我们可以缩短:

```
<a v-on:click="onClick">Click me</a>
```

收件人:

```
<a @click="onClick">Click me</a>
```

从 Vue 2.6.0 开始，我们还可以使用如下动态参数:

```
<a @[event]="onClick">Click me</a>
```

`:`和`@`是有效字符。而且，它们不会出现在最终的标记中。人手不足是完全可以选择的。但是当我们需要大量输入的时候，我们会很感激。

# 结论

一些指令有与之相关联的修饰符。

指令有多个修饰符来控制如何调用事件处理程序。

另外，`v-model`指令有一些修饰符，让我们可以自动将输入转换成数字，或者删除输入中的空白。

`v-on`和`v-bind`也有人手不足的时候。`v-on:`可简称为`@`,`v-bind:`可简称为`:`。

指令参数也适用于速记。