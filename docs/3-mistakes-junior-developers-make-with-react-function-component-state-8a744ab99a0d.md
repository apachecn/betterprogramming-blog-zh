# 初级开发人员在使用 React 函数组件状态时犯的 3 个错误

> 原文：<https://betterprogramming.pub/3-mistakes-junior-developers-make-with-react-function-component-state-8a744ab99a0d>

## “初级开发人员对 React 组件状态犯的 3 个错误”的后续

![](img/e397080fb1dca834393cd5b9677dd009.png)

照片由 [Zyanya BMO](https://unsplash.com/@zyan_bmo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

几周前，我写了一篇关于开发人员在使用 React 组件状态时有时会犯的错误的文章。我提供的所有例子都使用了类组件和`setState`方法。

有人几次问我，这些原则是否也适用于函数组件和钩子。答案是肯定的！

根据大众的需求，我们将在本文中探索这些相同的概念，但是这次是使用`useState`钩子的功能组件。我们将看看三个常见的错误以及如何纠正它们。

# 1.直接修改状态

当改变一个组件的状态时，重要的是返回一个修改过的状态的新副本，而不是直接修改当前状态。如果您错误地修改了组件的状态，React 的区分算法将不会捕捉到更改，并且您的组件将不会正确更新。

让我们看一个例子。假设你有这样一个状态:

```
const initialState = ['red', 'blue', 'green']
let [colors] = useState(initialState)
```

现在你想给这个数组添加颜色`yellow`。这样做可能很诱人:

```
colors.push('yellow')
```

甚至是这个:

```
colors = [...colors, 'yellow']
```

但是这两种方法都是不正确的！当在一个函数组件中更新状态时，你总是需要使用由`useState`钩子提供的 setter 方法，并且你应该总是小心不要改变对象。setter 方法是数组中`useState`返回的第二个元素，所以你可以像析构 state 值一样析构它。

将元素添加到数组的正确方法如下:

```
// Initial setup
const initialState = ['red', 'blue', 'green']
const [colors, setColors] = useState(initialState)// Later, modifying the state
setColors(colors => [...colors, 'yellow'])
```

这让我们犯了第二个错误。

# 2.设置依赖于前一状态而不使用函数的状态

有两种方法可以使用`useState`钩子返回的 setter 方法。第一种方法是提供一个新值作为参数。第二种方法是提供一个函数作为参数。那么，你什么时候想用一个而不是另一个呢？

例如，如果您有一个可以启用或禁用的按钮，您可能会有一个名为`isDisabled`的状态来保存一个布尔值。如果您想将按钮从启用状态切换到禁用状态，那么编写类似下面这样的代码，使用一个值作为参数:

```
// Initial setup
const [isDisabled, setIsDisabled] = useState(false)// Later, modifying the state
setIsDisabled(!isDisabled)
```

那么，这有什么错呢？问题在于 React 状态更新可以是批处理的，这意味着多个状态更新可以在一个更新周期中发生。如果您的更新是批处理的，并且您有多个更新处于启用/禁用状态，那么最终结果可能不是您所期望的。

更新此处状态的更好方法是提供前一状态的函数作为参数:

```
// Initial setup
const [isDisabled, setIsDisabled] = useState(false)// Later, modifying the state
setIsDisabled(isDisabled => !isDisabled)
```

现在，即使您的状态更新是批处理的，并且对启用/禁用状态的多次更新是一起进行的，每次更新都将依赖于正确的先前状态，以便您总是以您期望的结果结束。

对于像递增计数器这样的事情也是如此。

不要这样做:

```
// Initial setup
const [counterValue, setCounterValue] = useState(0)// Later, modifying the state
setCounterValue(counterValue + 1)
```

执行以下操作:

```
// Initial setup
const [counterValue, setCounterValue] = useState(0)// Later, modifying the state
setCounterValue(counterValue => counterValue + 1)
```

这里的关键是，如果你的新状态依赖于旧状态的值，你应该总是使用一个函数作为参数。如果您设置的值不依赖于旧状态的值，则可以使用值作为参数。

# 3.忘记了 useState 中的 Setter 方法是异步的

最后，重要的是要记住由`useState`钩子返回的 setter 方法是一个异步方法。例如，假设我们有一个状态如下的组件:

```
const [name, setName] = useState('John')
```

然后我们有一个更新状态并将状态记录到控制台的方法:

```
const setNameToMatt = () => {
  setName('Matt')
  console.log(`The name is now... ${name}!`)
}
```

您可能认为这会将`'Matt'`记录到控制台，但事实并非如此！它记录了`'John'`！

这样做的原因是，`useState`钩子返回的 setter 方法是异步的。这意味着当它到达调用`setName`的代码行时，它将开始状态更新，但是它下面的代码将继续执行，因为异步代码是非阻塞的。

如果您有需要在状态更新后运行*的代码，React 提供了`useEffect`钩子，允许您编写在任何指定的依赖项更新后运行的代码。*

*注意:这与你在一个类组件中为* `*setState*` *方法提供回调函数的方式有点不同。出于某种原因，* `*useState*` *钩子不支持相同的 API，所以回调函数在这里不起作用。*

更新后记录当前状态的正确方法是:

```
useEffect(() => {
  if (name !== 'John') {
    console.log(`The name is now... ${name}!`)
  }
}, [name])

const setNameToMatt = () => setName('Matt')
```

好多了！现在它像预期的那样正确地记录了`'Matt'`。

*注意:在这种情况下，我添加了* `*if*` *语句来防止组件第一次挂载时出现控制台日志。如果您想要一个更通用的解决方案，推荐使用* [*使用*](https://stackoverflow.com/questions/53253940/make-react-useeffect-hook-not-run-on-initial-render) `[*useRef*](https://stackoverflow.com/questions/53253940/make-react-useeffect-hook-not-run-on-initial-render)` [*钩子*](https://stackoverflow.com/questions/53253940/make-react-useeffect-hook-not-run-on-initial-render) *来保存一个在组件挂载后更新的值。这将成功地阻止你的* `*useEffect*` *钩子在组件第一次挂载时运行。*

# 结论

你有它！三个常见错误以及如何改正。你可以在 GitHub 上[找到代码。](https://github.com/thawkin3/react-component-state-demo)

记住，犯错是可以的。你在学习。我正在学习。我们都在学习。让我们继续学习，一起变得更好。