# 快速更改输入的数据

> 原文：<https://betterprogramming.pub/react-vs-vue-making-quick-changes-to-entered-data-337b9823ae6>

## 应该用 React 还是 Vue？

![](img/a8c73dbfd7d5b08dde9c9f99f939e6d0.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Carlos Muza](https://unsplash.com/@kmuza?utm_source=medium&utm_medium=referral) 拍摄的照片。

React 是最近几年最流行的前端库。Vue 是一个前端框架，最近在这方面做出了反应。

很难在这两种框架之间做出选择，因为它们各有利弊。当我们选择了一个，我们必须坚持几年。

在本文中，我们将了解在 React 或 Vue 应用程序中输入数据后，如何快速更改数据。我们要看的场景包括在变化时更新状态，转换成数字，以及修剪空白。

# 更改时更新状态

使用 React，我们还可以添加自己的事件处理程序，将输入值设置为状态值。

在大多数情况下，我们攻击一个`onChange`处理程序来完成这个任务。例如，我们编写以下内容:

在上面的代码中，我们在输入中附加了一个`onChange`处理程序来更新`name`状态。

然后我们在输入下方显示`name`。

使用 Vue，这也很容易，因为它有`lazy`修改器或`v-model`来在输入变化事件而不是输入事件时将输入值与模型同步。

例如，我们可以这样做(对于`index.html`和`index.js`):

我们只需将`.lazy`添加到`v-model`中，以在改变而不是输入时更新`name`。

# 自动将输入值转换为数字

Vue 的`v-model`有`.number`修饰符，可以将输入的任何内容转换成数字。

这很有用，因为即使输入类型是数字，输入的值也总是字符串。

我们可以将`.number`修饰符用于 Vue(用于`index.html`和`index.js`):

`age`自动转换成带有`.number`修饰符的数字。

使用 React，我们必须自己努力将输入的值转换成数字。

例如，我们可以将 React 中的示例编写如下:

我们添加了一元运算符`+`，在用`setAge`设置`age`之前，将`e.target.value`转换成一个数字。

这与我们的 Vue 示例非常相似。

![](img/9b656637416b208d16c254088eb0fd27.png)

[DAVIDCOHEN](https://unsplash.com/@dav1dcohen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

# 在输入时修剪输入值

Vue 有针对`v-model`指令的`.trim`修饰符，用于在输入值时从输入值中删除空白。

我们可以如下使用`trim`修改器(针对`index.html`和`index.js`):

我们只需将`.trim`修饰符添加到`v-model`指令中，从输入值的末尾修剪空白(以及两个单词之间的额外空白)。

如果两个单词之间有一个以上的空格，它会把空格减少到一个。

对于 React，我们必须自己完成。我们使用一些字符串方法来进行修剪。

例如，我们可以将上面 React 中的 Vue 示例编写如下:

在上面的代码中，我们有:

```
e.target.value.replace(/ +/g, " ").trim()
```

这调用了`replace`来将单词之间的多个空白字符整理成一个。然后我们调用`trim`从字符串的开头和结尾开始修剪空白。

如我们所见，这是我们可以通过 Vue 避免的工作。

# 裁决

与 React 相比，Vue 减少工作量的唯一情况是使用`v-model`指令的`.trim`修饰符自动修剪字尾和字间的空白。

除此之外，其他两种情况下的框架是相似的。