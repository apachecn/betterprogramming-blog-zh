# 何时使用 React。纯组件

> 原文：<https://betterprogramming.pub/when-to-use-react-purecomponent-723f85738be1>

## 使用这个鲜为人知的组件类优化性能

![](img/f666b6beba45c9f824b2d7afb10ff2f0.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com/collections/8567307/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你用过[反应](https://reactjs.org/)，那么你就熟悉`React.Component`。这可能就是你每次创建一个新的有状态组件时所扩展的东西。`App.js`就是这样一个类组件:

```
import React from 'react';export default class Parent extends React.Component {
  constructor(props) {
    super(props); this.state = {
      message: 'hello',
    };
  } render() {
    return (
      <main>
        {this.state.message}
      </main>
    );
  }
}
```

但是还有另一个鲜为人知的组件类可以扩展，叫做`React.PureComponent`。这两个类有什么区别，为什么要用`PureComponent`？

# 做出反应。PureComponent 主要用于性能优化

如 [React 文档](https://reactjs.org/docs/react-api.html#reactpurecomponent)中所述:

> 如果你的 React 组件的`render()`函数在相同的道具和状态下呈现相同的结果，在某些情况下你可以使用`React.PureComponent`来提升性能。

那到底是什么意思？假设我们有一个子组件，`child.js`:

```
import React from 'react';const Child = props => <div>{ props.message }</div>;export default Child;
```

该组件从其父组件接收道具。我们希望这个组件在收到新的道具时重新渲染。但是如果它的父节点正在跟踪不同种类的状态呢？即使子组件收到的道具没有改变，父组件会触发子组件的重新渲染吗？

假设父组件如下所示:

```
import React from 'react';
import Child from './Child';export default class Parent extends React.Component {
  constructor(props) {
    super(props); this.state = {
      counter: 0,
      message: 'hello',
    };
  } render() {
    return (
      <main>
        {this.state.counter}
        <br />
        <Child message={this.state.message} />
      </main>
    );
  }
}
```

`Parent.js`呈现一个`<main>`元素，显示两组状态，`counter`和`message`。`message`状态被传递给`<Child />`组件并在那里呈现。

仔细看看`constructor`方法中的状态，父方法在这里跟踪我们的状态:

```
this.state = {
  counter: 0,
  message: 'hello'
};
```

给定这些组件，如果我们运行`yarn start`，我们会在视口中得到以下结果:

```
0
hello
```

`0`从父组件渲染，`hello`从子组件渲染。

我们有设置在`Parent.js`的`counter`和`message`道具。再看一下`Child.js`，你可以看到它只是在接收`message`道具。如果`counter`道具改变，我们不希望`Child.js`重新渲染，因为`counter`与`Child.js`无关。对于像这样的小项目，担心这种级别的重新渲染可能不会有太大的影响，但是不必要地重新渲染一堆又一堆的子组件可能会妨碍更大项目的性能。

当我们的子组件重新渲染时，我们可以做一些事情来测试。这个 [YouTube 视频](https://www.youtube.com/watch?v=PXXjkq4A-OU)显示，通过在我们的每个组件中放置`console.log()`语句，我们可以检查哪些语句在特定事件后重新呈现。

让我们用父组件和子组件对此进行测试。首先，让我们向父组件添加一个`console.log()`。让我们也提供一种方法来改变我们的`counter`状态:

```
import React from 'react';
import Child from './Child';export default class Parent extends React.Component {
  constructor(props) {
    super(props); this.state = {
      counter: 0,
      message: 'hello',
    };
  } handleClick = () => {
    this.setState(prevState => ({ counter: prevState.counter + 1 }));
  }; render() {
    console.log('parent rendered');
    return (
      <main>
        {this.state.counter}
        <br />
        <Child message={this.state.message} />
        <button type='button' onClick={this.handleClick}>Increment</button>
      </main>
    );
  }
}
```

我们添加了一个附加到`<button>`的`handleClick()`方法。这样，我们可以增加`counter`状态，这将触发父组件的重新呈现。

如果我们点击几次按钮并检查控制台，我们会看到父组件在每次`counter`状态增加时重新呈现:

```
parent rendered
parent rendered
parent rendered
...
```

到目前为止，一切顺利。让我们也给子组件添加一个`console.log()`语句，并检查它是否也在每次`counter`递增时重新呈现:

```
import React from 'react';export default class Child extends React.Component {
  render() {
    console.log('child rendered');
    return (
      <div>{ this.props.message }</div>
    );
  }
}
```

我将子组件变成了类组件，只是为了在其中添加一个`console.log()`语句。在现实世界中，这并不是一个足够好的理由来使这个组件成为一个类。尽管如此，尽管有了`console.log()`语句，这个版本的子组件基本上与无状态组件相同。

现在，当我们在浏览器中增加时，我们会在控制台中看到:

```
Parent rendered
Child rendered
Parent rendered
Child rendered
...
```

假设这些`console.log()`语句只在`render()`方法被调用时触发，我们可以肯定地说，改变`counter`状态是不必要的重新呈现子组件。这就是`React.PureComponent`的用武之地。如果我们改为从`React.PureComponent`扩展子组件，那么我们将阻止它不必要的重新渲染:

```
import React from 'react';export default class Child extends React.PureComponent {
  render() {
    console.log('child rendered');
    return (
      <div>{ this.props.message }</div>
    );
  }
}
```

现在，当我们增加`counter`状态时，我们在控制台中得到这个:

```
parent rendered
child rendered
parent rendered
parent rendered
parent rendered
...
```

我们已经成功地防止了子组件中不必要的重新渲染。

# 引擎盖下是什么？

`Component`和`PureComponent`最大的区别在于`PureComponent`自动实现了`shouldComponentUpdate()`生命周期方法。

在`shouldComponentUpdate()`的[文档](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)中写着:

> 使用`shouldComponentUpdate()`让 React 知道组件的输出是否不受当前状态或道具变化的影响。默认行为是在每次状态改变时重新渲染，在大多数情况下，您应该依赖默认行为。

所以，现在你知道了——正如我们的`console.log()`实验所示，组件的默认行为是在每次状态改变时重新呈现。`shouldComponentUpdate()`是这个自动重新渲染之前的一个检查点，评估状态的变化是否与手头的组件有任何关系。

是的，您可以手动实现`shouldComponentUpdate()`，但是`PureComponent`是隐式实现的，有时这正是您想要的。您可以看到将几十个有状态子节点切换到扩展`PureComponent`可以避免许多不必要的重新渲染。

# 是否应该尽可能使用 PureComponent？

有时候，使用`PureComponent`没有多大意义，即使它确实有助于优化性能。给定我们上面的例子，除了使用一个`console.log()`之外，没有必要让`Child`成为一个有状态的类组件。如果没有理由让它成为一个类，那么它就不会扩展`Component`或`PureComponent`，把它变成一个类只是为了使用`PureComponent`是没有必要的。

然而，如果子组件是类，并且只被传递了其父组件的一些道具，那么`PureComponent`可能是值得研究的。