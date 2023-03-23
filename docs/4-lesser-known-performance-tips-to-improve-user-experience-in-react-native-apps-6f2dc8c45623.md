# 4 个鲜为人知的性能技巧，用于改善 React 原生应用的用户体验

> 原文：<https://betterprogramming.pub/4-lesser-known-performance-tips-to-improve-user-experience-in-react-native-apps-6f2dc8c45623>

## 除了优化渲染频率，你还可以做更多的事情

![](img/b51303285321dc5c6481bdf0acc09ab6.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)拍摄。

作为一家开发每天有数万活跃用户的 React 原生应用的公司的 CTO，我最关心的问题之一是确保该应用为所有用户提供出色的体验——无论他们的移动设备是好是坏。

您可能听说过 React Native 或 React 应用程序的性能直接取决于渲染的数量和频率。你可以[在](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)[官方文档](https://reactjs.org/docs/optimizing-performance.html)上读到它。在开发 React 或 React Native 应用程序时，避免不必要的渲染是您应该考虑的第一件事。你可以通过快速的谷歌搜索了解更多，所以我不打算讨论这个。

相反，我会给你额外的性能提示，这将使你的应用程序的性能有很大的不同。从我的经验来看，总有四个有问题的组件。优化后，它们会让应用程序感觉更快，总体来说反应更灵敏。

# 4 个最有问题的组件

1.导航:慢速动画

2.列表:占用大量内存

3.长时间运行的函数:阻塞 JavaScript 线程

4.持久化数据:降低整个应用程序的速度

让我们逐一检查这些组件，找出每个问题，并找到解决方案。

# 航行

你的用户会注意到有故障或缓慢的导航体验，这足以让应用程序失去他们的注意力。你的目标应该是屏幕之间平滑的动画过渡，但情况并非总是如此。

在有很多组件的屏幕上，你可能会发现在用户按下按钮和转换到新屏幕之间有很大的延迟。

这取决于您用于导航的库。例如，使用 [React Navigation](https://reactnavigation.org/) ，屏幕被渲染到屏幕外，动画仅在之后出现。所以你应该做的是快速的第一次渲染，并且只在动画结束后渲染组件的大列表。

在有很多商业逻辑的屏幕上，你可能会发现动画有点问题。

这也取决于您用于导航的库，但是如果它使用 JS 加速的动画，业务逻辑可能会导致它丢弃帧，因为它们都运行在同一个 JavaScript 线程上。所以你要做的是，在动画完成之后，才整理好你的业务逻辑。

基本上，你想要的是知道动画什么时候完成。有三个选项可用于实现这一点:

1.  `InteractionManager`:提供一个在所有动画完成后触发的回调。
2.  `didFocus`:当屏幕聚焦时提供回调。如果有过渡，则过渡完成。
3.  `setTimeout`:确保你让动画得到一些帧。这更像是一种黑客行为，但有时您可能会发现`InteractionManager`或`didFocus`方法的等待时间太长了。`setTimout`让你更好地控制时间。

现在让我们来看看如何实现其中的每一项:

```
/*  Option 1: Using ***InteractionManager****/
componentDidMount() {
 // You can do purely async functions here
 fetchData()

 **InteractionManager.runAfterInteractions**(() => {
  // Navigation is done animating // Synchronous function
  const data = processData() // Render the screen
  this.setState({
   loading: false,
   data
  }) })
} /* Option 2: Using ***didFocus****/
componentDidMount() {
 // You can do purely async functions here
 fetchData()

 **navigation.addListener('didFocus'**, () =>{
  // Navigation is done animating // Synchronous function
  const data = processData() // Render the screen
  this.setState({
   loading: false,
   data
  }) })
}/*  Option 3: Using ***setTimeout****/
componentDidMount() {
 // 1\. You can do purely async functions here
 fetchData() **setTimeout**(() => {
  // Navigation got some frames for animating // Synchronous function
  const data = processData() // Render the screen
  this.setState({
   loading: false,
   data
  }) }, 1)
}/* Render */
render() {
  /* When loading is true, render just a very simple loading view */
  /* When loading is false, render the components */
}
```

## 额外小费

因为导航将屏幕保留在堆栈上，而不是卸载它们，所以应用程序内存可能会开始增加很多。您可以使用`willBlur`(失去焦点)为那些隐藏的屏幕卸载或呈现更简单的组件(加载屏幕)。

# 列表

列表往往会占用大量内存，并可能导致 JS 和 UI 帧丢失。如果你的应用程序使用导航或者滑动器，你可能会有一些列表没有显示给用户，但是仍然会占用大量的内存资源，因为它们被安装在了某个地方。此外，即使您的列表当前正在显示给用户，他们也可能不会向下滚动列表，所以列表中的所有项目是否都已挂载并不重要。

基本上，您想要的是尽可能呈现最少的列表项，同时确保当用户看到列表时，至少显示前几项。

如果列表当前显示在活动屏幕上，则只能传递列表数组的前三项。

如果列表当前正在显示，您可以控制最初呈现的项目数量。

```
/* If your screen loses focus or is not visible, you can manipulate the data being rendered */**const data = this.isVisible ?
 this.state.bigList
 : this.state.bigList.slice(0, 3)** <FlatList
 data={data} /* If your list is always visible, you can try adding */
 **initialNumToRender = {5}**...
/>
```

# 长期运行的功能

React Native 的一个缺点是每个 JavaScript 代码都运行在单个 JavaScript 线程上。如果 JavaScript 线程被阻塞，您可能会注意到整个应用程序通常很慢或者完全没有响应。

所以首先，你根本不应该在 React Native 中有任何长时间运行的同步函数。但有时这是无法避免的。在这种情况下，您可以使用 React Native workers，这需要一些时间来实现。您还可以将长时间运行的函数分成一批一批的小步骤。这些批处理应该以几毫秒的间隔依次运行。

这几毫秒让 JavaScript 线程有一些帧来执行其他动作。这将使长时间运行的功能稍微慢一点，但你的应用程序将再次完全响应。最棒的是，这可以通过使用`await`非常容易地实现。

```
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))// How many steps your heavy function should run before it goes to sleep
const sleepFrequency = 200// How much time in ms your heavy function should sleep
const sleepTime = 10longRunningFunction()
 for (let i = 0; i < enormousNumberOfSteps; i++) { stepProcessing() /* Add this on your intensive functions */ **if (i % sleepFrequency === 0 && i > 0){
   await sleep(sleepTime)
  }** }
}
```

## 额外小费

如果用户离开屏幕或使用不同的参数再次调用长时间运行的函数，您应该在运行过程中通过检查不同的参数并在函数结束前退出函数来取消上一个函数。

# 持久数据

如果你需要持久化数据，你可能会发现频繁地保存大量数据会使你的应用程序变慢。例如，我处理需要保存到用户设备的实时数据。所以我用的是`debounce`。反跳延迟调用保存功能，直到从上次调用它起已经过了 X 毫秒。它对需要保存的内容进行批处理，然后一次性保存所有内容。如果使用 React Persist，这很容易做到:

```
const persistConfig = {
    ...//Add debounce here
 **debounce: 1000** //OR throttle if you are using redux-persist v5+};const persistedReducer = persistReducer(persistConfig, rootReducer)
```

## **奖励提示**

您也可以仅在特定时间点保存应用数据(例如，就在用户离开应用之前)。

# 结论

我希望这有助于您改进您的应用程序！

如果您在开发方面需要帮助或有任何问题，请随时发表评论或通过 andre@andrepimenta.io 联系我。