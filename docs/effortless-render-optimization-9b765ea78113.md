# 轻松的渲染优化

> 原文：<https://betterprogramming.pub/effortless-render-optimization-9b765ea78113>

## 尝试 react-tracked 和 reactive-react-redux，用 react 挂钩跟踪状态使用情况

![](img/0f11a7c2a244cb7a71f85c7efe8fb9bb.png)

# 介绍

React `useContext`可以非常方便地避免支柱钻孔。它可用于定义树中多个组件可以访问的全局或共享状态。

然而，`useContext`并不是专门为全局状态设计的，这里有一个警告:对上下文值的任何更改都会传播所有的`useContext`来重新呈现组件。

这篇文章展示了一些关于状态使用跟踪的问题和解决方案的示例代码。

# 问题

让我们假设一个人对象是一个状态:

```
const initialState = {
  firstName: 'Harry',
  familyName: 'Potter',
};
```

我们使用上下文和本地状态:

```
const PersonContext = createContext(null);const PersonProvider = ({ children }) => {
  const [person, setPerson] = useState(initialState);
  return (
    <PersonContext.Provider value={[person, setPerson]}>
      {children}
    </PersonContext.Provider>
  );
};
```

最后，这里有一个组件显示这个人的名字:

```
const DisplayFirstName = () => {
  const [person] = useContext(PersonContext);
  return (
    <div>First Name: {person.firstName}</div>
  );
};
```

到目前为止，一切顺利。但是，当您更新此人的姓氏并保持其名字不变时，问题就出现了。会触发`DisplayFirstName`重新渲染，即使渲染结果是一样的。

请注意，这并不是真正的问题，直到*变成*问题。通常情况下，大多数较小的应用程序都可以工作，但一些较大的应用程序可能会有性能问题。

# 解决方案:状态使用跟踪

让我们看看状态使用跟踪是如何解决这个问题的。

提供商看起来只是略有不同:

```
const usePerson = () => useState(initialState);
const { Provider, useTracked } = createContainer(usePerson);const PersonProvider = ({ children }) => (
  <Provider>
    {children}
  </Provider>
);
```

`DisplayFirstName`组件将被更改如下:

```
const DisplayFirstName = () => {
  const [person] = useTracked();
  return (
    <div>First Name: {person.firstName}</div>
  );
};
```

注意到变化了吗？唯一不同的是`useTracked()`而不是`useContext(...)`。

通过这个小小的改变，可以跟踪`DisplayFirstName`中的状态使用。即使更新了姓氏，只要名字没有更新，该组件也不会重新呈现。

这是毫不费力的渲染优化。

# 高级示例

一些读者可能认为这也可以通过类似于 T8 的钩子来实现。

这里还有一个例子，其中`useTracked`要简单得多。

```
const initialState = {
  firstName: 'Harry',
  familyName: 'Potter',
  showFullName: false,
};
```

假设我们有一个像上面这样的状态。让我们创建一个带有条件的组件。

```
const DisplayPersonName = () => {
  const [person] = useTracked();
  return (
    <div>
      {person.showFullName ? (
        <span>
          Full Name: {person.firstName}
          <Divider />
          {person.familyName}
        </span>
      ) : (
        <span>First Name: {person.firstName}</span>
      )}
    </div>
  );
};
```

该组件将在两种情况下重新呈现。

*   a)当`firstName`或`familyName`更新时，如果显示全名
*   b)当`firstName`更新时，如果没有显示全名

用`useSelector`重现同样的行为并不容易，很可能以分离组件而告终。

# 使用状态使用跟踪的项目

有两个项目使用状态使用跟踪。

## **反应-反应-还原**

[](https://github.com/dai-shi/reactive-react-redux) [## 代时/反应-反应-还原

### 用 React 钩子和代理绑定 React Redux。通过创造一个新的环境，为 dai-shi/reactive-react-redux 的发展做出贡献

github.com](https://github.com/dai-shi/reactive-react-redux) 

这是 react-redux 的替代库。它有相同的钩子 API 和`useTrackedState`钩子。

## **反应追踪**

[](https://github.com/dai-shi/react-tracked) [## 戴式/反应跟踪式

### 超快速反应全局/共享状态与上下文和钩子-戴-施/反应-跟踪

github.com](https://github.com/dai-shi/react-tracked) 

这是一个没有 Redux 依赖的库。本帖中的例子就是基于此。它有一个与 reactive-react-redux 兼容的 hooks API。

# 结束语

这篇文章关注的是如何轻松使用状态使用跟踪。我们没有讨论这些库的实现。

从技术上讲，有两个障碍。简而言之，我们使用代理 API 来跟踪状态使用情况，并在上下文 API 中使用一个未记录的特性来停止传播。如果您对这些内部内容感兴趣，请查看 GitHub 资源库。