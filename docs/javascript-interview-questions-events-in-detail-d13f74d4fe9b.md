# JavaScript 面试问题:详细事件

> 原文：<https://betterprogramming.pub/javascript-interview-questions-events-in-detail-d13f74d4fe9b>

## 准备好下次面试官问你关于 JavaScript 事件的问题

![](img/7cae697131b7e933c0b14b5ddc84fa3f.png)

图片由 [Aranxa Esteve](https://unsplash.com/@aranxa_esteve?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

要得到一份前端开发人员的工作，我们需要搞定编码面试。

在本文中，我们将探讨在前端 JavaScript 中处理事件的问题。

# **JavaScript 中如何使用事件处理程序？**

事件是用户活动的结果，如点击链接或输入文本。当在元素中触发事件时，需要事件处理程序来运行代码。

事件处理程序被设置为对象的额外属性。例如，如果我们想处理一个按钮的事件，那么我们可以写:

```
const button = document.getElementById('button');
button.onclick = (event) => {
  //...
}
```

假设按钮的 ID 是`button`，我们可以通过为`onclick`属性设置一个事件处理函数来附加一个`onclick`监听器来处理点击事件。

`event`参数是一个拥有关于事件的各种信息的对象。

我们也可以使用`addEventListener`调用来做同样的事情:

```
const button = document.getElementById('button');
button.addEventListener('click', (event) => {
  //...
})
```

第一个参数具有事件名称。事件监听器位于`addEventListener`方法调用的第二个参数中。

# “`event.preventDefault()'`和“`event.stopPropagation()'`有什么区别？

`event.preventDefault()`用于停止触发事件发生的任何元素的默认行为。

如果它和一个`form`元素一起使用，那么我们阻止它提交。

如果它和一个`a`元素一起使用，那么`preventDefault()`会阻止它导航。

`event.stopProgation()`专门用于阻止事件的传播，在冒泡和捕获阶段阻止事件发生。

# 我们如何知道元素中是否使用了'`event.preventDefault()'`？

我们可以检查事件对象中的`event.defaultPrevented`属性。如果是`true`，那么`event.preventDefault()`被调用。

# 什么是“事件.目标”？

`event.target`是发生事件的元素或触发事件的元素。

例如，假设我们有以下 HTML:

```
<div id='div'>
  <button id='button'>
    Click Me
  </button>
</div>
```

和下面的 JavaScript 代码:

```
const button = document.getElementById('button');
button.onclick = (event) => {
  console.log(event.target.id);
}
```

当我们点击`Click Me`按钮时，我们将从`console.log`中得到`button`，因为它是点击事件的起源。

这表明`event.target`是事件被触发的元素。

如果我们为 div 添加一个事件处理程序，如下所示:

```
const button = document.getElementById('button');
const div = document.getElementById('div');
button.onclick = (event) => {
  console.log(event.target.id);
}div.onclick = (event) => {
  console.log(event.target.id);
}
```

我们还让`button`登录到那里，所以`event.target`肯定是点击事件起源的元素。

# 什么是“event.currentTarget”？

`event.currentTarget`是显式附加事件处理程序的元素。

例如，假设我们有以下 HTML:

```
<div id='div'>
  <button id='button'>
    Click Me
  </button>
</div>
```

这个 JavaScript:

```
const button = document.getElementById('button');
const div = document.getElementById('div');
button.onclick = (event) => {
  console.log(`button.onclick ${event.currentTarget.id}`);
}div.onclick = (event) => {
  console.log(`div.onclick ${event.currentTarget.id}`);
}
```

当我们单击`Click Me`按钮时，我们看到从`button.onclick`事件处理程序记录的`button.onclick button`和从`div.onclick`事件处理程序记录的`div.onclick div`。

因此，我们看到`event.currentTarget`是我们附加到事件处理程序的元素。

![](img/feb251f2a5d6e1eef95e2bbc187b38d7.png)

照片由[你好我是尼克·🇬🇧](https://unsplash.com/@helloimnik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 什么是活动委托？

*事件委托*是我们将事件监听器添加到父元素，而不是添加到子元素。

侦听器将触发子代上触发的任何元素，因为事件是从触发事件的元素冒泡出来的。

这很有用，因为我们只有一个附加到父元素的处理程序。

也不需要将处理程序从被移除的元素上解除绑定，并将其绑定到新元素上。

例如，如果我们有下面的 HTML …

```
<div id='div'>
  <button id='button-a'>
    Click Me
  </button>
  <button id='button-b'>
    Click Me
  </button>
</div>
```

…然后我们可以编写以下 JavaScript 代码来添加事件委托:

```
const div = document.getElementById('div');div.onclick = (event) => {
  if (event.target.matches("button#button-a")) {
    alert('Button A clicked');
  } else if (event.target.matches("button#button-b")) {
    alert('Button B clicked');
  }
}
```

在上面的代码中，通过 ID 获取`div`,然后为它附加一个 click-event 处理程序。

然后，在事件处理函数中，我们通过使用`matches`方法检查调用 click 事件的`event.target`。

如果点击了 ID 为`button-a`的按钮，那么我们会显示一个带有`Button A clicked`的警告框。

如果点击 ID 为`button-b`的按钮，我们会显示一个带有`Button B clicked`的警告框。

正如我们所看到的，我们只有一个事件处理器，但是我们可以处理里面所有按钮的点击事件。

# 文档- `Load`事件和文档`DOMContentLoaded`事件有什么区别？

当初始的 HTML 文档已经被完全加载和解析，而没有等待样式表、图像和子框架完成加载时，触发`DOMContentLoaded`事件。

`load`事件仅在 DOM 和所有相关资源和资产被加载后被触发。

# 什么是事件循环？

事件循环是一个单线程循环，它监视调用堆栈并检查任务队列中是否有要运行的代码。

如果调用堆栈为空，而任务队列中有回调函数，那么它们将从任务队列中出列，并通过将它们推送到调用堆栈来运行。

# 结论

在 JavaScript 中，我们通过将事件监听器附加到元素来处理事件。

我们还可以使用事件委托来避免将事件侦听器附加到每个元素上。它对处理动态元素事件也很有帮助，因为我们不必通过事件委托来附加和删除事件侦听器。

在 JavaScript 中，事件循环是一个单线程循环，它监视调用堆栈并运行那里的内容。如果调用堆栈中没有任何东西，那么任务队列中的任何东西都将被移出队列，并通过将其推送到调用堆栈来运行。