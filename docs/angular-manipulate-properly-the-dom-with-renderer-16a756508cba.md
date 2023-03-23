# Angular:停止用 ElementRef 操作 DOM

> 原文：<https://betterprogramming.pub/angular-manipulate-properly-the-dom-with-renderer-16a756508cba>

![](img/4737daa11ef3f272bd2e152f6b1387dc.png)

我需要和你分享这个故事。最近在 Angular 里发现了`Renderer`的威力。

(`Renderer`已被弃用，所以使用`Renderer2`；不过，在本文中我将只使用`Renderer`。)

用 Angular 操作 DOM 很容易。我们可以通过使用`@angular/core`提供的`ElementRef`直接访问该元素。

如果您通过使用一个指令来操作它(常见用例)——大多数人很可能像这样直接使用`nativeElement`:

它将正确地改变元素的颜色。很多教程演示了`nativeElement`的用法，但是这样安全吗？不，不是真的。

## 为什么这么重要？

根据角度文件`ElementRef` *:*

> *当需要直接访问 DOM 时，使用这个 API 作为最后的手段。允许直接访问 DOM 会使您的应用程序更容易受到 XSS 攻击* ***。*** *仔细检查你的代码中* `[*ElementRef*](https://angular.io/api/core/ElementRef)` *的任何使用。请改用 Angular 提供的模板和数据绑定。或者，你可以看看* `[*Renderer*](https://angular.io/api/core/Renderer)` *，它提供了即使不支持直接访问本地元素也可以安全使用的 API。*
> 
> *依赖直接 DOM 访问会在应用程序和渲染层之间产生紧密耦合，这使得将两者分开并将应用程序部署到 web worker 变得不可能。*

不太好。但是，有一种替代方法叫做`Renderer`。

## 操纵 DOM 的渲染器

看看这个:

 [## 角度文档渲染器 2

### 渲染器 2 API

angular.io](https://angular.io/api/core/Renderer2) 

有很多方法可以操作 DOM。在这个用例中，我们需要改变一个 CSS 属性，颜色。根据 API，我们需要这样使用`setStyle`:

果然奏效！`Renderer`对于常见用例非常有用，对于复杂的 DOM 操作也非常有用。

要定位特定元素，您可能需要使用`ViewChild`或`ContentChild`。如果这样做，请参阅本文:

[](https://netbasal.com/understanding-viewchildren-contentchildren-and-querylist-in-angular-896b0c689f6e) [## 了解 Angular 中的 ViewChildren、ContentChildren 和 QueryList

### 有时父组件需要访问其子组件。让我们看看如何用 Angular 处理这个问题。

netbasal.com](https://netbasal.com/understanding-viewchildren-contentchildren-and-querylist-in-angular-896b0c689f6e) 

总之，停止使用`ElementRef`，使用`Renderer`！