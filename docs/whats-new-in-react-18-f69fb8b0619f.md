# React 18 有什么新功能？

> 原文：<https://betterprogramming.pub/whats-new-in-react-18-f69fb8b0619f>

## 这里有 3 个即将推出的 React 特性，你应该会感到兴奋

![](img/5b429657902b2ba9db858ce1565771c9.png)

照片由[肖恩·奥拉欣](https://unsplash.com/@oulashin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 团队已经宣布了发布 React 18 新版本的计划。现在它处于测试阶段。作为一名 React 开发人员，我希望了解新的特性。

在这篇文章中，我们来讨论 React 18 将如何更容易地改变我们的生活。

# 新建根 API

根 API 是指向顶层数据结构的指针，React 利用它来跟踪要呈现的树。现在 React 有两个根 API:

*   **遗留旧根 API** —带有`ReactDOM.render`的现有 API。在新的 React 版本之后，此 API 将被弃用
*   **新根 API** —带有`ReactDOM.createRoot`的新根 API。这个根在 React 18 中以全新的改进和特性工作

让我们检查并比较这两个根 API。下面是传统根 API 的典型用法:

```
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('app'));
```

让我们使用新的根 API 修改前面的代码:

```
import ReactDOM from 'react-dom';
import App from './App';

// create a root
const root = ReactDOM.createRoot(document.getElementById('app'));

// render an element to the root
root.render(<App />);
```

没有太大的区别，除了根是一个可以传递的对象。例如，如果您希望您编写的某个函数能够更新根，您不需要向它传递一个 DOM，相反，您应该传递根对象。

# 自动配料

批处理是一次重新渲染中的多次反应状态更新。

```
function Counter() {
  const [count, setCount] = useState(0);
  const [disabled, setDisabled] = useState(false);

  function handleClick() {
    setCount(c => c + 1); // does not re-render yet
    setDisabled(f => !f); // does not re-render yet
    // React will only re-render once (that's batching!)
  }

  return (
    <div>
      <h1>{count}</h1>
      <button disabled={disabled} onClick={handleClick}>
        Increment
      </button>
    </div>
  );
}
```

以上，我们有`count`和`disabled`两种反应状态。当我们点击一个按钮时，React 总是批处理并在一次重新渲染中更新它们。

这避免了不必要的重新渲染，提高了我们的性能。异步请求呢？React 17 不进行批处理，而是进行两次独立的更新。

```
function Counter() {
  const [count, setCount] = useState(0);
  const [disabled, setDisabled] = useState(false);

  function handleClick() {
    doPostRequest()
      .then(() => {
        setCount(c => c + 1); // does re-render
        setDisabled(f => !f); // does re-render
      })
  }

  return (
    <div>
      <h1>{count}</h1>
      <button disabled={disabled} onClick={handleClick}>
        Increment
      </button>
    </div>
  );
}
```

现在，在 React 18 中，我们可以在`promises`、`setTimeout`、`native event handlers`或任何其他事件中批量更新状态。

# startTransition API

在我们进入`startTransition`之前，让我们检查一下 React 中有什么样的状态更新:

*   **紧急更新** —打字、点击、敲击等直接交互。当它发生时，我们期待立即的反应。否则，我们会认为我们的应用程序有问题
*   **过渡更新** — UI 从一个视图过渡到另一个视图。当我们不期望在 UI 上看到每个中间变化时，我们可以描述它们

`startTransition`解决什么问题？例如，您有一个包含列表和输入的页面。当您输入内容时，您的列表将根据输入值进行过滤。您可以存储和更新状态中的输入值，并使用该值来过滤列表:

```
setInputValue(inputValue); // Urgent: show what was typedsetSearchQuery(inputValue); // // Not urgent: show filtered list
```

当用户在输入中输入任何值时，我们应该立即显示响应。但是当我们过滤时，就需要更多的时间。因为我们正在搜索和显示一个过滤列表。因此，开发人员经常为此使用[去抖](https://www.freecodecamp.org/news/javascript-debounce-example/)。

在 React 18 中，由`startTransition` API 解决:

```
import { startTransition } from 'react';

setInputValue(input);

startTransition(() => {
  // Transition: show filtered list
  setSearchQuery(input);
});
```

如果你想了解更多关于这个`startTransition`的信息，请看下面的讨论:

[](https://github.com/reactwg/react-18/discussions/41) [## 新功能:开始过渡讨论#41 reactwg/react-18

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/reactwg/react-18/discussions/41) 

# 结论

感谢阅读，希望这篇文章对你有用。编码快乐！

# 资源

[](https://github.com/reactwg/react-18/discussions?discussions_q=root&page=1) [## reactwg/react-18

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/reactwg/react-18/discussions?discussions_q=root&page=1) [](https://ru.reactjs.org/blog/2021/06/08/the-plan-for-react-18.html) [## React 18 - React 博客的计划

### React 团队很高兴分享一些更新:我们已经开始了 React 18 版本的工作，这将是我们的下一个…

ru.reactjs.org](https://ru.reactjs.org/blog/2021/06/08/the-plan-for-react-18.html) [](https://dev.to/alexeagleson/learnings-from-react-conf-2021-17lg) [## 从 React Conf 2021 学到的东西

### 我最近有机会参加了 2021 年数字反应会议，并听取了一些来自一个真正伟大的名单…

开发到](https://dev.to/alexeagleson/learnings-from-react-conf-2021-17lg)