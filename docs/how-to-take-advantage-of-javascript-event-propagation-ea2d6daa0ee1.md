# 如何利用 JavaScript 事件传播

> 原文：<https://betterprogramming.pub/how-to-take-advantage-of-javascript-event-propagation-ea2d6daa0ee1>

## 这种方法将改变您编写事件的方式

![](img/de93956fc3eaf37b4190e53dbc4170f0.png)

伊丽莎白在 [Unsplash](https://unsplash.com/s/photos/bubbling?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上探索

我相信每个开发人员一生中都至少使用过一次 jQuery，并且注意到将事件附加到元素上非常容易。

这是因为与`addEventListener`相比，提供的语法非常简单，而且很容易听到多个元素而不需要遍历它们。

# 听不止一种元素

假设我们想用`.accordion-link`来监听手风琴元素的点击，以显示或隐藏内容。

使用 jQuery，不管是一个元素还是几个元素，我们都将编写相同的代码块。

```
$('.accordion-link').click(function (event) {
    // Add some logic...
});
```

然而， [Vanilla JS](http://vanilla-js.com/) `addEventListener()`方法只能附加到一个元素上。因此，我们可以为每个元素添加一个单独的事件侦听器，或者更好的方法是，遍历所有元素，如下所示:

```
const accordionLinks = document.querySelectorAll('.accordion-link');for (let i = 0; i < elements.length; i++) {
    elements[i].addEventListener('click', function (event) {
        // Add some logic...
    }
}
```

上面的代码有几个问题:

*   移除已注册的事件将会很棘手，因为您需要再次遍历它们。
*   它不适用于动态呈现的元素，因为我们需要对以后添加到 DOM 中的每个元素重复这个过程。

那么，更好的处理方式是什么呢？

# 事件授权方法

JavaScript 事件使用一种叫做*事件冒泡*的东西，每当用户与嵌套元素交互时就会发生这种情况，并且事件通过所有祖先元素向上传播(“冒泡”)。

事件委托通过将事件监听器附加到`document`元素来利用这种传播。然后我们可以检查被点击的元素是否有我们关心的选择器。

```
document.addEventListener('click', function (event) {
    if (event.target.classList.contains('accordion-link')) {
        // Add some logic...
    }
}, false);
```

`event.target`总是被点击的元素。

这种方法的另一个好处是，您可以动态地向 DOM 添加元素，而不必添加额外的事件侦听器。

祝编码愉快！