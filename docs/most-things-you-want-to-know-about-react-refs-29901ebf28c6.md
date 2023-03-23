# 你想知道的关于 React Refs 的一切

> 原文：<https://betterprogramming.pub/most-things-you-want-to-know-about-react-refs-29901ebf28c6>

## 看看引用、回调引用、转发引用和命令句柄

![](img/8ce38e3c2de2ace2f5b6096f993943e4.png)

由[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当你在 React 中遇到这些术语时，有没有感到迷茫:`[ref](https://reactjs.org/docs/refs-and-the-dom.html)` [s](https://reactjs.org/docs/refs-and-the-dom.html) 、`[callback ref](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)` [s](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs) 、`[forwarding ref](https://reactjs.org/docs/forwarding-refs.html)` [s](https://reactjs.org/docs/forwarding-refs.html) 、`[imperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle)` [s](https://reactjs.org/docs/hooks-reference.html#useimperativehandle) ？我知道我有。

在 React 历史的开始有`[string ref](https://reactjs.org/docs/refs-and-the-dom.html#legacy-api-string-refs)` [s](https://reactjs.org/docs/refs-and-the-dom.html#legacy-api-string-refs) ，然后是回调参考——但事情仍然不对劲。从 React 16.3 起，[React 团队决定](https://github.com/reactjs/rfcs/pull/17/files)引进推广`createRef`，保留回调引用，弃用`string ref` s，11 个月后 React 16.8 来了，带着革命性的钩子。我们以附加的`useRef`和`useImperativeHandle`结束。

ref 是访问 DOM 元素或 React 元素的一种方式。我们被赋予了`ref`、`callback ref`、`forwarding ref`和`imperativeHandle`的权利

那么什么时候我们应该使用`ref` s 呢？只要我们能抵抗，我们就应该使用它们。多么讽刺！

*   React 团队说过，“避免对任何可以声明完成的事情使用引用。”
*   他们加粗了标题，“不要过度使用参考文献”
*   他们再次强调，“如果可能的话，我们建议不要暴露 DOM 节点，但这可能是一个有用的逃生出口。”

好——一个有用的逃生出口！

React 文档指出了一些很好的`ref`用例:

*   管理焦点、文本选择或媒体播放。
*   触发命令式动画。
*   与第三方 DOM 库集成。

对于这几个好的用例，我们来探索一下`ref` s。

# 使用案例

在本文中，我们使用这个用例作为例子。

有一个只读输入字段，初始文本:`highlight and copy me`。旁边是复制按钮。

![](img/d4d339ad50284699b6e5adcc8e5aca39.png)

如果单击该按钮，输入字段内容将突出显示，文本将复制到剪贴板。

![](img/0f8dc902c2073ad3ed85526602fbba01.png)

# **React.createRef**

`React.createRef`是为类组件设计的。通常，在构造函数中调用它来初始化一个`ref`，然后将生成的`ref`传递给 DOM 元素或 React 元素。

让我们来看看这段代码:

在第七行，我们创建了一个`ref`并将其分配给`this.textInput`。从第八行的输出中，我们可以看到开发者控制台上`this.textInput`的初始值。

```
Object {
  current: null
}
```

组件安装后，`this.textInput.current`被分配给输入元件。单击复制按钮可以触发第 13 行的输出:

```
"<input type='text' readonly='' value='highlight and copy me'>"
```

现在我们明白了为什么`createRe` f 会生成一个具有属性`current`的对象。这允许我们保持对对象的相同引用，同时将`current`分配给被引用的元素或组件。

当组件卸载时，`this.textInput.current`将被分配回`null`。`ref`更新发生在`componentDidMount`或`componentDidUpdate`生命周期方法之前。

从第 13 行的输出中，我们可以看到`this.textInput.current`中没有`ref`道具。`ref`，类似于`key`，是 React 内部使用的保留属性。

借助`this.textInput.current`的魔力，我们能够高亮显示第 14 行的输入，并通过第 15 行将其发送到剪贴板。

# React.useRef

`React.useRef`，相当于`React.createRef`，是为功能部件设计的。下面的代码做了同样的事情，但是它更加简洁明了。

`createRef`和`useRef`有什么区别？

*   `createRef`创建一个将`current`分配给`null`的对象。每次调用`createRef`都会生成一个新的对象。对于一个类组件的构造函数来说，它是完美的，因为它只被调用一次。但是，它不是用于功能组件的。为每次渲染重新生成一个对象并不是一个好的用途，但它是有效的。
*   `useRef`创建一个可自定义初始值的对象。只有第一次调用`useRef`才会生成一个新对象。该对象的`current`值在功能组件的生命周期内保持不变，除非它被重新分配一个新值。它是为功能组件设计的。

```
React.useRef() generates:
  Object {
    current: undefined
  }React.useRef(null) generates:
  Object {
    current: null
  }React.useRef('abc') generates:
  Object {
    current: "abc"
  }
```

*   虽然`React.createRef`的`current`是可变的，但它是供 React 内部使用的。
*   `React.useRef`的用法超出了`ref`属性——它用于保存任何可变值，类似于`instance`变量。

你遇到过这种反应警告吗？：

> 警告:无法对已卸载的组件执行反应状态更新。

下面的例子[用`useRef`解决了这个问题](https://github.com/material-components/material-components-web-react/issues/434)。第三行的`mounted`被初始化为`true`，该值在组件安装时保持不变。当组件被卸载时，`useEffect`的清理功能会通过第 11 行将其设置为`false`。然后，`setState`在组件被卸载后，第六行的条件保证命令不会被执行。

`React.useRef`不通知其内容变更。改变`.current`属性不会触发重新渲染。如果您想在 React 将一个`ref`附加或分离到一个 DOM 节点时运行一些代码，回调引用可以用于这种用途。

# 回调参考

当 React 团队弃用字符串`ref` s 时，他们保持回调`ref` s。回调`ref`是一个函数。它对`ref`何时置位和复位提供了更精细的控制。有了函数，我们可以直接存储对`textInput`的输入引用，而不需要可变的 holder `current`。类似于`createRef`和`useRef`，回调 ref 将在组件挂载时被调用，在组件卸载时被设置为`null`。

下面是一个类组件的回调`ref`实现:

下面是一个功能组件的回调`ref`实现:

不推荐回调引用——如果是内联回调，它们会在更新期间被调用两次。

# 转发参考

真正的项目是复杂的。不直接使用 input 元素，我们可能最终会得到一个组件库— `MyTextInput`。先前的`React.useRef`示例修改如下:

我们单击“复制”按钮，发现了一些问题:

*   输入字段内容不会突出显示。
*   文本不会复制到剪贴板。
*   开发人员控制台出现错误:`textInput.current.select is not a function.`
*   第 12 行输出`initialRef`，而不是`MyTextInput`。

这些问题是由于 React 没有给`current`分配非 DOM 元素造成的。`ref`不应该为`MyTextInput`设置，而是应该为第三行的输入元素设置。要解决这个问题，我们需要一个转发`ref`。

React 团队是这样定义的:`Ref`转发是一种自动将`ref`通过一个组件传递给它的一个子组件的技术。

因此，当`MyTextInput`得到一个`ref`时，它需要调用`React.forwardRef`(第一行)将`ref`传递给它的子节点:

第二行输出`MyTextInput`的`props`值，不包括`ref`:

```
Object {
  readOnly: true,
  type: "text",
  value: "highlight and copy me"
}
```

第三行输出`ref`的初始值，其中`current`为`null`:

```
Object {
  current: null
}
```

第 14 行输出分配的`textInput.current`:

```
<input type='text' readonly='' value='highlight and copy me'>
```

在转发引用的帮助下，我们的示例使用库组件`MyTextInput`。

# `React.useImperativeHandle`

`React.useImperativeHandle`甚至没有用`someRef`命名，但它仍然是 ref 家族的一部分。

*   React 团队是这样定义的，“`useImperativeHandle`定制使用`ref`时暴露给父组件的实例值。”
*   他们又一次说，“像往常一样，在大多数情况下应该避免使用引用的命令式代码。”

让我们来看看这个`ref`特性。

`React.useImperativeHandle(ref, createHandle, [deps])`用在`forwardRef`里面。它提供对`ref`的精细控制:

*   公开有限的 DOM 方法(如 select、focus、click 等。)给呼叫者。
*   重写现有的 DOM 方法。
*   向调用方提供常规方法

下面是`React.useImperativeHandle`实现的例子:

`React.useImperativeHandle`的代码是从第 4 行到第 12 行。它有两种方法，`select`和`log`。`select`是输入元素的 DOM 方法。它被选择重写并向调用者公开。`log`是正常的方法。它在开发人员控制台上打印出消息:`log is invoked inside MyTextInput.`

从第 24 行的输出中，我们可以看到当前对象不再是输入元素。相反，它是一个包含可用方法的对象:

```
Object {
  log: () => {
    console.log('log is invoked inside MyTextInput');
  },
  select: () => {
    childRef.current.select();
    console.log('Special select is called');
  }
}
```

在第 25 行，`textInput.current.log()`调用 log 方法。在第 26 行，`textInput.current.select()`调用定制的 select 方法。让我们从第 5 行到第 8 行注释掉。单击复制按钮不会使输入字段内容高亮显示，并且文本不再被复制到剪贴板。这是因为 select 方法不再公开。

实际上，对于一个真正的 React 项目，你可能不需要`useImperativeHandle`。

我们解释了`ref` s、`callback ref` s、`forwarding ref` s 和`imperativeHandle`s。React 是一种不断发展的语言。新事物的出现为开发者提供了更多的动力。

保持饥饿。保持愚蠢。让我们等待未来的`whatever ref`吧！

感谢阅读。我希望这有所帮助。如果你有任何问题，请随时回复。你可以在这里看到我的其他媒体出版物。