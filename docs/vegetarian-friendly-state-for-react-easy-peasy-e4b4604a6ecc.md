# 素食主义者友好的国家反应——容易

> 原文：<https://betterprogramming.pub/vegetarian-friendly-state-for-react-easy-peasy-e4b4604a6ecc>

## Easy Peasy 为您提供了一个直观的 API 来快速方便地管理 React 应用程序的状态

![](img/3d8784a97e0f513b6f66acd6a978c02b.png)

[https://github.com/ctrlplusb/easy-peasy](https://github.com/ctrlplusb/easy-peasy)

# 为什么这么简单？

[Easy Peasy](https://github.com/ctrlplusb/easy-peasy) 为您提供了一个直观的 API 来快速方便地管理 React 应用程序的状态。与 Redux 等其他工具相比，它使用的代码更少，复杂度更低。

不需要额外的配置来支持派生状态、API 调用和性能优化。更重要的是，它完全支持 **Redux** 开发者工具。

# 主要特征

*   零配置，无样板文件
*   React 基于钩子的 API
*   数据提取/副作用
*   将状态保存到会话/本地存储
*   广泛的类型脚本支持
*   全局、共享或组件级存储
*   **反应原生**支持
*   **支持 Redux 开发工具**
*   **支持热重装**

在开发你的原生应用或浏览器 web 应用时，能够支持最后三件事对你特别有帮助。

# 装置

安装 Easy-Peasy 只需要一个简单的命令。如果您使用 NPM:

```
npm install easy-peasy
```

或者，如果你正在使用[纱线](https://yarnpkg.com/):

```
yarn add easy-peasy
```

# 基本用法

1.  定义一个包含商店和对这些对象的各种操作的模型。
2.  我们只需要导入动作来定义自定义动作，每个组件都会调用这些动作。开始时，您将每个可公开访问的变量放在 store 部分。

# 商店

在下面的例子中，我们将构建一个简单的点餐系统，在这里你可以**添加**、**删除**或**完成**自己餐馆的任何订单！

我们首先用各种函数和订单列表来定义模型:

```
import { action, thunk } from "easy-peasy";
import uuid from "uuid";

const model = {
  // Store
  foods: [],
  // Subroutines to load from API
  fetchFoods: thunk(async actions => {
    const res = await fetch(
      "https://jsonplaceholder.typicode.com/todos?_limit=4"
    );
    const foodsFromAPI = await res.json();
    actions.setFoods(foodsFromAPI);
  }),
  // Actions
  setFoods: action((state, foods) => {
    state.foods = foods;
  }),
  add: action((state, food) => {
    food.id = uuid.v4();
    state.foods = [...state.foods, food];
  }),
  toggle: action((state, id) => {
    state.foods.map(food => {
      if (food.id === id) {
        food.completed = !food.completed;
      }
      return food;
    });
  }),
  remove: action((state, id) => {
    state.foods = state.foods.filter(food => food.id !== id);
  })
};

export default model;
```

# 定义商店提供商

在这里，您将使用这两个挂钩来构建一个存储，并为其余的 React 组件提供其操作和对象。

```
import React from "react";
import model from "./model";
import { StoreProvider, createStore } from "easy-peasy";
import Orders from "./components/Orders";
import AddFood from "./components/AddFood";
import "./App.css";

const store = createStore(model);

function App() {
  return (
    <StoreProvider store={store}>
      <div className="container">
        <Orders />
        <AddFood />
        <br />
        <img
          src="https://easy-peasy.now.sh/assets/img/happy-peas.dc3eb36b.png"
          alt=""
        />
      </div>
    </StoreProvider>
  );
}

export default App;
```

# 最后，使用行动和商店！

我们将构建两个组件，一个包含所有订单的完整列表，它还将要求商店从 API 初始化值。

**订单:**你要用两个最漂亮的钩子:

*   `useStore`从存储器中加载特定状态
*   `useActions`从商店调用一个特定动作，如`fetchFoods`

```
import React, { Fragment, useEffect } from "react";
import { useStore, useActions } from "easy-peasy";
import FoodItem from "./FoodItem";

const Orders = () => {
  const foods = useStore(state => state.foods);
  const fetchFoods = useActions(actions => actions.fetchFoods);

  useEffect(() => {
    fetchFoods();
    // eslint-disable-next-line
  }, []);

  function foodList() {
    return foods.map(food => <FoodItem todo={food} key={food.id} />);
  }

  return (
    <Fragment>
      <h1>easy-peasy</h1>
      <h2>Food Order System</h2>
      <p>
        Click an order to complete it!
        <span role="img" aria-label="">
          🥦
        </span>
      </p>
      <hr />
      {foodList()}
    </Fragment>
  );
};

export default Orders;
```

`AddFood`:第二个类似组件将是向列表添加新订单的表单；总是使用不同函数的`useActions`钩子——在本例中是 add 函数，它追加一个新订单。

# 完整的 Codesandbox

【https://codesandbox.io/s/easy-peasy-food-orders-g9yxc】code sandbox 链接:

# 如果你想了解更多关于钩子的知识

当我开始使用钩子时，我亲自阅读了**"**[**Learn React Hooks**](https://amzn.to/2Y8hoX9)**"**，它帮助我理解它们如何使用工具，例如**use actions**:[https://amzn.to/2Y8hoX9](https://amzn.to/2Y8hoX9)

# 参考资料和资源

*   [https://easy-peasy.now.sh/](https://easy-peasy.now.sh/)
*   [https://github.com/ctrlplusb/easy-peasy](https://github.com/ctrlplusb/easy-peasy)
*   [https://codesandbox.io/s/easy-peasy-food-orders-g9yxc](https://codesandbox.io/s/easy-peasy-food-orders-g9yxc)