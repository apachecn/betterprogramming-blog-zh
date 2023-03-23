# 如何在 React 中以编程方式使用媒体查询

> 原文：<https://betterprogramming.pub/how-to-use-media-queries-programmatically-in-react-4d6562c3bc97>

## 使用断点挂钩#扩展

![](img/37a5710f9e73a6b97b843b4b64eaafa0.png)

艾伦·毕晓普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 React.js 中获取媒体查询断点有时可以非常方便地在 React.js 中操作我们的 UI，而不是编写 CSS 然后为其编写媒体查询。

在我的[上一篇](https://medium.com/better-programming/usebreakpoint-hook-get-media-query-breakpoints-in-react-3f1779b73568)文章中，我讨论了当设备宽度改变时，获得这些断点作为`xs`、`sm`、`md`和`lg`。在那里，我使用`resize`事件讨论了`useBreakpoint`钩子的实现。

在这篇文章中，我们将看看其他比使用`resize`事件更合法的实现方式。

# 匹配媒体

为了获得媒体查询断点，我们需要开始检验我们的媒体，或者换句话说，开始检验我们的设备；无论是小屏设备还是大屏设备。

我们如何做到这一点？

为此，JavaScript 在`window`对象上为我们提供了一个`matchMedia`方法。

```
window.matchMedia('(max-width: 100px)')
```

就像这样，我们将会看到我们的文档最大宽度是 100 像素。

`matchMedia`方法返回一个`MediaQueryList`对象，代表指定媒体查询的解析结果。

基本上，这个`MediaQueryList`对象是我们实际用来确定`document`何时匹配媒体查询，或者监视`document`以检测它何时匹配或停止匹配媒体查询。

```
const mql = window.matchMedia(*mediaQueryString*);
```

# 使用 MediaQueryList

使用`MediaQueryList`时会出现两种情况:

1.  当我们希望即时检查我们的文档是否匹配媒体查询，或者我们希望只检查一次，可能是在我们的主要 React 组件安装之后。
2.  当我们不断检查我们的文档是否与媒体查询相匹配时。这种情况可能发生在当我们的设备的视窗宽度或高度可以改变时，比如说当我们将手机方向从纵向改为横向时。

对于第一种情况，当我们需要即时检查时，我们将简单地使用`MediaQueryList` `matches`属性。这是一个布尔属性，如果`document`当前匹配媒体查询列表，则返回`true`，否则返回`false`。

```
mql.matches //returns true or false
```

对于第二种情况，我们需要观察`MediaQueryList`对象上的`change`事件。为了开始监听`change`事件，`MediaQueryList`提供了一个名为`addListener()`的方法。

同样，为了停止监听，我们有`removeListener()`。

```
const listenerFunc = () => {
// Listening logic goes here};mql.addListener(listenerFunc);
```

这个侦听器函数不会在我们调整文档宽度、改变方向或其他事情时每次都运行，但它只会在两种情况下被触发。当我们的文档开始匹配`mediaQueryString`或停止匹配`mediaQueryString` *时。*

# 我们要做的是

我们将从自定义钩子的用户那里得到一个对象，其中每个属性值都是一个媒体查询字符串。

```
const queries = {xs: '(max-width: 320px)', //query for xs devicessm: '(max-width: 720px)',md: '(max-width: 1024px)'}
```

并且，作为我们的输出，我们还期待一个对象，它具有在`queries`对象中给定的所有键，其中属性/键的每个值都是布尔值。`true`如果文档匹配查询，则`false`否则。

例如，假设我们有一个 640 像素的视窗宽度，那么上面的`queries`的输出将是:

```
{xs: false,sm: true,md: true}
// here our matches are sm and md
```

要获得上述输出，我们将遵循以下步骤:

*   我们将遍历`queries`对象的每个属性，并为每个查询调用`matchMedia()`(下面是代码 1)。
*   每次调用`matchMedia`进行查询都会返回一个`MediaQueryList`对象，我们将把`MediaQueryList`对象存储到一个新的对象中，对应于同一个键(代码一)。
*   同样为了获得当前的`document`匹配查询，以便我们知道启动时我们的设备类型是什么，我们将在每个`MediaQueryList`对象(代码一)上使用`matches`属性。

代码 1

*   现在，我们还需要处理我们在`queries`对象中的查询发生变化的情况，即当我们的文档开始或停止匹配查询时。为此，我们将使用`addListener()`(代码二)监听每个`MediaQueryList`项目上的`change`事件。
*   我们将定义一个处理函数，在这个函数中，我们将遍历`queries`对象的每个键，并保存每个`MediaQueryList`对象的`matches`属性的结果(代码二)。

注意:每当我们的文档开始或停止匹配`queries`对象中的任何查询时，都会调用这个处理函数。

代码 2

# React 中的实现

检查媒体查询是我们代码中的一个副作用，所以我们将把这个逻辑放入一个`useEffect`钩子中，并且我们还必须检查我们的浏览器是否支持`matchMedia`。

除此之外，我们还必须维护一个状态，该状态将显示所有媒体查询的结果，无论这些结果是否与我们的`document`匹配。

代码 3

现在我们可以像使用其他钩子一样使用这个钩子了。

```
import useBreakpoint from './useBreakpointext'const queries = {
  xs: '(max-width: 320px)',
  md: '(max-width: 720px)',
  lg: '(max-width: '1024px)',
}const App = () => {
  const matchPoints = useBreakpoint(queries);
  //Rest Code goes here
} 
```

# 是一种高效的方式吗？

不，你们中的一些人可能已经明白了，因为如果我们必须在成千上万个组件中使用`useBreakpoint`钩子，那么我们必须一次又一次地调用`useBreakpoint`。

这导致注册一个`change`事件监听器，如果我们有几个挂载的组件，并且它们都使用`useBreakpoint`钩子，那么这个监听器的效率会非常低。

# 那么什么是有效的方法呢？

在这里，React 上下文提供了帮助。

因此，我们将使用上下文，然后使用我们的自定义钩子，而不是直接使用`useBreakpoint`。

我们将使用`React.createContext(*defaltValue)*` *创建一个上下文。*

```
const defaultValue = {};
const BreakpointContext = createContext(defaultValue);
```

我们必须将我们想要访问上下文值的代码包装到一个`Provider`中，在我们的例子中是`BreakpointContext.Provider`，然后，为了访问那个值，我们将使用 React 钩子`useContext`。

但是，我们不会直接使用`BreakpointContext.Provider`，而是创建一个包装器。

代码 4

`useBreakpoint`是一个函数，我们从中返回上下文值，这基本上是我们匹配的查询结果。

我们将把我们的应用程序包装在`BreakpointProvider`中，并传递一个查询 prop 给它，而`useBreakpoint`只能在被包装在`BreakpointProvider`中的组件中使用。

现在，让我们把所有的部分放在一起。

代码 5

# 使用

使用`BreakpointProvider`:

代码 6

使用`useBreakpoint`:

代码 7

注意:我们不仅可以使用此方法检查`max-width`或`min-width`，还可以检查方向和许多其他媒体查询。

# 参考

[](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries) [## 以编程方式测试媒体查询

### DOM 提供了可以通过 MediaQueryList 接口以编程方式测试媒体查询结果的特性…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries)