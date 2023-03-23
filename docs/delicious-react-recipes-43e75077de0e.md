# 美味的 React 应用

> 原文：<https://betterprogramming.pub/delicious-react-recipes-43e75077de0e>

## 提升您的应用的高级配方

![](img/0e480ef72f2f91f35621c949bf2316bf.png)

我通常不做饭，但是当我做饭的时候，我*会按照食谱*来做，而且通常会做出美味的食物！

同样的事情也适用于**网络应用**。通过遵循食谱，也称为[设计模式](https://en.wikipedia.org/wiki/Design_Patterns)，你可以得到一些美味的应用程序。

有很多方法可以帮助提升你的应用。在这篇文章中，我将带你浏览三个高级的，你可以添加到你的代码库，以改善你的 **React 应用的表现:**

1.  上下文 API
2.  展示/容器组件
3.  渲染道具

## 我们开始做饭吧！

# **配方#1:上下文 API**

为了清楚地了解我们的第一个**配方**是什么，让我们以这段代码为例:

```
import React from 'react';
import Dashboard from 'components/Dashboard';

const user = {
  name: "John Doe"
}

const App = () => {
    const { name } = user;

    return (
        <Dashboard user={ name } />
    );
};

export default App;
```

向下传递一个道具不是一个大问题，但是如果我们在`<Dashboard/>`中有另一层组件会发生什么？

```
import React from 'react';
import Profile from 'components/Profile';

const Dashboard = (props) => {
    const { user } = props;

    return (
        <Profile user={ user } />
    );
};

export default Dashboard;
```

为了让*用户名*从`<App/>`进入`<Profile/>`，它需要通过`<Dashboard/>`，即使那个道具在那个级别没有被使用，所以我们最终通过一个不需要它的组件将那个道具传递到我们的树中。

想象一个更复杂的情况，在那里将**令人沮丧的**通过一堆不使用它的组件传递道具…

想到的解决方案之一是使用 [*Redux*](https://redux.js.org/) ，但这是一个复杂的实现。您所要做的就是沿着链向下传递一个道具，而不必通过中间组件。

> 但是如果我们可以从一个深度嵌套的组件中访问一个全局状态呢？

谢天谢地，我们可以——使用**上下文 API。**

React [上下文 API](https://reactjs.org/docs/context.html) 作为一个实验特性已经存在一段时间了，但是只有在 React 的版本 [16.3.0](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) *，*中它才可以安全地用于生产。

API 解决了**道具钻取**的问题，让我们能够通过组件树传递数据，而不必在每一层手动传递道具。

## 这种行为是如何完成的？

让我们以前面的例子为例，看看我们如何使用上下文 API 传递属性。

首先，我们需要设置**上下文**:

```
import React from "react";

const MyContext = React.createContext({});

export default MyContext;
```

然后，把我们的道具存放在**供应商**那里:

```
import React from 'react';
import Dashboard from 'components/Dashboard';

const props = {
  name: "John Doe"
}

const App = () => {
    const { name } = props;

    return (
        // wrap the app in our Provider
        // pass in the value we want to access in our deeply nested component
        <React.Provider value={ name }>
            <Dashboard />
        </React.Provider>
    );
};

export default App;
```

最后，使用**消费者**访问**提供者**上存储的数据:

```
import React from 'react';

const Profile = () => {

    return (
        // consume the value holds by the Context's Provider
        <React.Consumer>
          { value => <p>{ value.user.name }</p> }
        </React.Consumer>
    );
};

export default Profile;
```

如您所见，我们没有向`<Dashboard/>`传递任何属性，因为*用户名*只在我们的深层组件`<Profile/>`中需要。

那**是不是很牛逼**？

## 那么，什么时候应该使用上下文 API 呢？

*   当你发现自己把道具传到树的深处时
*   你需要一个**全局状态**(用户设置、翻译、主题等)
*   有人告诉你在不需要的时候使用 Redux

# **配方#2:展示/容器组件**

现在，我要向你介绍我们的第二个配方，它是由[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1?source=post_page-----43e75077de0e--------------------------------)提出的，虽然他的文章[表示和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)写于 2015 年，它已经很老了，但我仍然在我的 React 应用程序上使用它，因为我相信它是最重要的设计模式之一，它也可以应用于 React 范围之外。

为了给你更多的背景，让我们想象我们正在构建一个**天气小部件应用**。为此，我们必须做两件事:

1.  从 API 请求一些天气数据
2.  在页面上呈现数据

这看起来非常简单，让我们来构建它:

```
import React from 'react';

class WeatherWidget extends React.Component {
   state = { weather: null }

   componentDidMount() {
      // fetch weather data
      getWeather().then(weather => {
         this.setState({ weather })
      })
   }

   render() {
      // display weather data
      return (
         <div>
            <h1>{ weather.city } - { weather.country }</h1>
            <ul>
               <li>{ weather.temp }</li>
               <li>{ weather.description }</li>
            </ul>
         </div>
      )
   }
}

export default WeatherWidget;
```

这很好——你对你的组件很有信心，因为它很小。但是，如果你需要添加更多的东西和逻辑，会发生什么呢？你最终肯定会得到一个臃肿的组件,这是一个庞大而复杂的组件:

*   难以理解
*   难以修改/重用
*   对其他用户不友好

> 如果有一个*关注点分离*不是更好吗？

这正是表示和容器组件背后的关键思想。

回到我们的例子，我们有两个主要关注点:

1.  UI 关注点:它与在屏幕上显示数据更相关
2.  逻辑问题:它与管理状态有关，并从 API 请求数据

每个问题都需要有自己的组成部分:

*   **呈现组件**，负责呈现我们的布局
*   **容器组件**，它基本上是负责管理我们组件的逻辑部分的组件

记住这一点，让我们重写`<WeatherWidget/>`来应用这一行为:

```
// Presentation componentimport React from 'react';

const WeatherWidget = (props) => {
    const { weather } = props;
    return (
        <div>
            <h1>{ weather.city } - { weather.country }</h1>
            <ul>
                <li>{ weather.temp }</li>
                <li>{ weather.description }</li>
            </ul>
        </div>
    )
}

export default WeatherWidget;
```

```
// Container componentimport React from 'react';
import WeatherWidget from 'components/WeatherWidget';

class WeatherWidgetContainer extends React.Component {
   state = { weather: null }

   componentDidMount() {
      // fetch weather data
      getWeather().then(weather => {
         this.setState({ weather })
      })
   }

   render() {
      // display weather data
      return (
        // the presentation component is responsible 
        // for visually displaying the widget
        <WeatherWidget weather={ this.state.weather }>
      )
   }
}

export default WeatherWidget;
```

正如你所看到的，我们已经创建了一个*关注点分离*，并且使我们的组件更加简单易读。

## 为什么这是有用的？

*   这将使代码**可读**
*   它将**可维护**
*   这将使组件**可重用**
*   这将使代码**的每个块都是可测试的**

## 你应该什么时候使用它？

*   当你的组件包含**太多东西**
*   当有*混合*的**逻辑**和**呈现**时
*   当你有一个**臃肿的组件**

通过实现这种模式，你将使你的应用程序对你和其他开发者来说更加愉快。

# **配方 3:渲染道具**

现在该给你介绍我们的**第三招**了。这是我学到的一种新的 React 设计模式——我真的很喜欢它背后的逻辑，并想与你分享。

正如我们对以前的食谱所做的那样，让我们举一个具体的例子。

假设我们想要构建一个 **PuppyFeed** **应用程序**，它将显示一个狗狗画廊，该组件需要:

*   提出`GET`请求**API/小狗**
*   显示一个**装载旋转器**等待
*   渲染小狗列表
*   如果错误，渲染**错误**

你完成了应用程序的构建，它看起来令人难以置信，一切都按预期运行。但是有一天，你的经理找到你，问你这次能否用小猫做同样的事情。你感到非常自信，因为你已经设置好了几乎所有的东西，你只需要做一些小的调整，并遵循相同的模式。

这样做，你很快意识到你是在**重复完全相同的代码——**唯一的区别是`<PuppyFeed/>`显示的是**小狗**而`<KittenFeed/>`显示的是**小猫**。这是一个*抽象* *机会*，让我们为两者制作一个通用组件，称之为`<PetFeed/>`，并以这种方式使用它:

```
<PetFeed pet='puppies' /> 🐶
<PetFeed pet='kittens' /> 🐱
```

完美，抽象正确地完成了，并且它像预期的那样工作。但是，你的经理又一次找到你，想知道我们是否可以应用相同的逻辑，但是对于**用户配置文件**，它需要呈现用户表单而不是宠物图库。

然后，你开始挠头，考虑如何将相同的逻辑用于宠物*和*并将其应用于用户资料。你决定使用一个*条件渲染技术*，根据给定的道具显示宠物图库或用户表单。

> 如果有另一种技术可以用来在 React 组件之间共享代码，那会怎么样呢？

这正是 [*渲染道具*](https://reactjs.org/docs/render-props.html) 负责的事情，太神奇了。

之所以叫**渲染道具**是因为我们传递给组件的道具名称叫**渲染**，但不一定要这样命名。

记住这一点，让我们重写`<PetFeed/>`来适应我们所有的三个场景——小狗、小猫和用户资料。

我们首先需要定义它们的共同点和不同点。

## 他们有什么共同点？

*   它们都从 API 请求数据
*   它们都显示出装载状态
*   它们都有一个错误状态

## 它们有什么不同？

*   它们各自呈现不同的布局
*   它们每个都可以渲染不同的加载微调器

正如我们所看到的，它们有共同的**逻辑**但有不同的**呈现方式**。让我们从将组件重命名为`<Resource/>`开始，并编写一些代码。

```
// Puppiesimport React from 'react';
import Resource from 'components/Resource';

const PuppyFeed = () => (
  <Resource 
    path='api/puppies' render={ data => {
      if (data.loading) return <p>Loading Puppies…</p>
      return data.paypload.map(puppy => <div>{ puppy }</div>)
    }}
  />
)

export default PuppyFeed;
```

```
// Kittensimport React from 'react';
import Resource from 'components/Resource';

const KittenFeed = () => (
  <Resource 
    path='api/kittens' render={ data => {
      if (data.loading) return <p>Loading Kittens…</p>
      return data.paypload.map(kitten => <div>{ kitten }</div>)
    }}
  />
)

export default KittenFeed;
```

```
// User profileimport React from 'react';
import Resource from 'components/Resource';

const UserProfile = () => (
  <Resource 
    path='api/profile' render={ data => {
      if (data.loading) return <p>Loading Profil…</p>
      return (
        <form>
          <label>Name</label>
          <input value={ data.paypload.name }>
        </form>
      )
    }}
  />
)

export default UserProfile;
```

这里的想法是，在从 API 获得所需数据后，我们可以将我们的表示推迟到以后，这使得我们的组件非常灵活，经得起未来的考验。

## 什么时候应该使用渲染道具？

*   不要从渲染道具开始。从简单开始，以后再重构。
*   在组件之间共享逻辑，但不共享 UI
*   其他开发者重用你的代码
*   将用户界面的呈现推迟到以后
*   处理不同组件中的常见状态/事件。

# **最终想法**

本文档中介绍的所有方法都必须处理管理状态，我不认为这是一个巧合——大多数错误来自于*不正确的* *状态管理。请记住，如果你真正了解你的应用程序的状态，你将对你的项目有更好的控制。*

食谱很重要，有几个原因，它们是:

*   经过战斗考验的
*   可分担的
*   使用起来很有趣
*   熟悉的

祝你烹饪愉快！