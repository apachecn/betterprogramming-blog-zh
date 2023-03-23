# 关于 React 组件生命周期，您需要了解的一切

> 原文：<https://betterprogramming.pub/everything-you-need-to-know-about-react-component-life-cycle-e04fbd4811f4>

## 理解 React 组件生命周期方法的工作原理

![](img/8b17f267988b7109ec154456c2c58b4f.png)

[梁金生](https://unsplash.com/@filmprint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/carnival?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我们都知道，这个世界上存在的一切都有其生命周期。作为人类，我们出生，成长，最后死亡。类似地，React 组件被初始化、更新，然后消亡。这个过程被称为 React 组件生命周期。每个组件都有一个周期，从生成并安装到 DOM 到卸载并销毁。

React 提供了在生命周期的每个阶段自动调用的*钩子和*方法。它们提供了对被调用阶段发生的事情的控制。对这些钩子的强烈意识将会给我们自由，在组件的整个生命周期中轻松地操纵组件中发生的事情。

组件的生命周期通常分为三个部分:

```
1\. [Mounting Phase](#ca2e)
2\. [Updating Update](#46f0)
3\. [Unmounting Phase](#c05a)
```

让我们来探索在这些不同阶段适用的各种生命周期方法。

# 安装阶段

当一个组件被创建在一个初始化了属性和状态的构造函数中时，它就被挂载了。这是它第一次被放入 DOM 的时候，也就是第一次被渲染的时候。在此阶段，这些方法应适用于:

*   `constructor()`
*   `getDerivedStateFromProps()`
*   `componentWillMount()`
*   `render()`
*   `componentDidMount()`

## 构造函数()

这是每个项目命名的第一个方法。初始化属性和状态是构造函数的主要目标。

此外，它还将事件链接到`this`。对`super()`的调用与`this.state`的初始化一起包含在构造函数中。需要记住的另一点是，状态初始化也可以在没有`constructor()`的情况下执行，其行为是相同的。

```
import React from ‘react’; class FriendsComponent extends React.Component {
 constructor(props) {
    super(props);
    this.state = {
    title : ‘F.R.I.E.N.D.S’
   }
    console.log(‘Saying Hi from the Constructor’);
  }
}
```

## getDerivedStateFromProps()方法

在 DOM 上呈现组件之前，调用`GetDerivedStateFromProps`方法。当组件的状态依赖于道具时，调用此方法。通常，当道具发生变化时，这种方法可以使组件调整其状态。

这种方法很少使用，但是执行的顺序是非常重要的，因为这个方法在挂载阶段和更新阶段都被命名。

继续上面的代码片段:

```
import React from ‘react’;class FriendsComponent extends React.Component {
 constructor(props) {
   super(props);
   this.state = {
   title : ‘F.R.I.E.N.D.S.’
  }
   console.log(‘Saying Hi from the Constructor’);
 } static getDerivedStateFromProps(props, state) {
   console.log(‘Saying Hi in the React Component
   getDerivedStateFromProps’);
  }
}
```

## componentWillMount()方法

在组件挂载或调用`render()` 方法之前，会调用`componentWillMount()`方法。事实是，在 React 应用程序中，您几乎无法使用这种方法。让我告诉你为什么。

`componentWillMount()`位于构造函数和`render()`之间，这使它处于一个相当奇怪的位置。对于组件，它可以用来设置默认配置，因为它在 render 方法之前，但这通常是使用构造函数来完成的。虽然这两种方法之间没有什么不同，但是不需要再次建立配置。

现在，您可能会认为这是进行客户端呈现 API 调用的最佳位置，但是不能在这里进行。

API 调用是异步的，在`render()`被调用之前，数据可能还没有被返回。所以对于 DOM(也就是用 API 响应更新数据)，什么都不能做，因为它还没有被挂载。这意味着组件至少可以用空数据呈现一次。

使用这种方法的一个好方法是执行任何可以在运行时完成的设置，比如连接到像 Firebase 这样的外部 API。与根组件一样，这种配置通常可以在组件的顶层执行，因此大多数组件可能不会使用这种方法。

```
class FriendsComponent extends React.Component {
 componentWillMount() {
   console.log(‘Hello I am Chandler Bing’);
  }render() {
  return <h1>Hello Friends</h1>;
  }
}
```

## render()方法

最常用的生命周期方法是`render()`方法。在所有的 React 组中，你都会看到它。这是因为`render()`是类组件中唯一需要的方法。

顾名思义，它管理组件在用户界面上的呈现。这发生在组件安装和升级阶段。

这个方法不改变状态；每次调用它，它都会返回相同的结果。`render()`方法监控属性和状态的值，并返回以下任何一项:

*   反应元素
*   排列
*   碎片
*   字符串、数字、布尔值或空值

```
class FriendsComponent extends Component{
 render(){
   return <div>Hi Chandler {this.props.name}</div>
 }
}
```

## componentDidMount()方法

就在组件安装之后，这个方法是可访问的。那是在呈现的 HTML 完成加载之后。在组件生命周期中，它被调用一次，这意味着组件及其所有子组件都已被正确呈现。

在这个方法中，您还可以调用`setState()`方法来改变状态，并最终调用`render()`方法来更新 DOM。即使调用了两次`render()`函数，这个方法也保证了用户不会看到任何中间状态。

所以这确实是进行 API 调用的最安全的地方，因为组件已经被挂载，并且在这个阶段可以被 DOM 访问。

一般来说，如果没有 DOM，这是一个完成所有配置工作的好地方，因为你无法完成这些工作。用这种方法可以做很多事情:

*   将 React 应用程序链接到外部应用程序，如 JavaScript 框架或 web APIs
*   使用`SetTimeout` 或`SetInterval` 设置定时器
*   为事件添加侦听器
*   在刚刚渲染的元素上绘图

```
import React from ‘react’;class FriendsComponent extends React.Component {
 constructor(props) {
   super(props);
   this.state = {
   title : ‘F.R.I.E.N.D.S’
  }
   console.log(‘Saying Hi from the Constructor’);
 }static getDerivedStateFromProps(props, state) {
  console.log(‘Saying Hi in the React Component getDerivedStateFromProps’);
 }componentDidMount(){ 
 console.log(“Hi Friends componentDidMount”);
 }render() {
 console.log(‘Hi Friends I am in the render method’);
 return <div> This is the FriendsComponent </div>
 } 
}
```

# 更新阶段

安装后，组件并不总是保持相同的状态。基础属性可能经常改变，重新呈现组件是合适的。

更新的生命周期方法使您有权决定更新实际发生的时间和方式。

更新阶段共有五种方法，包括以下方法:

*   `componentWillReceiveProps()`
*   `shouldComponentUpdate()`
*   `componentWillUpdate()`
*   `render()`
*   `componentDidUpdate()`

## componentWillReceiveProps()方法

属性从父组件外部传递给组件。这些道具通常与父组件的状态挂钩。

因此，如果父组件的状态发生变化，那么属性也将随之改变，需要进行修改。如果这些属性与组件状态相关联，那么一个变化就意味着组件状态的变化。

`ComponentWillReceiveProps`是在新道具为一个组件做某事之前命名的一种方法。这个方法叫做使用新的道具作为参数。在这里，下一组道具和当前的道具可供我们访问。因此，我们可以使用这种方法将当前道具与新道具进行比较，并评估某些东西是否真的发生了变化。

```
class FriendsComponent extends React.Component {
 constructor(props) {
   super(props);
   this.state = {name: this.props.name};
  }componentWillReceiveProps(nextProps) {
   if (this.props.name !== nextProps.name) {
   this.setState({name: nextProps.name});
  }
 }render() {
 return (
   <h1>{this.state.name}</h1>
  )
 }
}
```

只有当前名称与先前名称不同时，上图中的`this.state.name` 才会改变。但是如果没有区别，状态就不会改变。

## shouldComponentUpdate()方法

在组件重新呈现之前，在获得一组新的道具之后，或者在有一个新的状态之前，这个方法将被调用。

我们可以看到收到两个论点，下一个道具，下一个状态。通常的行为是，一旦属性的状态改变，组件就重新呈现。

`shouldComponentUpdate()` 可用于让 React 知道组件的性能不受组件属性或状态修改的影响，因此不应重新渲染。如果返回了一个`true`或者一个`false`值，组件将继续前进并做它总是做的事情:如果它返回了`true`，则重新呈现组件。如果它返回`false`，那么它不会更新组件。请注意，当子组件的状态改变时，它不会阻止子组件重新渲染。

使用这个方法最简单的方法就是让它返回`false`，然后在某些情况下，组件就不会更新了。如果情况满足，它不会更新组件。

只有当新的输入不同于先前的输入时，组件才能更新。

```
class FriendsComponent extends React.Component {
 […]shouldComponentUpdate(nextProps, nextState) {
 if (this.state.name == nextState.name) {
 console.log(“Inside shouldComponentUpdate”);return false;
  }
 }
  […]
}
```

## componentWillUpdate()方法

`ComponentWillUpdate` 是可以在重新渲染之前使用的准备方法。这个方法不允许你调用`this.setState`。

使用这种方法，要做的一件重要的事情是与 React 架构之外的东西进行通信。`ComponentWillUpdate`如果您需要在组件渲染之前执行一些非反应设置，也可以使用。

如果你正在使用`shouldComponentUpdate`，另一个使用这种方法的时候是当你需要做道具改变的时候。在这种情况下，最好使用它而不是`ComponentWillReceiveProps`,因为它只有在组件真正被重新呈现时才会被调用。但是，如果您需要改变与道具变化相关的状态，请始终使用`ComponentWillReceiveProps`。

**注意:**虽然可以用它来实现动画和其他效果，但是不应该这样做，因为在组件被重新渲染之前，这个方法可能会被调用多次。

语法如下:

```
class FriendsComponent extends React.Component {
 […]componentWillUpdate(nextProps, nextState) {
 // Do something here
 }
 […]
}
```

## componentDidUpdate()方法

在渲染的 HTML 完成加载后，调用`componentDidUpdate`。在当前更新过程开始之前，它获得两个参数:组件的属性和状态。

与非 React 环境(如浏览器)通信或在中发出 HTTP 请求的最佳位置是`componentDidUpdate`。要阻止不需要的网络请求，只要你把新道具和以前的道具对比一下就应该可以实现了。

使用`componentDidUpdate`的图示如下。

```
class FriendsComponent extends React.Component {
 […]componentDidUpdate(prevProps, prevState) {
 if (this.props.name == prevProps.name) {
 // make ajax calls
 // Perform any other function
  }
 }
 […]
}
```

更新阶段的示例如下。

`**Child.js**`

```
import React, { Component } from ‘react’; class ChildFriendsComponent extends Component{ 
 constructor(props){ 
   super(props); 
   this.state={ 
   value:’FRIENDS’ 
  } 
   console.log(“Hi you’re in the child constructor”); 
 } static getDerivedStateFromProps(props,state){ 
  console.log(“Hi you’re in the child getDerivedStateFromProps”); 
  return null; 
 } componentDidMount(){ 
 console.log(“Hi you’re in the child componentDidMount”); 
 } shouldComponentUpdate(){ 
 console.log(“Hi you’re in the child shouldComponentUpdate”); 
 return true; 
 }getSnapshotBeforeUpdate(prevProps,prevState){  
 console.log("Hi you're in the child getSnapshotBeforeUpdate");  
 return null;  
 }  

componentDidUpdate(){  
  console.log("Hi you're in the child componentDidUpdate");  
 }  

render(){  
  console.log("Hi you're in the Friends Child Component render");  
  return <div/>  
 }  
}  

export default ChildFriendsComponent ;
```

`**App.js**`

```
import React,{Component} from ‘react’; 
import ChildFriendsComponent from ‘./Child’; class FriendsComponent extends Component{ 
 constructor(props){ 
   super(props); 
   this.state={ 
   value:FRIENDS’ 
 } 
   this.changeState = this.changeState.bind(this); 
   console.log(“Hi you’re in the constructor”); 
 } static getDerivedStateFromProps(props,state){ 
   console.log(“Hi you’re in the getDerivedStateFromProps”); 
   return null; 
 } componentDidMount(){ 
   console.log(“Hi you’re in the componentDidMount”); 
 } shouldComponentUpdate(){ 
   console.log(“Hi you’re in the shouldComponentUpdate”); 
   return true; 
 } getSnapshotBeforeUpdate(prevProps,prevState){ 
   console.log(“Hi you’re in the getSnapshotBeforeUpdate”); 
   return null; 
 } componentDidUpdate(){ 
   console.log(“Hi you’re in the componentDidUpdate”); 
 } changeState = () => { 
   this.setState({ 
   value : “FRIENDS started” 
  }) 
 } render(){ 
 console.log(“Hi you’re in the render”); 
 return( 
   <div> 
   <div>Friends Parent Component</div> 
   <button onClick={this.changeState}>Say Hi</button> 
   <ChildFriendsComponent/>
   </div> 
  ); 
 } 
} export default FriendsComponent ;
```

# 卸载阶段

组件不会一直存在于 DOM 中。也许，由于状态变化或其他原因，它们必须被丢弃。这种卸载方法将帮助我们管理组件的卸载。我们说方法是因为在 React 16 中，卸载只有一种方法。

## componentWillUnmount()方法

这是唯一的卸载技术。在从 DOM 中移除组件之前，立即调用`ComponentWillUnmount`。这是应该执行任何清理的地方，比如使计时器失效、暂停网络请求、删除事件监听器或暂停任何`componentDidMount`订阅。

`componentWillUnmount()`是任何 React 组件生命周期的最后一个阶段，卸载后就不能再挂载了。而`setState()`方法会重新呈现 DOM 内容，所以不可能这样做。

```
class FriendsComponent extends React.Component { 
 […] componentWillUnmount() {
 document.removeEventListener(“click”, SomeFunction);
  }
  […]
}
```

# 功能组件中的生命周期方法或生命周期挂钩

功能组件中没有组件生命周期方法。由于功能组件只是一个 JavaScript 函数，所以我们不能在组件中使用`setState()`方法。这就是为什么它们经常被称为*无状态功能组件。*

在功能组件中，我们可以使用 React 钩子。`useEffect()`钩子可以用来复制生命周期动作，而`useState`可以在功能组件中用来存储状态。

# 结论

再深入一点，每当有趣的事情发生时，我们发现 React 会持续观察并通知我们的组件。这大部分是通过我们在本文中关注的生命周期方法实现的。

我们在这里讨论的生命周期方法是你几乎总是会用到的。对于一些不寻常和复杂的情况，您将需要一些不寻常的生命周期方法。

感谢您的阅读！