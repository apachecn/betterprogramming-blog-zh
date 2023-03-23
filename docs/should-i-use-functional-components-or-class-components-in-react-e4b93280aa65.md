# React 中应该使用功能组件还是类组件？

> 原文：<https://betterprogramming.pub/should-i-use-functional-components-or-class-components-in-react-e4b93280aa65>

## 两个组件之间的最终对峙

![](img/ca5ccf374fad2ba4985cb6d4f7178eb0.png)

哈桑·帕夏在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在过去的一年半时间里，我一直是 React JS 的开发人员。作为 React JS 和 React Native 社区的一员，在脸书和其他平台上，这个问题不断出现，所以我在这里提供一些信息。

首先，让我们先了解 React！

[React JS](https://reactjs.org/) 是一个开源的 JavaScript 库，它从虚拟 DOM 中分割出 DOM，并在构建 web 应用程序时带来了一种创新的状态管理方法。逻辑与 [React Native](https://reactnative.dev/) 相同，但它更具体地构建移动应用程序。

# 学习曲线

要学习 React Native，您必须学习 React JS。要学习 React JS，您需要先了解 HTML、CSS 和 JavaScript。

有了互联网上的许多社区和指导，这肯定可以很容易地实现。

React JS 和 React Native 是唯一的移动和 web 应用吗？

不要！还有其他几个库和框架可以用来构建移动和 web 应用，分别是 [Flutter](https://flutter.dev/) 和 [Angular](https://angular.io/docs) 。

[](/react-native-vs-flutter-a-decision-pending-in-2020-65bf5835c8ed) [## 反应本土与颤振:2020 年待定的决定

### 你应该学哪一个？

better 编程. pub](/react-native-vs-flutter-a-decision-pending-in-2020-65bf5835c8ed) 

# React 为什么叫“React”

在 React 中，我们用基于组件的方法构建应用程序。这仅仅意味着我们将 UI 分割成更小的独立组件，然后将它们组合起来构建复杂的 UI。

组件是用 JavaScript 类、状态和呈现方法实现的。

状态是我们在呈现组件时想要显示的数据。render 方法呈现用户界面，它通常有 HTML 或 JSX 代码。

render 方法返回一个映射到 DOM 元素的 JavaScript 对象。React 在内存中保留了 DOM 的轻量级表示，我们称之为虚拟 DOM ***。***

因此，当我们改变一个组件的状态时，我们会得到一个新的 React 元素。React 然后将虚拟 DOM 中的这个元素与实际 DOM 进行比较，并更新实际 DOM 以保持同步。因此，我们不需要像使用普通 JavaScript 或 JQuery 那样在浏览器中使用 DOM API。

因此，这就是 React 被称为“React”的原因——它通过将虚拟 DOM 与实际 DOM 进行比较来检测虚拟 DOM 中的变化，并通过分别更新实际 DOM 来对其做出反应。

# 最终的僵局

随着类组件和函数组件成为最近几天的热门话题，以及 react 开发社区之间的激烈辩论，许多开发人员在开发 react 应用程序时都不确定使用哪一个。

因此，在下面提到的标准下，这里有一个独特的比较来突出两个组成部分的优势和均衡。

## 句法

编写函数组件和类组件时的语法非常不同。由于名字本身定义了单词，功能组件就像编写一个简单的 JavaScript 函数，而类组件就像编写一个 JavaScript 类。下面是一个函数的例子:

```
export default function Welcome(props) {
  return(
      // code to display ui
  )
}
```

**功能组件**是字面上简单的 JavaScript 函数，如上图所示。return 函数显示通常是 JSX 表达式或原始 HTML 代码的 UI 代码。它是一个有效的 React 组件，因为您可以向它传递属性。下面是一个类的例子:

```
export default class Welcome extends Component {    
    constructor(props){
        super(props);
        this.state = {}
    }
        render(){
        return(
            // code to display ui
        )
     }
}
```

**类组件**有简单 [ES6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) JavaScript 类的语法，如上图。render 方法呈现 return()函数中的用户界面。它由一个构造函数和一个呈现方法组成。

## 传递道具

处理道具在职业组件和功能组件上差别很大。这里有一个例子:

**类组件**就是简单的 ES6 JavaScript 类。因此，正如您在上面看到的，props 被传递到基本构造函数中。可以通过下面的表达式在类组件中访问和使用 props:`{this.props.<propertyName>}`。

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

**功能组件**接受“props”作为唯一参数，所以可以将属性作为 props 传递。但是如果你想传递复杂的属性，你可以通过析构它们来实现。

例如，假设您想要传递一个具有属性`title`和`name`的用户对象，它将如下所示:

```
function Welcome({user}) {
  return <h1>Hello,  <h1>Hello , {user.title, " ", user.name}</h1>;
}
```

因为我们已经解构了道具，所以我们可以通过使用`propName.attribute`在函数中的任何地方访问它们。

将属性传递到组件中，不管它们是类组件还是功能组件，都是这样完成的:

```
<Welcome name = "John" />
```

## 管理状态

如上所述，状态管理是 React 的主要内容之一。类组件密集支持状态管理。它可以管理复杂的状态对象。类中的构造函数不仅有 props，还分配了初始的`this.state`。

要将状态分配到一个类组件中，您需要定义一个构造函数，然后由它分配初始状态。这些属性被传递到基构造函数中。

当组件具有从零到最小状态管理时，使用功能组件。

然而，当编写一个功能组件，并且您意识到您需要添加一个状态时，您不必将整个组件转换成一个类组件。您可以简单地使用 React `useState()`钩子来实现这一点，如下所示:

上面的`Counter`组件显示了按钮被点击的次数。

`useState`钩子可以从‘React’导入。使用`useState`钩子，它由三个主要部分组成:

*   状态变量
*   更新状态的函数
*   初始状态变量

`useState()`只能带一个自变量，我们称之为*初始状态*。这个初始状态可以是任何东西:数字、字符串、对象、数组等等。有关管理功能组件状态的更多信息，请参考下面的故事:

[](/how-to-use-reacts-usestate-hook-ee0dc6da54f8) [## 如何使用 React 的 useState()钩子

### 从类到功能组件的状态管理

better 编程. pub](/how-to-use-reacts-usestate-hook-ee0dc6da54f8) 

## 生命周期方法

**类组件**有以下[生命周期](https://reactjs.org/docs/react-component.html)方法。

*   `componentDidMount()` —该方法在组件安装后立即触发。一个完美的例子是将数据从 API fetch 调用加载到归档页面。这种方法非常适合设置任何订阅，即初始状态。但是，当要卸载组件时，需要取消订阅。
*   `componentDidUpdate()` —初始渲染时不调用此方法。但是，每当组件更新，并且状态或道具发生变化时，就会触发此方法。该函数中的逻辑必须包装在 if 条件中。这就是更新的道具和之前的道具对比的地方。
*   `componentWillUnmount()` — 该方法在组件被卸载和销毁之前触发。它清除任何“垃圾”，如无效计时器、取消网络请求或任何订阅。因此，避免了内存泄漏。

**功能组件**使用`useEffect()`挂钩来实现。钩子是 [React 16.8](https://reactjs.org/docs/hooks-effect.html) 的新增。React `useEffect()`钩子类似于`componentDidMount()`、`componentDidUpdate()`和`componentWillUnmount()` 的总和。

以上是使用`useEffect`挂钩的简单[示例](https://reactjs.org/docs/hooks-effect.html#detailed-explanation)。

## 表演

虽然功能组件只是 JavaScript 函数，类组件是 JavaScript 类，但没有证据表明功能组件比类组件性能更好。

然而，功能组件被认为是轻量级的，因为要编译的代码比类组件少得多。说到更少的代码，阅读和测试相对更容易。

随着 React 团队最近致力于[优化](https://reactjs.org/blog/2015/10/07/react-v0.14.html#stateless-functional-components)功能组件，我们相信功能组件会比类组件有更好的贡献。

包装纸还没有揭开！

## 测试

功能组件比类组件更容易测试，因为它们只是 JavaScript 函数。使用功能组件编写测试用例并执行测试要方便得多。

Jest 是执行测试的推荐框架。同样，我们可以使用 [React 测试库](https://testing-library.com/docs/react-testing-library/intro/)来执行测试。他们表现不同。更多信息，请参考[文档](https://reactjs.org/docs/testing.html)。

我们将把这部分留给另一个故事，因为这超出了范围。

# 用哪一个

然而，只使用功能组件就像遵循过程化编程一样；使用类组件是遵循 OOP。因此，功能组件可能更容易实现。有些可能偏向于使用类组件或其他。然而，事实并非如此。

类组件和功能组件都有它们的好处。有些人可能对这个问题有偏见。那么，你选哪个？

问你自己这个问题:这个组件有某种状态吗？

如果*是*，用类组件实现。否则，用功能组件实现它。将两者结合使用，实现更有效的应用。

# 结论

观点必然会随着时间而改变，但事实不会！所以，让我们不要让偏见影响我们的决策。

> 有时候，解决方案不是选项 1 或选项 2。有时候你只是创造了一个最好的选择 3(混合)。~放开我

以上提供的事实是我基于个人研究的不带偏见的比较。因此，我的结论是，同时使用功能组件和类组件是最佳和最有效的方法。

分享知识是获得知识的唯一途径。欢迎在评论区贡献你自己的一些想法。

玩得开心！享受编码！