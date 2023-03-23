# 如何确保 React 不会损坏组件

> 原文：<https://betterprogramming.pub/how-to-ensure-that-react-is-not-destroying-components-db52a653eadf>

## 一旦我们知道哪些组件有问题，修复问题就容易多了。

![](img/1d42f062286bfd8c829a27eff53162c1.png)

照片由[错误 420📷](https://unsplash.com/@error420?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

对于我们来说，确保 React 不会在组件没有改变时重新呈现它们是很常见的。我们是否知道如何识别 React 何时完全销毁实例，并在每次渲染时从头重新安装实例？

当我调试我正在使用的最新的基于 React 的文本编辑器时，我需要识别每个渲染上卸载和安装的 React 组件。我所经历的具体行为是，当在要编辑的段落之间切换时，我的编辑器需要第二次点击。第一次点击启动了 onBlur，但是它没有关注下一个要编辑的段落。

# 修复错误的问题

起初，我认为这是一个性能问题，并开始使用浏览器的性能选项卡来分析这些操作。`onBlur`事件保存了内容并重新解析了文本，对于一个大文件来说需要>1000 毫秒。

我们花了几天时间来优化它的性能和我们管理的一些依赖项，并将其缩减到不到 500 毫秒。这应该没问题，但是这种行为仍然持续在只需要 50 毫秒的小文件上。为此做了很多工作。至少现在这款应用的响应速度更快了，没有浪费时间。

修复 bug 的第二次尝试是尝试 React 18 的新功能`startTransition`。这允许我们包装一个代码块，并将其指定为当前渲染不必要的。

这听起来很适合我的场景，因为在上游保存之前，组件应该已经应用了适当的编辑。成功了！嗯，算是吧。它允许点击聚焦在新的段落上，但是一旦延迟的渲染发生就退出。它在重新渲染之前将大约 500 毫秒分割为大约 200 毫秒，在焦点丢失之后和之前分割为大约 300 毫秒。回到制图板！

我从前面的步骤中学到的是，无论组件渲染得多快，我仍然会失去焦点。这不是性能问题。我怀疑这些部件被销毁了，没有被重新使用。我开始研究，发现了很多减少重渲染的技巧，以及其他关于何时以及如何在组件上使用 key 属性的技巧。没有一个管用。

# 识别真正的问题

我需要想出一种方法来查看哪些组件被卸载，然后在不应该卸载的时候被装载。

`useEffect()`来救援了！当我们有一个带有空依赖数组的`useEffect`时，我们确保它只在第一次渲染时运行。

添加一个带有消息的`console.log()`，告诉你这是第一次渲染。如果我们在意想不到的时候看到了那条信息，那么维奥拉！我们知道在当前渲染过程中它会破坏我们现有的组件。

为了确定卸载事件何时被触发，我们在返回块的`useEffect`中添加了第二个`console.log()`。`useEffect`中的返回块是卸载时要清理的代码。这些日志一起向我们显示了卸载和装载发生的时间。

通过将此添加到我怀疑的每个组件，我能够确认哪些组件被破坏和重新创建。我现在可以确切地知道我的更改何时正确地导致了组件被重用。

```
useEffect(() => {
 if (verbose) console.log(‘ComponentName: Mount/First Render’);
 return (() => {
   if (verbose) console.log(‘ComponentName Unmount/Destroyed’);
 });
 // eslint-disable-next-line react-hooks/exhaustive-deps
}, []);
```

也许有其他方法可以做到这一点，但这对我解决一个令人沮丧的错误真的很有帮助！

为了简化将它添加到更多组件的过程，以确保只在需要时才进行渲染，我创建了一个自定义钩子，其中只包含上面的代码。为组件名和详细标志传递了两个属性。这样，我可以只在调试时打开日志。

![](img/efef57cf8c7997f4774e89eb460c11ff.png)

帕特里克·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 修复我的 bug

最后，对我的编辑器的焦点错误的修复是一个单行的修改。在我的一些组件中，我在渲染道具中传递的组件。更具体地说，我用 props 提供的组件覆盖了一些默认组件。

```
const components = {
  ...DEFAULT_PROPS.components,
  ...props.components,
};
```

我的第一次尝试是用来自`use-deep-compare`库的`useDeepCompareMemo()`来包装它，该库提供了 React 的`useEffect`、`useCallback`、`useMemo`版本，允许对依赖关系中的对象和数组进行深度比较。

然而，无论我如何记忆作为道具传入的组件，都不能解决问题。它还在不该打印的时候打印信息。

```
const components = useDeepCompareMemo(() => (
  { ...DEFAULT_PROPS.components, ...props.components }
), [props.components]); 
*// props.components appeared to still be new each time*
```

合并组件和使用 props 提供的组件的逻辑导致父组件认为自定义组件功能在每次渲染中都是新的。我希望他们永远不会改变。为了解决这个问题，我简单地用一个空的依赖数组`useMemo()`包装了这个定义，以确保它们只在第一次渲染时被定义。

```
const components = useMemo(() => (
  { ...DEFAULT_PROPS.components, ...props.components }
), []); 
*// if non-empty dependencies, components render from scratch each time*
```

现在我在包装合并的`useMemo()`中使用了一个空的依赖数组，我的控制台没有日志。不再需要从零开始破坏和重建组件。多么美丽的景色啊！

最棒的是，最大限度地减少每次渲染时的卸载和装载，使得我们已经针对大文件优化的< 500 毫秒的渲染减少到了～250 毫秒！

在调查过程中，我还发现了一些我试图用来记忆 JSX 街区的方法，而这些并不是必须的。我删除了这些，只在组件的返回块中呈现了 JSX。

我意识到 React 可以在没有我的帮助下很好地管理何时重新渲染这些块。现在这是可能的，因为我确定这个改变不会导致额外的渲染。

# 摘要

花几天时间修复一个 bug 可能会令人沮丧，而解决方案只是一个小小的改变。我们可以花很多时间优化代码，这些代码产生了名义上的影响，但没有解决根本问题。

在每次渲染时，能够识别 React 何时从零开始破坏和重新创建组件实例是至关重要的。

我发现有帮助的简单方法是利用 React 的`useEffect()`和一个空的 dependencies 数组，当 mount 和 unmount 事件发生时打印几行`console.log()`。一旦我们知道哪些组件有问题，修复问题就容易多了。