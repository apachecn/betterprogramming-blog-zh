# 使用断点挂钩—在 React 中获取媒体查询断点

> 原文：<https://betterprogramming.pub/usebreakpoint-hook-get-media-query-breakpoints-in-react-3f1779b73568>

## 创建一个自定义挂钩来获取窗口宽度或断点

![](img/431fc62096e50a5b84c2402a375e3026.png)

米凯尔·克里斯滕森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

您可以使用媒体查询根据 CSS 中的视区宽度轻松选择 UI 设计，但有时您需要获得窗口宽度或断点，以便根据 React 中的视区宽度调整 UI。

为了在 React 中实现这一点，我们将创建一个定制钩子`useBreakpoint`，它将根据窗口宽度给出`xs`、`sm`、`md`和`lg`。

# 我们需要测量什么以及何时测量？

为了获得断点，我们需要测量浏览器的宽度，并在`window`对象上获得该宽度。`window`对象有两种宽度:`outerWidth`和`innerWidth`，我们将只使用`innerWidth`。

现在我们有了需要度量的东西，但是我们什么时候需要度量它。每当我们调整窗口大小时，我们测量`window.innerWidth`。

```
function calcInnerWidth() {
  /* Get window inner width here */}window.addEventListener('resize', calcInnerWidth)
```

因此，每当我们调整窗口大小时，`resize`事件被触发，我们计算`innerWidth`，但是实现这样的东西有一个小问题，你们中的一些人可能已经发现了。

每当我们稍微调整窗口大小时，回调函数`calcInnerWidth`就会被触发，也就是说，如果我们调整窗口大小时，比如说 100 毫秒，它会多次执行回调函数。

因此，为了克服这一点，我们将以给定的时间间隔调用回调函数。

对于节流功能，我们将使用`[lodash.throttle](https://lodash.com/docs)`包。

```
import throttle from 'lodash.throttle';/------- Other Code Goes Here ------/function calcInnerWidth() {
  /* Get window inner width here */}window.addEventListener('resize', throttle(calcInnerWidth, 200))
```

现在，`calcInnerWidth`函数最多每 200 毫秒调用一次。当我们从这个回调函数内部更新我们的状态时，这将派上用场。

# 计算断点

现在我们有了需要测量的内容，当我们需要测量时，让我们根据断点获取设备类型。

*   如果宽度小于 320px，那么那个设备就是特小，用`xs`表示。
*   如果宽度等于或大于 320px，小于 720px，那么那个设备小，用`sm`表示。
*   如果宽度等于或大于 720px，小于 1024px，那么那个设备是中型的，用`md`表示。
*   如果宽度等于或大于 1024px，则该设备较大，用`lg`表示。

代码 1

# 使用断点挂钩

现在我们有了所有需要的细节，让我们连接 React 中的所有部分，得到一个完整的`useBreakpoint`定制钩子。

首先，我们将使用`useState`钩子创建一个状态，它将存储我们当前的设备类型。

```
const [brkPnt, setBrkPnt] = useState(() =>  getDeviceConfig(window.innerWidth))
```

因此，当这个钩子被执行时，`brkPnt`的初始值将是当前的设备类型。

现在我们需要在每次调整窗口大小时计算断点。这个计算是一个副作用，所以我们将使用`useEffect`钩子来计算它。

```
useEffect(() => {window.addEventListener('resize', throttle(function() {setBrkPnt(getDeviceConfig(window.innerWidth));}, 200))
})
```

我们不希望每次这个钩子被重新调用时都注册事件监听器，也就是说，要么父钩子被更新，要么状态`brkPnt`被改变。

我们希望它在第一次装载时注册事件处理程序，我们还希望它在卸载时从该事件中取消注册。

为了实现这一点，我们必须传递一个空数组`[]`作为`useEffect`的第二个参数，这表示我们的`useEffect`钩子没有依赖关系，传递给它的回调将只被调用一次，即在它所在的组件被挂载之后。

为了从`resize`事件中取消注册，我们需要从`useEffect`回调函数中返回一个函数，每当我们的组件被卸载时，这个函数就会被执行，并将我们所有的逻辑放在那里。

```
useEffect(() => {const calcInnerWidth = throttle(function() {setBrkPnt(getDeviceConfig(window.innerWidth))}, 200)window.addEventListener('resize', calcInnerWidth)return () => window.removeEventListener('resize', calcInnerWidth)}, [])
```

现在，让我们将整个代码连接在一起。

代码 2

# 使用

代码 3

注意:关于这个问题的更优化和更好的解决方案，请看[这篇](https://medium.com/better-programming/how-to-use-media-queries-programmatically-in-react-4d6562c3bc97)帖子

# 资源

[](https://developer.mozilla.org/en-US/docs/Web/API/Window/innerWidth) [## Window.innerWidth

### 浏览器窗口视区的宽度(以像素为单位),包括垂直滚动条(如果渲染的话)。注意:如果您使用…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Window/innerWidth) [](https://developer.mozilla.org/en-US/docs/Web/API/Window/resize_event) [## 窗口:调整事件大小

### 在一些早期的浏览器中，可以在任何 HTML 元素上注册 resize 事件处理程序。仍然有可能…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Window/resize_event)