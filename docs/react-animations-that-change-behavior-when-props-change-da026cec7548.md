# 当道具改变时如何改变反应动画的行为

> 原文：<https://betterprogramming.pub/react-animations-that-change-behavior-when-props-change-da026cec7548>

## 中途更改动画

![](img/570f4784f077e32f9c079e55a7f6c33e.png)

动画！反应过来！激动人心！

如果你想在 React 中制作动画，有很多方法可以实现。CSS 动画特别优雅，但它似乎更适合于在动画开始后不需要控制动画行为的情况。

本文将描述如何中途改变动画的行为以响应组件属性值的改变。此外，我们将在避免直接引用 DOM 元素的同时做到这一点，因为这可能会干扰 React 的渲染。

这里给出的方法可以很容易地重复用于您想要实现的任何类型的基于数据的动画。

# 呈现进度条

对于我们的例子，假设我们需要一个 React 组件来呈现一个简单的进度条。它将需要 props 操作开始的时间(`startTime`)和操作预计需要多长时间(`expectedDuration`)。

```
const outerStyle = { 
  width:'100%', 
  height:'10rem', 
  backgroundColor:'blue' 
};const innerStyle = { 
  height: '100%', 
  backgroundColor:'black' 
};function ProgressBarComponent({startTime, expectedDuration}) {
  const elapsed = Date.now() - startTime;
  const completedRatio = elapsed > expectedDuration
    ? '100%' : `${elapsed / expectedDuration * 100}%`; return (
    <div style={outerStyle}>
      <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}export default ProgressBarComponent;
```

渲染时，进度条如下所示:

![](img/949c830e303aa754743101ed6ba94cb9.png)

我不是想用美学设计来打动你。

# 制作进度条动画

制作进度条动画的任务仍然存在。我们可以使用 JavaScript 的`setTimeout()`来更新用于渲染的状态。让我预先警告你，我将拒绝下面的解决方案。

```
**import { useState } from 'react';**...**const FRAME_INTERVAL = 1000 / 30; // 30 FPS**function ProgressBarComponent({startTime, expectedDuration}) {
  **const [completedRatio, setCompletedRatio] = useState(null);** **const updateCompletion = () => {
    const elapsed = Date.now() - startTime;
    if (elapsed >= expectedDuration) {
      setCompletedRatio('100%');
      return;
    }
    setCompletedRatio(`${elapsed / expectedDuration * 100}%`);
    setTimeout(updateCompletion, FRAME_INTERVAL);
  }** **if (completedRatio === null) updateCompletion();**
  return (
    <div style={outerStyle}>
      <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}...
```

上面的代码将使进度条从左向右移动。但是它有一个问题。在这个例子中，我们希望进度条根据对操作所需时间的估计来改变进度。

如果父组件改变了传递给`ProgressBarComponent`的`expectedDuration`属性，那么在`updateCompletion()`内部执行的用于`setTimeout()`回调的代码将仍然获得`expectedDuration`的先前值，忽略这一改变。

如果在动画完成之前`expectedDuration`改变了，你会看到一些混乱的，破碎的渲染。两个独立的`setTimeout()`回调链将使用它们不同的`expectedDuration`值来呈现进度条的位置。

# 最小化 SetTimeout 回调中的状态更改

为了解决这个问题，我们可以将状态更新逻辑从`setTimeout()` 回调移到组件的呈现函数中。我们将使用`setTimeout()`来触发重新渲染。这种方法如下所示。

```
...function ProgressBarComponent({startTime, expectedDuration}) {
  const [frameNo, setFrameNo] = useState(0); **setTimeout(() => setFrameNo(frameNo + 1), FRAME_INTERVAL);** **const elapsed = Date.now() - startTime;
  const completedRatio = elapsed > expectedDuration 
    ? '100%' : `${elapsed / expectedDuration * 100}%`;** return (
    <div style={outerStyle}>
      <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}...
```

代码变得更简单了。一件好事是，我们不必担心在回调中更新除了`frameNo`之外的任何状态。`frameNo`除了触发新的渲染之外，不用于其他任何事情。

# 使用 UseEffect 避免额外渲染

使用上面的代码，每当父组件更改传递给组件的 prop 值时，都会触发一个新的 setTimeout()回调链。这可能导致对 render 函数的过度调用，从而降低 web 应用程序的性能。

我们可以添加一个效果挂钩来确保我们只有一个`setTimeout()`回调链。

