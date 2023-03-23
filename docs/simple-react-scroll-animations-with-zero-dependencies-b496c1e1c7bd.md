# 零依赖性的简单反应滚动动画

> 原文：<https://betterprogramming.pub/simple-react-scroll-animations-with-zero-dependencies-b496c1e1c7bd>

## React 钩子、CSS 和交叉点观察器 API

![](img/44a146310ea8eccca9aa8afa6e084cbc.png)

照片由[蒂姆·施密德鲍尔](https://unsplash.com/@timschmidbauer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/scroll-laptop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

小册子网站中最常见的效果之一是元素进入用户屏幕后不久，在滚动过程中出现淡入或浮入动画。

为了实现这些效果，开发人员通常会使用动画库。但是对于大多数网站来说，不需要这种额外的开销！

使用 CSS 和本地交叉点观察器 API，我们可以创建一个简单且高效的方法，用于在用户向下滚动页面时动画化元素。在本文中，我们将看到如何用 React 实现这一点。本文中的示例也使用了 TypeScript。

# 步骤 1:检测元素何时出现在屏幕上

每当我们的一个 React 组件被安装时，我们可以创建一个新的`IntersectionObserver`来观察它何时与浏览器视口相交。

在 React 中引用元素的首选方式是通过 refs，所以我们将使用 ref 作为第一个参数。第二个参数允许我们设置偏移量，以便动画触发到窗口边缘的固定距离——而`"0px"`正好在边缘上。

确保在返回的清理函数中运行`unobserve`,这样一旦组件被卸载，我们就不会继续观察它。

# 步骤 2:创建容器组件

对于一个简单的淡入淡出效果，我们可以简单地根据元素是否在屏幕上来改变`opacity`和`translate` CSS 属性。

然后，在我们的代码中，我们可以简单地调用:

```
<AnimateIn>
  <h1>Hello World</h1>
</AnimateIn>
```

我们可以就此打住，但是通过一些额外的调整，我们可以将这种模式变成更加可定制和可重用的东西。

# 步骤 3:创建动画库

如果我们想要扩展可能的动画范围，我们需要使我们的组件更通用一点，允许开发人员为`from`和`to`状态传递他们自己的 CSS。

现在，如果我们想创建一个简单的动画库，我们可以使用我们的`AnimateIn`组件轻松地构建它。例如:

作为个人偏好，我喜欢将这些组件分组为一个`Animate`对象的一部分，然后导出:

```
export const Animate = {
  FadeIn,
  FadeUp,
  ScaleIn,
};
```

我们可以这样称呼一个动画:

```
<Animate.ScaleIn>
  <h1>Hello World</h1>
</Animate.ScaleIn>
```

我们现在可以访问与大多数小册子网站相当的动画，而不需要额外的依赖！

对于一个工作示例，请查看下面的代码笔:

*感谢阅读！敬请关注更多内容。*