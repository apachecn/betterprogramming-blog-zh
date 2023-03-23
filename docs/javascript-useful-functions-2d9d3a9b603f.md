# 有用的 JavaScript 函数

> 原文：<https://betterprogramming.pub/javascript-useful-functions-2d9d3a9b603f>

## 统一事件、去抖动和检测移动浏览器

![](img/8a27e4431d9e7acb0f6b5ee67fa2402a.png)

照片由 [Sam Moqadam](https://unsplash.com/@sammoqadam?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/free?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我每天分享[一招](https://medium.com/@david.dalbusco/one-trick-a-day-d-34-469a0336a07e)直到原定的瑞士新冠肺炎隔离期结束的日期，2020 年 4 月 19 日。离第一个里程碑还有七天。希望更好的日子就在前面。

我们在 [DeckDeckGo](https://deckdeckgo.com) 的多个应用程序和 web 组件中使用了几个 JavaScript 函数。

也许这些对你的项目也有用？

# 统一鼠标和触摸事件

如果你通过鼠标或触摸设备来实现与用户交互有关的功能，那么它们的结果很有可能是相同的。

例如，演示者可以使用我们的遥控器绘制演示文稿。为此，我们使用`canvas`并附加如下事件:

```
const canvas = document.querySelector('canvas');

canvas.addEventListener('mousedown', this.startEvent);
canvas.addEventListener('touchstart', this.startEvent);
```

您可能注意到了，`mouse`和`touch`事件由相同的函数处理，这很好。但是，不幸的是，如果你想访问职位信息，例如，`clientX`或`clientY`，没有一点逻辑，他们就不能算出来。

的确，`touch`定位在对象的根处不可用，而是在数组`changedTouches`中。

这就是为什么我们使用一个名为`unifyEvents`的函数来获得定位，而不考虑设备。

```
export function unifyEvent($event) {
  return $event.changedTouches ? $event.changedTouches[0] : $event;
}
```

可以按如下方式使用:

```
function startEvent($event) {
  // TouchEvent and MouseEvent unified
  console.log(unifyEvent($event).clientX);
}
```

# 去抖动

我在[之前的文章](https://medium.com/better-programming/debounce-with-vanilla-javascript-or-rxjs-70f29c58ca80)中讨论过这个主题*用 JavaScript* 去抖动，但是如果你想在没有任何依赖的情况下给你的应用添加这样一个特性，这里有一个函数可以做到。

# 用户代理人

今年早些时候，谷歌宣布决定在其 Chrome 浏览器中取消对用户代理字符串的支持。

因此，应该明智地使用下面的方法，要知道它们将来会被取代。也就是说，这里有几个方便的函数可以帮助检测关于浏览器或设备类型的信息。

## isMobile

[检测移动浏览器](http://detectmobilebrowsers.com)提供了一个开源的移动设备列表，可用于测试我们的导航器，以检测用户是否正在移动设备上浏览或使用应用程序。

## isIOS

为了检测我们的用户是否正在使用苹果移动设备，我们可以根据关键字`iPad|iPhone|iPod`测试导航器。

## isIPad

同样，我们可以将查询减少到`iPad`来猜测用户是否在 iPad 上。

## isFirefox

同样，您可以检测客户端是否正在使用特定的浏览器。比如火狐。

# 全屏

我们的演示文稿可以在独立模式或全屏模式下编辑和浏览，这就是为什么我们必须检测这样的状态。

为此，我们将`window.innerHeight`与`screen.height`进行比较，如果两者相等，则浏览器处于全屏模式。

# 从 HTML 字符串中删除属性

假设您的 DOM 包含一个元素，您希望借助 JavaScript 将其解析为一个`string`。

```
<div contentEditable="true" style="color: #ff0000">Red</div>const div = document.querySelector('div').outerHTML;
```

出于某种原因，您可能无法接触或克隆 DOM 元素，但无论如何会对从`string`值中移除一个属性感兴趣。

分别，你会对这样一个结果感兴趣:

```
<div style="color: #ff0000">Red</div>
```

要清除或删除字符串中的属性，可以使用以下方便的 RegExp:

基本上，它搜索所有可能的属性并创建一个新的包含选择前后内容的`string`。

我最喜欢的技巧是。

# 摘要

DeckDeckGo 是开源的，你可以在我们的 [GitHub repo](https://github.com/deckgo/deckdeckgo/tree/master/utils) 或者他们相关的 [npm 包](https://www.npmjs.com/search?q=%40deckdeckgo)中找到上面的函数。

感谢阅读，

大卫