```
import { useState, useEffect } from 'react';...function ProgressBarComponent({startTime, expectedDuration}) {
  const [frameNo, setFrameNo] = useState(0); **useEffect(() => {
    const timer = setTimeout(
      () => setFrameNo(frameNo + 1), FRAME_INTERVAL);
    return () => clearTimeout(timer);
  }, [frameNo]);** const elapsed = Date.now() - startTime;
  const completedRatio = elapsed > expectedDuration 
    ? '100%' : `${elapsed / expectedDuration * 100}%`; return (
    <div style={outerStyle}>
      <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}...
```

我来解释一下上面的`useEffect`是做什么的。它将执行这一行…

```
setTimeout(() => setFrameNo(frameNo + 1), FRAME_INTERVAL);
```

…第一次安装组件时。这将导致对`setFrameNo()`的调用在未来一段时间内发生，这将更新`frameNo`属性。这又会导致“setTimeout”行再次执行。

最后，当组件卸载时，它返回一个调用 clearTimeout()的函数。这可以防止超时回调在 frameNo 和 setFrameNo 不再存在后引用它们。

这是一个改进，因为我们已经将`setTimeout`行的触发器数量限制为我们需要的两个——组件挂载和`frameNo` prop 的更新。

# 动画完成后停止重新渲染

所以现在代码不那么占用 CPU 了。但是没有动画表演的时候呢？如果进度条达到 100%，我们可以停止更新动画。

```
...function ProgressBarComponent({startTime, expectedDuration}) {
  const [frameNo, setFrameNo] = useState(0);
 **const [isAnimating, setIsAnimating] = useState(true);** useEffect(() => {
    **if (isAnimating) {
      const timer = setTimeout(
        () => setFrameNo(frameNo + 1), FRAME_INTERVAL);
      return () => clearTimeout(timer);
    }
  }, [frameNo, isAnimating]);** const elapsed = Date.now() - startTime;
  const completedRatio = elapsed > expectedDuration 
    ? '100%' : `${elapsed / expectedDuration * 100}%`;
  **if (completedRatio === '100%' && isAnimating) 
    setIsAnimating(false);** return (
    <div style={outerStyle}>
      <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}...
```

# 这不是你要找的进度条

这个进度条并不漂亮。当你前面的等待增加时，它会做出不友好的事情，比如倒退。(老实，但是有点苛刻！)在文章中使用它的目的只是为了给出一个简单而具体的受改变道具影响的动画示例。

如果你没有道具控制动画的特殊需求，我推荐你使用其他更有效的方法，比如 CSS 或者画布动画。

这是该组件的完整源代码。我在此将它公之于众。

```
import { useState, useEffect } from 'react';const outerStyle = { 
  width:'100%', 
  height:'10rem', 
  backgroundColor:'blue' 
};const innerStyle = { 
  height: '100%', 
  backgroundColor:'black' 
};const FRAME_INTERVAL = 1000 / 30; // 30 FPSfunction ProgressBarComponent({startTime, expectedDuration}) {
  const [frameNo, setFrameNo] = useState(0);
  const [isAnimating, setIsAnimating] = useState(true); useEffect(() => {
    if (isAnimating) {
      const timer = setTimeout(
        () => setFrameNo(frameNo + 1), FRAME_INTERVAL);
      return () => clearTimeout(timer);
    }
  }, [frameNo, isAnimating]); const elapsed = Date.now() - startTime;
  const completedRatio = elapsed > expectedDuration 
    ? '100%' : `${elapsed / expectedDuration * 100}%`; if (completedRatio === '100%' && isAnimating) 
    setIsAnimating(false); return (
    <div style={outerStyle}>
    <div style={{...innerStyle, width:completedRatio}} />
    </div>
  );
}export default ProgressBarComponent;
```

如果你喜欢这篇文章或者觉得它有用，那么…哦，我真的不在乎你做什么。祝你生活愉快，我的朋友！

![](img/28b1a708eb16b660cc60a42aeefcdb3e.png)

* * * [媒体学分](https://medium.com/@ErikH2000/where-my-media-comes-from-59761dc4be7) * * * [想要一份工作？](https://medium.com/@ErikH2000/yes-i-can-help-you-get-an-it-job-b0b51fee4c95) * * * [LinkedIn 个人资料](https://www.linkedin.com/in/erikhermansen) * * *