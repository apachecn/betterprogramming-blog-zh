# 用于状态管理的纯 JavaScript 模式

> 原文：<https://betterprogramming.pub/pure-javascript-pattern-for-state-management-75fedf0916f6>

## 当您可以依赖纯 JavaScript 时，为什么要依赖第三方工具来管理应用程序状态呢？

![](img/9c50e4af8f6f0bd95e27c577d5673ca2.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)拍摄

[Redux](https://redux.js.org/) 不错，类似的开发者管理 app 状态的工具也都不错。

但我在这里的问题是:“为什么一个 JavaScript 开发者要依赖第三方工具来管理应用状态，而不是依赖纯 JavaScript 代码，自己写的？”我认为，这样做的好处微不足道，可以讨论。

因此，本文旨在理解这种工具应该提供的基本特性，以及它在纯 JavaScript 中的实现。

# 抽象讨论

让我们直接跳到这种功能的抽象层次。众所周知，这种工具应该提供三个核心特性:

1.  远程和本地数据的集中本地存储。
2.  在任何时候，任何视图组件都应该能够读取/写入这些数据。
3.  当有数据更新时，这个集中的本地存储应该能够向任何视图组件广播。

说白了，这就是 *MVC 模式*中控制器的功能。所以，这个工具应该被看作是客户端的视图和服务器的模型之间通信的控制器。

附言:我的理解是 [GraphQL](https://graphql.org/) 最好地实现了这些要求！

# 第一项要求的实现

好吧，撇开 GraphQL 不谈，让我们用纯 JavaScript 一个接一个地实现所有功能。

现在，有了 ES6****带给我们的新选项，比如类和静态方法，特性一将这样实现:****

```
**// Controller.jslet _instance = null;export default class Controller { static get instance() {
    return _instance ? _instance : _instance = **new** *Controller*();
  } constructor() {
    *this*._core = {};
    window.Controller = *this*; //for debugging purposes
  } get core() {
    return *this*._core;
  } editCore(*prop*, *value*) {
    *this*._core[prop] = value;
  } foo(*prop*, *value*) {
    let todo = 1 + value; //some logic here
    *this*.editCore(prop, todo);
  }
}**
```

****注意静态方法在类中的位置。它不是意外地写在构造函数之上的。写在它上面也是为了反映在这样一个类中的执行顺序。****

****众所周知，首先执行静态方法，然后是构造函数，然后是实例的方法。这是因为静态方法是在类上执行的，而不是在实例上，因此是这个顺序。****

****因此，这段代码负责将所有数据存储在对象`this._core` 中，并分别通过`get core()`和`editCore(prop, value)`对其进行读写。****

****这个类还包含一个可能的函数的虚拟实现来处理内核。它从可以进行 HTTP 获取的函数到简单的算术函数都有。****

# ****第二项要求的实施****

****现在谈谈第二个代码特性。我们如何从任何视图组件读取或写入这样一个集中式本地存储？****

****可以这样做:****

```
**// DummyComponent.jsimport Controller from./Controller';Controller.instance.editCore('Message', 'Hello World!');console.log(Controller.instance.core);
//logs { Message: "Hello World! }**
```

****这样做的先决条件是`Controller.js`和`DummyViewComponent.js`在同一个文件夹中，但是这样的东西是可编辑的，在这种情况下只需要修改导入的路径。****

****现在用 core 写很直白。我们只在开始时实例化这个类一次，然后在应用程序的剩余生命周期中处理它。****

****这可以通过`Controller.instance.editCore(‘Message’, ‘Hello World!’)` 的首次写入来实现，或者简单地通过在应用的第一次挂载时执行`Controller.instance;` 来实现。****

****现在，核心对象的每次更新都将基于这个实例。****

****要读取核心对象，我们只需使用`get core()` getter，在`Controller.js`中实现即可。因此，在任何给定的时间，在任何视图组件中，我们可以执行`Controller.instance.core`，它将返回整个核心对象。****

****这样做的好处是，如果您确定要寻找什么，您还可以访问核心的特定子对象。****

****例如，如果您正在寻找一个消息子对象，您可以简单地通过`Controller.instance.core.Message`请求它。这样，你不会得到整个核心对象，而只是必要的数据。****

# ****第三项要求的实施****

****现在，第三个需求应该使我们的控制器能够在每次核心对象发生变化时将核心对象广播到订阅的视图组件。****

****让我们用公开`subscribe()`和`unsubscribe()`函数的想法来解决这个需求，这些函数将简单地推送或移除核心中需要这种功能的视图组件。****

****最后，让我们实现一个`emit()`函数，它将在所有订阅的视图组件中执行`next(core)`函数，然后每个组件应该负责后续的操作。因此，`next(core)`函数将接收更新后的内核作为参数。****

****为了实现这样的功能，我们应该以这种方式更新`Controller.js`文件:****

```
**// Controller.js
import ReactDOM from 'react-dom';let _instance = null;export default class Controller { .
  .
  . constructor() {
    *this*._core = {
      subscribers: [],
    };
    ...
  } editCore(*prop*, *value*) {
    *...*
    *this*._core.subscribers && *this*.emit();
  } emit() {
    *this*._core.subscribers.forEach(*subscriber* => 
      subscriber.next(*this*._core)
    );
  } emit() {
    *this*._core.subscribers.forEach(*component* => {
      typeof component.next === 'function' && 
        component.next(*this*._core);
    });
  } subscribe(*component*) {
    !*this*._core.subscribers.includes(component) && 
      *this*._core.subscribers.push(component);
  } unsubscribe(*component*) {
    *this*._core.subscribers.includes(component) && 
*this*._core.subscribers.splice(*this*._core.subscribers.indexOf(component), 1);
  }
}**
```

****重要的是，核心中的所有更改都要通过提供的`editCore(prop, value)` 函数，因为每次组件被订阅和核心中的更改都会调用`emit()`函数。****

****然后，在视图组件中订阅、取消订阅和接收更新的核心就像下面这样简单:****

```
**// VeryFarComponent.jsimport Controller from./Controller';class VeryFarComponent { next(*core*) {
    *console*.log('Updated core emitted to VeryFarComponent: ', core);
  } subscribe() {
    Controller.instance.subscribe(*this*);
  } unsubscribe() {
    Controller.instance.unsubscribe(*this*);
  }
}**
```

****在我们想要接收的每个视图组件中实现`next(core)`函数是很重要的，否则，仅仅将它订阅到核心不会有任何功能上的影响。****

****建议总是在卸载或其他生命周期取消订阅组件，因为这样我们就剩下不必要的组件对象了。****

# ****结论****

****这种方法有很多优点，我认为这是实现这种功能的最佳模式，因为:****

1.  ****一个目标来统治他们！****
2.  ****没有第三方复杂或半复杂的库来管理应用状态。****
3.  ****非常直观的和简单的学习曲线相比其他库的厚样板。****
4.  ****在任何使用 JavaScript 的框架中都能工作。****
5.  ****28 JavaScript 行交付一个全功能的状态管理工具。****
6.  ****订阅上没有可能的内存泄漏，因为在`emit()`中，我们可以对未安装的组件执行自动检查。在这种情况下，`emit()`将执行另一个分支，该分支将简单地从`core.subscribers`对象中删除相应的组件。****

*****所有这些的一个 [React.js 实现可以在这里找到](http://controller.surge.sh/)和它的[对应的代码库在这里](https://github.com/agonqorolli/controller)。****

****我期待着参与下面的建设性讨论。****