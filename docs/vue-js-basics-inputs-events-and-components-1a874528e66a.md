# Vue.js 基础:输入、事件和组件

> 原文：<https://betterprogramming.pub/vue-js-basics-inputs-events-and-components-1a874528e66a>

## Vue.js 入门

![](img/ea4365448ee8b87e90fa410fc7afd6c1.png)

[约书亚·希伯特](https://unsplash.com/@joshnh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Vue.js 是一个简单易用的 web 应用框架，我们可以用它来开发交互式前端应用。

在本文中，我们将研究如何从用户那里获得用户输入并创建 Vue.js 组件。

# v-on 指令

指令让我们处理各种来源触发事件，包括用户交互。

我们可以用它来处理用户点击，如下所示。首先，在`index.html`中，我们写道:

然后在`src/index.js`中，我们写道:

然后当我们点击按钮时，我们会看到`Clicked`而不是`Hello`。

上面的代码通过我们添加按钮和`p`元素来工作。`p`元素有`message`变量，它从`index.js`的`data.message`值中获取数据，并将其插入到我们的 HTML 中。

我们的按钮添加了`v-on:click`指令。当我们点击按钮时，来自`index.js`中`methods`属性的`onClick`方法被调用。

然后`this.message`被设置为`'Clicked'`，这是`message`的新值。`data.message`为`index.html`中`message`的默认值，`this.message`为`message`的当前值。

所有的 DOM 操作都由 Vue 处理。我们不需要写任何代码来完成它。

# 垂直模型指令

指令让我们获得输入数据，并在其他地方自动使用它。它用于在 Vue 实例和我们的 HTML 之间绑定输入数据。

例如，我们可以在`index.html`中编写以下内容:

然后在`index.js`中，我们写道:

然后我们应该看到`input`字段已经预先填充了值`Hello`。此外，我们插入了单词`Hello`作为我们的`p`元素的内容。然后当我们输入一些东西时，`p`标签的内容会自动更新为我们输入的内容。

这就是`v-model`指令的神奇之处，它接受我们的输入值，将其设置为`this.message`，然后在`p`标签的模板元素中呈现`message`，以便将其插入到`p`标签中。

![](img/7b37b44b6771c74e5434b16437e46fc5.png)

[Yoal Desurmont](https://unsplash.com/@yoal_des?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 用组件组装零件

为了制作更复杂的应用程序，我们可以将一个组件嵌套在另一个组件中。这让我们可以在不同的地方重用组件。

我们可以用`Vue.component`方法定义一个新的组件。它将组件名称的字符串作为第一个参数，将一个对象的属性`template`和`props`作为第二个参数。

例如，我们可以如下创建一个组件。

在`index.js`中，我们添加:

上面的代码有一个`props`属性，我们可以用它来访问从另一个组件传入的任何对象。`template`有要渲染的模板。

`persons`有我们想要渲染的数据。

然后在`index.html`中，我们添加:

HTML 文件有`v-for`来遍历`data.persons`中的`persons`条目。

我们还需要`v-bind:person`将我们的`person`对象从`v-for`传递给我们的`person-name`组件。这就是为什么我们在:

```
Vue.component("person-name", {
  props: ["person"],
  template: "<li>{{ person.name }}</li>"
});
```

一旦`person`被传递到`person-name`组件中，我们就可以在模板中访问它的`name`属性。

然后我们得到:

```
Mary
Jane
Joe
```

显示在我们的浏览器屏幕上。

# 与自定义元素的关系

Vue.js 组件看起来类似于定制元素，但它们并不相同。Vue.js 组件松散地基于定制元素，如 slots 和`is`属性。

但是，Vue.js 组件也有通过 props 和自定义事件通信的跨组件数据流。它还集成了可用于定制元素的构建工具。

Vue.js 组件可以构建到定制元素中，并在定制元素注册表中注册它们自己。

# 结论

我们可以用`v-on`指令处理事件。例如，`v-on:click`让我们通过设置我们自己的事件处理器方法作为值来处理用户点击某物的情况。

`v-model`让我们接受用户输入并在其他地方使用该值。

我们可以创建新的组件并将它们嵌套在模板中，这样我们就可以在其他地方重用它们。为此，我们可以调用`Vue.component`方法。