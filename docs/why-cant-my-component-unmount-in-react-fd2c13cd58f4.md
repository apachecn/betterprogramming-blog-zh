# 为什么我的组件不能在 React 中卸载？

> 原文：<https://betterprogramming.pub/why-cant-my-component-unmount-in-react-fd2c13cd58f4>

## 修复常见的反应竞争情况

![](img/bb1bc9c3540429cb86649c89e9ac21ca.png)

照片由[丹·加里](https://unsplash.com/@danielgarrigues1977)在 [Unsplash](https://unsplash.com/) 上拍摄。

```
Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup function.
```

有时候，我读到一个警告，但完全不知道它想告诉我什么。

让我们从第一个短语开始:“不能在一个卸载的组件上执行反应状态更新。”状态更新分散在我构建的每个组件中。奇怪的是，其中一个组件*未安装*。

这怎么可能？我的代码中发生的大多数更新都是对用户操作的“反应”。例如，按钮点击:

由`useState`提供的 setter 函数既可以传递一个值，也可以传递一个第一个参数是当前值的函数。

显然，`handleLoad`只能在按钮挂载渲染时调用。我永远不会遇到这个代码的警告。

我仍然没有任何进展。读第三句给我一个更好的提示。React 告诉我要确保取消任何订阅或异步任务。那么，下一步就是寻找异步任务！

# 3 名罪犯

当我遇到这个问题时，我发现了三个问题:异步函数、依赖于该函数的状态改变和`unmount`事件。

为了对警告进行分类，我将设置一个引用组件。它有一个状态变量:

```
function UnmountComponent() {
  const [count, setCount] = useState(0);
  ...
```

我要处理的第一个问题是异步函数。它模拟加载一些远程资源。它可能是 axios 或 fetch 调用。两秒钟后完成。

该函数不在 React 组件中:

```
function load() {
  return new Promise(res => {
    setTimeout(res, 2000);
  });
}
```

接下来是犯罪依赖。这个函数试图修改我的 React 组件的状态:

最后，我们需要一个`unmount`事件。一个用例是加载远程资源，然后导航离开该路线。在我的例子中，我将中断组件包装在一个更高级别的组件中。这个更高级别的组件只需按一下按钮就可以卸载中断组件:

该警告是一个竞争条件。第一步是单击下面代码中的`AsyncCount`按钮:

然后，在加载远程资源时，单击`Unmount`按钮。当我打开控制台时，我可以看到警告！

发生这种情况是因为在远程资源完成`(load().then)`之后调用了`setCount`，但是也在组件卸载之后调用了`setCount`。

# 结论

如果你依赖承诺并启用`React.StrictMode`，你将会遇到这种情况。这只是一种竞争情况，在这种情况下，当远程资源正在加载时，您的组件被卸载。一种解决方案是将远程资源调用放入一个`useEffect`中，并监听卸载事件:

因为组件没有其他依赖项，所以只有在组件卸载时才调用 return 函数。

我在 [CodeSandbox](https://codesandbox.io/s/sweet-feistel-9df2l?file=/src/App.js) 中构建了前面的例子。请随意使用重现步骤来运行它，以查看错误。