# 👆React 中的指针事件—原因、方式和内容

> 原文：<https://betterprogramming.pub/pointer-events-with-react-the-why-how-what-617a5b51dbb2>

![](img/d24913b5808e7a3a7bdd024a79b0fcca.png)

# 我们来谈谈事件，更具体地说是指针事件。

## 什么是指针事件？

> [指针事件](https://developers.google.com/web/updates/2016/11/nic55#pointer-events)提供了处理所有输入事件的统一方式。

在过去美好的日子里，我们只有一个鼠标，监听事件很简单。web 内容假设用户的定点设备是鼠标。

如今，我们有许多与鼠标无关的设备，比如带触摸屏的手机或笔。

![](img/32b5884667777e730f61511d933815ac.png)![](img/74f041c9d7e901c035d66a038f1b2b2a.png)

如果用户没有鼠标，我们如何监听事件？

## [触摸事件](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events)来救援！

![](img/92f251c34fdda48d0c906d5d8fc2d0d4.png)

[https://www.w3.org/TR/touch-events/](https://www.w3.org/TR/touch-events/)

![](img/2f094c3b2548c2dc0c4bb076a43fda0d.png)

[https://caniuse.com/#search=touch](https://caniuse.com/#search=touch)

触摸事件的接口是相对低级的 API，可用于支持特定于应用的多点触摸交互，例如双指手势。

当手指(或触笔)第一次触摸接触面时，多点触摸交互开始。其他手指可以随后触摸该表面，并且可选地在触摸表面上移动。当手指从表面移开时，交互结束。在该交互期间，应用在开始、移动和结束阶段接收触摸事件。

![](img/ffc4ec352ecb97dc74b555b9e7dd950a.png)

[https://developer . Mozilla . org/en-US/docs/Web/API/Pointer _ events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)

添加触摸事件监听器相当简单，但是您必须同时支持`touch`和`mouse`:

![](img/efd2fd9efd62007393fdf4e8d08c87a4.png)

[http://jsbin.com/libayejike/edit?js,console,output](http://jsbin.com/libayejike/edit?js,console,output)

在你的手机上尝试这个例子，并尝试在桌面版本上使用它来分割屏幕，然后检查`console.log` 输出。

![](img/bf6d078203d83061be30c484314bc258.png)

测试 iPhone 上的触摸事件

支持`touch`和`mouse`事件会变得非常臃肿和难以维护，因为你必须为不同的设备编写事件代码。

## 指针事件到救援！

![](img/2dcad7850acc643e2b731cafb7ba26a1.png)

[https://w3c.github.io/pointerevents/](https://w3c.github.io/pointerevents/)

![](img/823e4ffc4a70c41bc2531b4a0f2b72bb.png)

看看 jQuery 团队的这个令人惊叹的多填充符,它支持大多数浏览器上的指针事件

还记得上面的例子吗，我们有两个事件监听器——一个用于鼠标，一个用于触摸？

好吧，我们可以简单地将其中一个改为`pointermove`并删除其余的，而不是编写两个事件监听器。

**pointermove** 支持触摸和鼠标

![](img/0c182aaad9c080d258022fc1b3f3d006.png)

[http://jsbin.com/xexotimade/edit?js,console,output](http://jsbin.com/xexotimade/edit?js,console,output)

![](img/d3f84a79317ba1647639e16330243dd1.png)

[https://github.com/jquery/PEP/blob/master/README.md](https://github.com/jquery/PEP/blob/master/README.md)

今天浏览器能做的事情太神奇了！但愿有一天我们能在浏览器中拥有完全原生的体验。

![](img/325f5185cc9eaa992f2509711233e1b7.png)

[https://developer . Mozilla . org/en-US/docs/Web/API/Pointer _ events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)

![](img/1265ad672ce1860d831722a1fca2c5eb.png)

React 最近推出了 16.4 版本，支持原生指针事件。

![](img/fe2ed0d78bab4d6263d382c826af92f3.png)

让我们创建一个快速反应项目，并尝试这些新的漂亮的事件。更新到最新的`[create-react-version](https://www.npmjs.com/package/create-react-app)`，启动项目。

![](img/e34c0666a93fddf4d302733573fb3cab.png)![](img/81dc512f176ab453c4905af354bd12ed.png)

由于指针事件支持触摸事件，我们也想在手机上测试它。用 via `on your network: http://xxx.xxx.x.xx:3000/`网址打开手机上的应用。

![](img/445405b160432ba2e29ee8e8e51b6495.png)

用手机打开 react 应用程序

我们首先创建同一个圆，并声明一个名为`hasCapture`的状态:

![](img/7c934f2a4649f5f04fa88e32aca8ab06.png)![](img/13b844d2c9131eaf529b1a927718ad7f.png)![](img/2e4bf4461b1687c715cad2ac41eac87f.png)

接下来，让我们添加`onPointerEnter`和`onPointerLeave`——当你将鼠标悬停在元素上时，听起来就是这样。

![](img/b8106e954789a5b542fceb4018b20310.png)![](img/5c95d998a56aeed6d45e74e473e24979.png)

**on pointer 输入**和 **onPointerLeave**

但是`onPointerEnter`和`onMouseEnter`有什么区别呢？

![](img/352a2cd7829722ed049ac6d6449d2524.png)

来源:[https://mobi forge . com/design-development/html 5-pointer-events-API-combining-touch-mouse-and-pen](https://mobiforge.com/design-development/html5-pointer-events-api-combining-touch-mouse-and-pen)

简而言之，通过使用指针事件，我们支持更多的设备。这可能是未来的活动方式。

如果你想让你的 JavaScript 能力更上一层楼，我推荐阅读“ [*你不知道的 JS*](https://amzn.to/2LSDpG6) ”系列丛书。

## 感谢阅读，保持敬畏！❤