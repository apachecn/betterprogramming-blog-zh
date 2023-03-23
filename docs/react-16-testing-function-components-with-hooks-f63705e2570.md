# React 16:用钩子测试功能组件

> 原文：<https://betterprogramming.pub/react-16-testing-function-components-with-hooks-f63705e2570>

## 如何使用 Jest 和 Enzyme 测试有状态组件

![](img/c8ea3e94331ab3b8bf997a712f320188.png)

马库斯·萨卡奇斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果可能的话，应该将有状态逻辑和视图分开。通常情况下，您可以通过 Redux 风格的缩减器和无状态组件来实现这一点。这两者都很容易测试，并且可以成为应用程序的最大部分。

然而，有时您可能仍然想测试有状态的组件，或者只是不想重构来使用 reducers。尤其是当状态变化异步发生时，事情会变得棘手。在本文中，我将告诉你如何使用 React 16、 [Jest](https://jestjs.io/en/) 和 [Enzyme](https://airbnb.io/enzyme/) 来测试各种情况下的有状态组件。

# 创建示例应用程序

让我们从使用 create-react-app 创建一个示例应用程序开始

```
npx create-react-app react16-testing
```

我们现在可以通过运行`npm run test`轻松地运行测试。之后，我们编辑`src/App.js`并添加一个最小的例子用于我们的测试。我们创建一个功能组件，它使用了`useState`钩子。

要获得一些视觉反馈，请在 app.css 中添加以下代码行。

```
.box { color: white; }
.blue { background-color: blue; }
.red { background-color: red; }
.green { background-color: green; }
```

上面的应用程序将简单地显示 3 个`button`元素和 1 个带`background-color`的`div`，这取决于你点击了哪个按钮。

# 安装测试依赖项

假设我们想测试组件在点击按钮后是否显示正确的类。我们将使用来自 Enzyme 的`shallow()`渲染 API 来完成。您可以安装相关的依赖项:

```
npm install --save enzyme enzyme-adapter-react-16 react-test-renderer
```

之后，您必须用一些初始命令创建`./src/setupTests.js`，这些命令应该在测试开始之前运行。

```
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
configure({ adapter: new Adapter() });
```

在 React 16 之前，如果我们想使用状态，我们必须使用类组件。那时，您可以简单地呈现您想要测试的组件，并可以直接在该组件上调用`setState()`来测试该组件在状态改变时是否行为正确。随着功能组件中钩子的引入，这不再可能。

# 使用酶测试具有钩子的功能部件

但是，您仍然可以手动触发相关事件，从而改变状态。下面是一个例子:`App.test.js`，它点击最后一个按钮触发一个状态改变和一次重新渲染。

您通常希望使用默认事件来访问数据属性，如下面的示例所示，而不是将值直接绑定到函数。

这样做之后，我们的测试将会失败，因为我们的`simulate`函数在默认情况下没有通过一个事件。我们不得不嘲笑它(见下面的例子)。

# 测试异步状态的功能组件

现在，在现实世界应用程序中，您的状态更改可能会被异步触发。如果我们在`handleClick`中的状态变化是异步发生的，就像下面的例子，我们的测试也会失败。

```
function handleClick(e) {
    setTimeout(() => {
        changeColor(e.target.getAttribute('data-color'));
    }, 1000);
}
```

在这个例子中，状态将在一秒钟后改变，并且在重新渲染之前测试已经完成。我们在这里可以做的是在`handleClick`中返回一个`Promise`，它在状态改变后被解析。在我们的`App.test.js`中，我们可以使用`async/await`来等待状态变化的发生。有关更多详细信息，请查看 jestjs async tutoria l。

```
function handleClick(e) {
    const node = e.target;
    return new Promise(resolve => {
        setTimeout(() => {
            changeColor(node.getAttribute('data-color'));
            resolve();
        }, 1000);
    });
}
```

# 使有状态功能组件可测试

因此，为了使带有钩子的函数组件可测试，触发状态改变的函数:

*   必须作为道具，这样我们才能手动触发它
*   如果使用模拟事件中的值，则需要模拟事件
*   如果状态变化异步发生，必须返回一个`Promise`