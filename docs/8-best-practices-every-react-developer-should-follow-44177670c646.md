# 每个 React 开发人员应该遵循的 8 个最佳实践

> 原文：<https://betterprogramming.pub/8-best-practices-every-react-developer-should-follow-44177670c646>

## 编写更好的 React 代码的技巧

![](img/c04206867e09edf832456396c364eb32.png)

图片来源 [Freepik](https://www.freepik.com/)

# 1.使用状态更新的回调语法

当一个组件的下一个状态依赖于前一个状态时，总是传递一个函数给`setState`，不要直接设置状态。下面的代码向您展示了原因:

组件分解

*   我们有一个状态变量`count`
*   `count`可以通过两个按钮递增
*   `Increment`按钮会立即更新状态
*   `Increment Lazy`按钮模仿异步操作，并在一秒半后更新状态

这很简单，不是吗？但这里的问题如下:

*   假设当前计数是 2
*   我点击`Increment Lazy`按钮，点击该按钮后，我立即点击`Increment`按钮
*   `Increment`按钮将当前计数更新为 3
*   因为我点击了`Increment Lazy`按钮，它应该在一秒半后将计数增加到四

但是这并没有发生，因为当我点击`Increment Lazy`按钮时，`count`是 2，JavaScript 将使用这个值来递增。因此，一秒半后，`Increment Lazy`处理器再次将状态设置为 3。

这不是我们想要的，并且会导致很难捕捉的错误。因此，作为最佳实践，当下一个值依赖于前一个值时，总是使用回调语法。

```
// follow this approach
<button onClick={() => {
  setTimeout(() => {
    **setCount(prevCount => prevCount + 1);**
    }, 1500);
  }}>
  Increment lazy
</button>
```

`prevCount`参数确保您每次都能获得`count` 的正确值。

[这里是上面代码](https://codesandbox.io/s/xenodochial-ardinghelli-5be8m?file=/src/App.js)的沙箱。

# 2.用正确的数据结构初始化状态

使用正确类型的数据结构作为状态变量的初始值是很重要的。下面是许多初学者和开发人员遇到的一个常见问题的代码:

我们没有为 todos 设置任何初始值。因为 React 首先渲染 JSX，然后运行导致错误的效果。我们可以通过设置一个空数组作为初始值来解决这个错误。

```
const [todos, setTodos] = useState([]);
```

注意，我给了一个数组作为初始值，而不是一个对象。如果给定一个空对象作为数组数据结构的初始值，在映射数据时可能会出错。

```
**// using wrong data structure as initial value**
const [todos, setTodos] = useState(**{}**);return (
 **// throws error: todos.map is not a function**
 {todos.map()}
);
```

因此，请始终使用您以后希望用于状态变量的数据结构。

如果您最初需要以编程方式计算数据的类型，请向`useState`钩子传递一个回调函数

```
const [todos, setTodos] = useState(() => {
  // whatever you return from this function
  // will be used as the initial value return [];
});
```

它还提高了代码的可读性。您可以看到状态变量的定义，并知道它包含什么类型的数据。

# 3.使用关键道具

属性让 React 识别列表中的每个元素。由于 DOM 操纵操作开销很大，React 在内存中维护了一个虚拟 DOM 的副本。

考虑下面的待办事项列表:

```
<ul>
  <li>Breakfast</li>
  <li>Lunch</li>
</ul>
```

现在，让我们向列表中添加一个新项目:

```
<ul>
  <li>Exercise</li>
  <li>Breakfast</li>
  <li>Lunch</li>
</ul>
```

当新项目添加到列表中时，会发生以下情况

*   React 将虚拟 DOM 中的`<li>Breakfast</li>`与更新后的虚拟 DOM 中的`<li>Exercise</li>`进行比较
*   这个比较告诉 React 列表项被改变了
*   React 将重新绘制 DOM 中的所有列表项

这是非常低效的，因为 React 必须重新绘制所有的列表项。为了解决这个问题，我们可以给一个`key`属性一个值，这个值在它的兄弟中唯一地标识一个列表项。

```
<ul>
  <li key="ex">Exercise</li>
  <li key="bf">Breakfast</li>
  <li key="lu">Lunch</li>
</ul>
```

现在，当您添加一个带有键的列表项时，React 将只是向 DOM 添加新元素，而不是再次操作 DOM 中的所有项。对大型列表不使用 key prop 会导致呈现问题。

# 4.使用去抖

这并不是 React 特有的做法，但是我见过许多开发人员不利用去抖功能。下面是从[这个栈溢出的反跳答案](https://stackoverflow.com/a/25991510/2924393):

> “去抖将把对一个函数的一系列连续调用串成对该函数的单个调用。它可以确保针对多次触发的事件发出一次通知。

让我们考虑下面的代码片段:

假设每次输入值发生变化时，您都请求搜索客户列表。如果您不去抖这个函数，那么您将会收到大量无用的请求，因为`onChange`事件触发得如此频繁。

要在 React 中消除事件处理程序的抖动，您可以执行以下操作:

现在，我们只在用户一秒钟没有输入任何内容的情况下向服务器发出一个请求。

我们必须在`useMemo`中包装我们的公开回调，以防止 React 在每次重新渲染时创建新的函数引用。我用的是 lodash 的[去抖，你也可以自己创建。](https://lodash.com/docs/4.17.15#debounce)

# 5.效果清理

当效果的依赖关系发生变化或定义该效果的组件被卸载时，将调用效果的清理功能。在类组件中，通常会使用生命周期方法`componentWillUnmount`来执行清理。

让我们考虑一个添加和删除事件侦听器的示例:

这样，我们也避免了内存泄漏。

# 6.使用道具类型

使用 prop 类型可以避免许多愚蠢的错误。如果你已经在使用 typescript，那么不使用 prop 类型也可以，但是对于 JavaScript，prop 类型是必须的。

安装一个包`prop-types`并给组件分配一个特殊的属性`propTypes`，用于定义组件属性的类型定义。

如果您不小心将此人的`age`作为字符串传递，您将在浏览器控制台中看到一条警告。

```
// throws a warning that age is not a number
<Person name="Haseeb" age="15" />
```

此外，它还帮助您的代码编辑器自动完成。如果定义了组件的属性类型，那么 VSCode 会建议您可以传递给组件的属性列表。

如果你不太了解道具类型，那么[这里有一个完整的道具类型指南。](https://javascript.plainenglish.io/the-complete-guide-to-prop-types-in-react-9baa22e80ce4)

# 7.用 React 方式构建您的代码库

始终遵循 React 构建 React 应用程序的方式。以下是我喜欢在任何 React 应用程序中使用的结构:

```
public
src
  └── components
     └── customers
           - customer related components
     └── orders
           - order related components
  └─ hocs
        - any higher order components
  └── pages
        - app pages
  └── styles
        - stylesheets
  └── utils
        - utility related files
```

有些人尝试使用 Angular 的服务和组件方法，如下所示:

```
// wrong component name
my-component.component.js// should be
MyComponent.js// AND do not create services
john-doe.service.js
```

这是不好的反应。我读过的所有 React 文档(以及我目前参加的课程)都没有展示过这种命名文件的方法。一个结构良好的代码库更容易理解，对开发者体验(DX)非常重要。

# 8.保持你的组件简短

冗长的组件使得逻辑更难理解，调试更糟糕。保持组件简短并专注于做一件事总是一个好的实践。

以下是您应该将一个大组件分解成小组件的时机:

*   当逻辑变得难以理解时
*   当您的组件做得太多时
*   由此产生的小组件可以重复使用

感谢阅读。我希望你觉得这篇文章很有用，并从中学到了一些东西。

# 进一步阅读

[](https://haseeb-anwar.medium.com/react-router-6-features-upgradation-guide-886b9bc2fb71) [## React 路由器 6 功能和升级指南

### 迄今为止最强大的 React 客户端路由器

haseeb-anwar.medium.com](https://haseeb-anwar.medium.com/react-router-6-features-upgradation-guide-886b9bc2fb71) [](/understand-keys-virtual-dom-reconciliation-and-diffing-in-react-e65a9bee316f) [## 理解键、虚拟 DOM、协调和差异

### React 开发人员的基本概念

better 编程. pub](/understand-keys-virtual-dom-reconciliation-and-diffing-in-react-e65a9bee316f)