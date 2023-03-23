# React 挂钩的基础

> 原文：<https://betterprogramming.pub/the-basics-of-react-hooks-f270428f1b92>

## 现在造你的第一个钩子还不晚

![](img/21749f04906fb43e5bdb90c6ebff0569.png)

[Joshua Sortino](https://unsplash.com/@sortino?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 什么是钩子？

钩子是 JavaScript 函数，允许您向功能组件添加状态。它们是在 React v16.8 中添加的。

# 为什么要用钩子？

在 React 的早期版本中，状态和功能组件是互斥的特性。要么有状态，要么有功能组件。Hooks 解决了这个问题。钩子允许我们给一个功能组件添加状态。

现在我们已经了解了钩子的用途，让我们看看我们实际上是如何使用它们的。

下面是一个函数的例子，它使用钩子来呈现一段文本，告诉用户灯的状态，以及一个按钮，允许用户打开和关闭灯。

您可能会注意到，我们从 React 导入了名为`useState`的东西。这是一个钩子，我们在函数内部调用它来给组件添加一些状态。它返回我们的当前状态— `value(false)` —以及一个允许我们更新状态的函数— `setLightStatus`。例如，这个返回的函数可以在事件处理程序中调用，类似于来自类组件的`this.setState`。

`useState`钩子只接受一个初始化的状态参数，在我们的例子中是`false`。注意，我们的初始化状态不像使用`this.state`时那样是一个对象——但是如果你需要，它可以是一个对象。

# 挂钩指南

尽管钩子只是一个 JavaScript 函数，但它有两个简单的规则:

*   钩子应该只在顶层被调用，并且它们应该*而不是*在循环、条件或嵌套函数中被调用。
*   React 挂钩应该只用于功能性 React 组件内部或自定义挂钩内部。(稍后将详细介绍定制挂钩)

# 多状态变量呢？

你可以像这样多次调用`useState`:

```
function ExampleWithManyStates() {
// Declare multiple state variables!
const [miles, setMiles] = useState(42);
const [food, setFood] = useState(‘banana’);
const [ideas, setIdeas] = useState([{ ideaOne: ‘Learn Hooks’ }]);
// …
}
```

# 钩子的另一个作用

另一个增加的挂钩是`useEffect`挂钩。当`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`被一个类组件使用时，一个功能组件将使用`useEffect`钩子。`useEffect`挂钩可代替以下部件:

```
componentDidMount() 
{
 document.title = `You clicked ${this.state.count} times`; 
}
componentDidUpdate() { 
document.title = `You clicked ${this.state.count} times`; 
}
```

有了这个:

```
useEffect(() => { 
// Update the document title using the browser API 
document.title = `You clicked ${count} times`; 
});
```

通过调用`useEffect`钩子，React 将在刷新 DOM 更改后运行钩子内部定义的内容。另一个好处是，因为钩子是在组件内部定义的，所以钩子可以访问组件的属性和状态。

# 定义多个 useEffect 挂钩

`useEffect`的另一个很好的特性是我们可以用它们各自的状态钩子而不是生命周期名称来分离动作，这个特性大大提高了代码的可读性，正如你可能猜到的那样。与其这样:

```
componentDidMount() {
console.log(“Do Something”)
console.log(“Something Else”)
}
```

我们可以像这样定义多个`useEffect`钩子:

```
const [food, setFood] = useState(‘banana’);
useEffect(() => {
 document.title = “You’re Favorite Food is ${food}“;
});const [isOnline, setIsOnline] = useState(null);
useEffect(() => {
 console.log(Do Something’)
  return () => {
    console.log(“Clean Up”)
  };
});
```

需要注意的是，多个`useEffect`钩子是按照它们被定义的顺序被调用的。

# 构建自定义挂钩

通常，在构建 React 应用程序时，您希望在多个组件中重用一个状态逻辑。以前，有两种方法可以实现这一点——创建高阶组件或使用渲染道具。定制钩子允许我们以更简洁的方式实现同样的功能。首先，我们定义名为`useOnlineStatus`的定制钩子:

然后，我们可以在其他组件中使用这个自定义挂钩:

这些组件的状态相互独立。自定义钩子只允许我们重用状态逻辑。此外，习惯上用`use`作为函数来命名一个定制钩子。

# 结论

`useState`和`useEffect`钩子只是在抓 React 钩子的表面。在[文档](https://reactjs.org/docs/hooks-reference.html)中定义了许多更有用的钩子。

如果您有任何问题、建议或其他任何事情，请随时发表评论！