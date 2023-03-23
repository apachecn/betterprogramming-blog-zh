# 反应声明-命令桥模式

> 原文：<https://betterprogramming.pub/react-declarative-imperative-bridge-pattern-483d8ab63559>

## 在 React 应用程序中执行命令式代码的更简单方法

![](img/af010681fff0ce34d9dade8bcaa104fb.png)

由[亚历山大·巴尔苏科夫](https://unsplash.com/@aleksandr_barsukov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/bridge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

不管我们在编写 React 应用程序方面有多好，我们都不能完全避免针对命令式接口进行编码。这是因为 UI 从根本上来说是必不可少的，浏览器中的 API 也是如此。

控制媒体元素是一种常见的情况，但我们也可能处理符合浏览器惯例的第三方库。React 的声明式模型与命令式 API 不太匹配，我们需要在“是什么”和“如何”之间架起一座桥梁。在本文中，我们将讨论这样一种模式。

关注我的人都知道我是普通 JavaScript 的坚定信徒。我从香草冒险中得到的一个重要教训是，每个解决方案都有一个应该实现的自然位置，偏离这个位置会产生样板文件和复杂性。如果你曾经觉得与基于状态的 UI 更新相比，使用`useEffect`和`useRef`以及手动跟踪钩子依赖关系有点不合适，那是因为它确实如此。它不适合那个模型(双关语)，也不属于反应式 UI。

声明性-命令性桥模式旨在解决这个问题，并将声明性和命令性世界之间的桥放到一个更自然的地方。我说的自然地点是指一个已经存在一个定义明确的机制来做我们所需要的事情的地方。在声明性-命令性桥接的情况下，自然发生的地方是[定制元素](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)中的属性观察机制。

这是一个相对较新的模式，我们只有在 2020 年左右才能在我们的目标浏览器中实际使用，当时在微软最终放弃其旧的 Edge 浏览器并切换到基于 Chromium 的浏览器后，自定义元素规范[在所有浏览器中得到支持](https://caniuse.com/?search=custom%20element)。Safari 仍然不支持所有的规范(谢谢苹果！)，但这与我们在这里要做的事情没有关系。

# 反应一方

*(为完整起见，在此插入使用参考和效果的良好 ol’示例。)*

我将假设您熟悉使用引用和效果的常用方法。我不会用这种模式的例子来烦你。让我们直入主题吧。

反应部分与任何状态驱动的反应组件基本相同。

例如:

```
let 
  STOPPED = 0, 
  PLAYING = 1

let Player = () => {
  let 
    [playbackState, setPlaybackSate] = useState(STOPPED),
    onPlay = () => setPlaybackState(PLAYING),
    onStop = () => setPlaybackState(STOPPED)

  return (
    <article>
      <react-audio playback-state={playbackState}>
        <audio src="foo.mp3"></audio>
      </react-audio>
      <button onClick={onPlay} 
              aria-pressed={playbackState === PLAYING}>
        Play
      </button>
      <button onClick={onStop}>
        Stop
      </button>
    </article>
  )
}
```

我希望我不需要解释上面的代码是做什么的，因为这是 DIB 模式的要点——简单。

然而，我要指出一个小细节。`<audio>`元素是`<react-audio>`定制元素的子元素。这一点非常重要，因为 React 可以完全控制定制元素的子树。在示例代码中，React 指定了源代码，事实上，它创建了`<audio>`元素。如果我们想在回放时得到当前时间的通知，我们可以将一个`onTimeUpdate`事件处理程序直接附加到`<audio>`元素，而不是自定义元素。

# 这座桥

让我们看一下实际的声明性命令性桥接发生的自定义元素。我们称这个元素为“桥”元素。

```
customElements.define('react-audio', class extends HTMLElement {
  static get observedAttributes() { return ['playback-state'] }

  attributeChangedCallback() {
    if (this.getAttribute('playback-state') == STOPPED) {
      this.firstElementChild.pause()
      this.firstElementChild.currentTime = 0
    } else {
      this.firstElementChild.play()
    }
  }
})
```

自定义元素将`playback-state`属性声明为 observed。这将导致每次属性改变时调用`attributeChangedCallback()`实例方法。

在`attributeChangedCallbak()`中，我们将回放状态的声明性概念转化为在`<audio>`元素上执行的命令性动作，这是定制元素的第一个(也是唯一的)子元素。

请记住，随着我们需求的增长，这可能会变得更加复杂。我们可能有多个观察到的属性，我们可能发出事件，等等。说到事件，请注意，由于它的[合成事件](https://reactjs.org/docs/events.html)实现，React 仅限于少数标准事件。我们不能利用自定义事件。

`<react-audio>`元素不需要创建任何子节点，因为这不是它的目的。因为我们正在使用定制元素，所以我们仍然有呈现整个子树的*选项*，甚至为此使用[阴影 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 。通过这样做，我们放弃了对 React 方面的子节点的一些控制，但是为其他一些事情打开了大门(例如，使用不同框架的应用程序之间的代码重用，或者甚至没有框架)。关键是，是在 React 中还是在 custom 元素中呈现子树与 DIB 模式无关，模式也不会强制您这样或那样做。

# 但是，但是…自定义元素没有反应！

当然不是。这才是重点！

这种模式的全部要点是将命令性的——因此也是非特定于 React 的——代码移出效果挂钩，移出组件，无论如何它都不太合适。这是你无论如何都需要在某个地方写*的代码，但是把它包装在一个钩子中并不会比一卷卫生纸更“被动”和“状态驱动”。我认为它是嵌入在 React 组件中的“外来”代码，就像一头游荡到某人后院的野生动物。它正被营救并转移到它的自然栖息地。*

# 摘要

概括地说，DIB 模式的工作方式如下:

*   React 做了 React 做得最好的事情——状态驱动的 UI 更改——我们只是避免在组件中做任何命令性的代码(效果)
*   自定义元素的观察属性特性弥补了声明性 API 和命令性 API 之间的差距
*   所有命令式代码都存在于自定义元素中
*   定制元素可能使用也可能不使用 React 创建的子树，它与模式无关

你可以在我的 GitHub 库[中找到更完整的 DIB 实现的完整源代码。](https://github.com/foxbunny/react-dib-pattern)