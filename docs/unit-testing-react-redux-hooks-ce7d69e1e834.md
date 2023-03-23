# 单元测试 React-Redux 挂钩

> 原文：<https://betterprogramming.pub/unit-testing-react-redux-hooks-ce7d69e1e834>

## 测试使用 useDispatch 和 useSelector 的功能组件可能略有不同。以下是测试它们的方法。

![](img/706eab17a6ebc341daeecb426eedd0c0.png)

李·坎贝尔在 [Unsplash](https://unsplash.com/s/photos/web-design?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

测试使用`useDispatch`和`useSelector`钩子的功能组件可能与常规的连接组件测试略有不同。本文演示了一种测试组件的简单方法，这种方法适用于两种类型的组件(使用这些钩子的组件或`connected`组件)。

因为学习的最好方法是创建一个小项目，我们将创建一个小的 web 应用程序。它将有一个“计数的数量”文本显示在顶部和一个按钮，当点击时增加计数的数量。这篇教程的完整源代码可以在这里找到:[https://github.com/AngSin/counter-app-tested](https://github.com/AngSin/counter-app-tested)。

首先让我们使用 create-react-app 来设置我们的项目。如果没有 create-react-app，可以使用:`npm i -g create-react-app`下载。安装完成后，使用`create-react-app <PROJECT_NAME>`初始化一个新项目。

然后要加 redux: `yarn add redux react-redux`。

我们将使用`enzyme`作为我们的测试库:
`yarn add -D enzyme enzyme-adapter-react-16`进行测试。

以下是该项目的基本框架文件:

**索引**

```
// index.js
import React from ‘react’;
import ReactDOM from ‘react-dom’;
import { Provider } from ‘react-redux’;import ***store*** from ‘./store’;
import Component from ‘./Component’;
import ‘./index.css’;ReactDOM.render(
 <Provider store={***store***}><Component /></Provider>,
 ***document***.getElementById(‘root’)
);
```

**组件**

```
// Component.js
import React from 'react';
import { ***useDispatch***, ***useSelector*** } from "react-redux";

import './Component.css';
import { addCount } from './actions';

const Component = () => {
  const count = ***useSelector***(state => state.count);
  const dispatch = ***useDispatch***();
  const handleClick = () => dispatch(addCount());

  return (
    <div className="App">
      <h3>
        Count: {count}
      </h3>
    <button onClick={handleClick}>
      Increase count
    </button>
    </div>
  );
};

export default Component;
```

**动作**

```
// actions.js
export const ***ADD_COUNT_TYPE*** = 'ADD_COUNT_TYPE';

export const addCount = () => ({
  type: ***ADD_COUNT_TYPE*** });
```

**减速器:**

```
// reducer.js
import { ***ADD_COUNT_TYPE*** } from './actions';

const initialState = {
  count: 0,
};

export default(state = initialState, action) => {
  switch(action.type) {
    case ***ADD_COUNT_TYPE***:
      return {...state, count: state.count + 1};
    default:
      return state;
  }
};
```

现在，为了创建一个与 create-react-app 的测试脚本一起工作的测试套件，让我们创建一个遵循`test.js`命名约定的测试文件。我给我的取名`Component.test.js`。因为我们使用的是 enzyme，所以我们也必须在测试文件中配置 enzyme:

```
// Component.test.js
import Enzyme, { mount } from 'enzyme';
import EnzymeAdapter from 'enzyme-adapter-react-16';Enzyme.configure({ adapter: new EnzymeAdapter() });
```

通常这段代码会放在一个`testSetup.js`文件中，但是因为我想让它成为一个准系统项目，所以我没有遵循很多 react 文件夹结构惯例。

我们可以通过使用`enzyme`的`shallow`或`mount`函数将我们的组件呈现给测试 dom，但是由于在这种情况下，我们的组件依赖于 redux 存储，我们还必须将它包装在由`react-redux`导出的`Provider` HOC 中。由于本教程的目的是全面测试我们的组件，包括 redux 端，我们必须为 redux `<Provider />`创建一个模拟存储，其初始状态满足我们的 redux 的结构。一旦完成，我们就可以使用`mount`来呈现我们的组件。现在，我们可以检查并验证计数是否正确显示，当单击按钮时，它也会更新显示的计数。我们的文件应该如下所示:

```
// Component.test.js
import React from 'react';
import Enzyme, { mount } from 'enzyme';
import EnzymeAdapter from 'enzyme-adapter-react-16';
import { Provider } from 'react-redux';
import { ***createStore*** } from 'redux';

import Component from './Component';
import reducer from './reducer';

Enzyme.configure({ adapter: new EnzymeAdapter() });describe('<Component /> unit test', () => {
  const mockStore = ***createStore***(reducer, {count: 0});
  const getWrapper = () => mount(
    <Provider store={mockStore}>
      <Component/>
    </Provider>
  );

  it('should add to count and display the correct # of counts', () => {
    const wrapper = getWrapper();
    expect(wrapper.find('h3').text()).toEqual('Count: 0');
    wrapper.find('button').simulate('click');
    expect(wrapper.find('h3').text()).toEqual('Count: 1');
  });
});
```

我们的组件现在已经被正确测试了。这通常是测试组件的最佳方式，通过模拟动作，而不是检查组件属性/状态或检查某些功能是否被触发。然而，让我们更进一步。

如果在点击按钮时，我们向服务器发出 HTTP 调用来更新评论中的赞数，会怎么样？在这种情况下，仅仅检查 UI 是不够的。我们必须检查相应的动作是否被分派，这将负责发出异步请求。在这个例子中，我们没有发出异步请求，而是调度一个动作来更新计数。我们可以编写一个测试来确保动作被调度。我们的文件现在看起来像这样:

```
// Component.test.js
import React from 'react';
import Enzyme, { mount } from 'enzyme';
import EnzymeAdapter from 'enzyme-adapter-react-16';
import { Provider } from 'react-redux';
import { ***createStore*** } from 'redux';

import Component from './Component';
import { addCount } from './actions';
import reducer from './reducer';

Enzyme.configure({ adapter: new EnzymeAdapter() });

describe('<Component /> unit test', () => {
  const getWrapper = (mockStore = ***createStore***(reducer, { count: 0 })) => mount(
    <Provider store={mockStore}>
      <Component/>
    </Provider>
  );

  it('should add to count and display the correct # of counts', () => {
    const wrapper = getWrapper();
    expect(wrapper.find('h3').text()).toEqual('Count: 0');
    wrapper.find('button').simulate('click');
    expect(wrapper.find('h3').text()).toEqual('Count: 1');
  });

  it('should dispatch the correct action on button click', () => {
    const mockStore = ***createStore***(reducer, { count: 0 });
    mockStore.dispatch = jest.fn();

    const wrapper = getWrapper(mockStore);
    wrapper.find('button').simulate('click');
    expect(mockStore.dispatch).toHaveBeenCalledWith(addCount());
  });
});
```

我们在这里做什么？我们不是在 describe 块的开头初始化`mockStore`，而是将它作为参数传递给`getWrapper`函数。这允许我们引用`mockStore`对象并检查它的方法`dispatch`是否被调用。此外，如果应用程序很复杂，我们还可以检查是否不仅调度了某个操作，而且该操作是正确的:

```
expect(mockStore.dispatch).toHaveBeenCalledWith(addCount());
```

显然，在我们的例子中，第二个测试是多余的，因为它的唯一目的是更新 UI。然而，如果不是更新 UI，而是产生副作用，例如调用 HTTP/HTTPS API，那么第二个测试将非常有用。

希望您发现这篇文章很有用，可以用它来更好地测试您的组件。如果你遇到任何问题，你可以在下面留下评论或者在源代码回购中提出问题:[https://github.com/AngSin/counter-app-tested](https://github.com/AngSin/counter-app-tested)。如果你是一个自学成才的程序员，你可能也会发现[这篇文章](https://medium.com/makingofamillionaire/how-i-got-a-90k-job-tc-as-a-self-taught-web-developer-with-1-5-years-of-experience-in-germany-465d5281f7fa)很有用。