# React With Fetch、Axios 和 Axios 挂钩中的 REST API 消耗

> 原文：<https://betterprogramming.pub/rest-api-consumption-in-react-with-fetch-axios-and-axios-hooks-d9dd14b43c8b>

## 什么时候在你的应用中使用 Fetch 和 Axios？

![](img/cb3cf619cc042451709c7ab072537059.png)

Rob Fuller 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 React 中有多种使用 REST API 的方法，但是在这篇博客中，我们将关注如何使用两种最流行的方法来使用 REST API:

*   Axios(一个基于承诺的 HTTP 客户端)
*   Fetch API(浏览器内置的 web API)。

我们也用`[axios-hooks](https://github.com/simoneb/axios-hooks)`来讨论 Axios 的钩子版本。

首先，让我们创建一个示例 react 应用程序来演示 React 应用程序中 Rest API 的使用。

应用程序能够调用一个给出随机用户信息的[端点](https://randomuser.me/api/?results=20)。目前，我们只打印用户的全名和电子邮件。

```
import React,{useEffect,useState} from "react";

const RenderUserInfo = (props) => {
    const { name, email } = props;
    return (
        <div>
            <p>Name: {name}</p>
            <p>Email: {email}</p>
        </div>
    );
};

const UserList = () => {

    const [userList,setUserList] = useState([])

    const apiCall=()=>{
        // API logic goes here
    }

    useEffect(()=>{
        apiCall()
    },[])

    return (<div>
            {userList.map((item, index) => (
                <RenderUserInfo name={item.name} email={item.email} key={index} />
            ))}
        </div>
    );
};

export default function App() {
    return (
        <div className="App">
            <h1>Hey there! hope this is fun :)</h1>
            <UserList />
        </div>
    );
}
```

在上面的代码中，UI 部分已经准备好了，现在我们需要编写逻辑来调用 API 并在`userList` ***中设置数据。*** 让我们同时使用`Fetch`和`Axios`来实现`apiCall()`的功能。

# **获取**

`fetch()` API 是一个内置的 JavaScript 方法，用于从服务器或 API 端点获取资源。它类似于`[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)`，但是 [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 提供了一个更加强大和灵活的特性集。

它定义了诸如 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 和 [HTTP Origin header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) 语义等概念，取代了它们在别处的单独定义。

`fetch()` API 方法总是接受一个强制参数，它是您想要获取的资源的路径或 URL。它返回一个指向请求响应的承诺，不管请求是否成功。您也可以选择传入一个 init options 对象作为第二个参数。

一旦获取了响应，有几个内置的方法可以用来定义主体内容是什么以及应该如何处理它。

```
const apiCall=()=>{
    fetch("https://randomuser.me/api/?results=20")
        .then((response) => response.json())
        .then((dataArr) => {
            setUserList(dataArr.results.map((item) => ({
                    name: item.name.first + " " + item.name.last,
                    email: item.email
                })));
        });
}
```

在上面的代码中，我们从一个以 JSON 形式返回数据的 URL 获取数据，然后在`userList`中设置它。使用`fetch()`的最简单形式是只接受一个参数，即您想要获取的资源的路径，然后返回一个包含获取请求响应的承诺。这个响应是一个对象。

该响应只是一个常规的 HTTP 响应，而不是实际的 JSON。为了从响应中获取 JSON 主体内容，我们必须使用响应上的`json()`方法将响应更改为实际的 JSON。

# Axios

Axios 是一个易于使用的基于 promise 的 HTTP 客户端，用于浏览器和 node.js。由于 Axios 是基于 promise 的，我们可以利用 async，并等待更多可读的异步代码。有了 Axios，我们获得了拦截和取消请求的能力，它还有一个内置功能，可以提供客户端保护，防止跨站点请求伪造。

让我们使用 Axios 实现同样的`apiCall`功能。

```
const apiCall=()=>{
    const apiUrl = "https://randomuser.me/api/?results=20"
    axios.get(apiUrl).then((dataArr) =>{
        setUserList(dataArr.results.map((item) => ({
            name: item.name.first + " " + item.name.last,
            email: item.email
        })));
    });
}
```

*Axios 是第三方库，所以请使用以下命令安装它:*

```
npm install axios
```

在前面的代码块中，我们发出一个 GET 请求，该请求返回一个包含用户数据的承诺。随着承诺的完成，我们将数据赋给一个状态变量`userList`。

# **轴钩**

[Axios-Hooks](https://github.com/simoneb/axios-hooks) 提供了一种更简洁的方式来使用带有钩子的 Axios，代码行更少。它拥有所有预先编写的钩子，可以利用 Axios 的所有功能，并且内置了对服务器端渲染的支持。

让我们从下面的例子来理解带有 axios 挂钩的 GET 请求是什么样子的:

```
import useAxios from 'axios-hooks'

function App() {
  const [{ data, loading, error }, refetch] = useAxios(
    'https://reqres.in/api/users?delay=1'
  )

  if (loading) return <p>Loading...</p>
  if (error) return <p>Error!</p>

  return (
    <div>
      <button onClick={refetch}>refetch</button>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  )
}
```

在上面的代码中，在组件挂载时，它自动触发一个 API 调用，并将`data`、`load`中的结果设置为真，直到 API 调用正在进行，并将收到的错误放入 API 调用中。

为了手动控制 API 的调用，我们需要做一个小的改变，钩子看起来像这样:

```
import useAxios from 'axios-hooks'

function App() {
  const [{ data, loading, error }, refetch] = useAxios(
    {
      url: 'https://reqres.in/api/users/1',
      method: 'GET'
    },
    { manual: true }
  )
  if (loading) return <p>Loading...</p>
  if (error) return <p>Error!</p>

  return (
    <div>
      <button onClick={refetch}>refetch</button>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  )
}
```

现在，只有当我们调用`refetch`函数时，API 才会被调用。

请注意，使用`axios-hooks`，代码行数将会大幅减少。同时，它提供了`data`、`loading`和`error`，所以我们也不用担心状态管理。

现在，让我们看看使用 axios-hooks 对我们的原始示例所做的更改:

```
import useAxios from 'axios-hooks'

function App() {
  const [{ data, loading, error }, refetch] = useAxios(
    {
      url: 'https://reqres.in/api/users/1',
      method: 'GET'
    },
    { manual: true }
  )
  if (loading) return <p>Loading...</p>
  if (error) return <p>Error!</p>

  return (
    <div>
      <button onClick={refetch}>refetch</button>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  )
}
```

如果我们仔细观察上面的代码，我们会注意到我们没有使用`useState`和`useEffect`。

你可以在沙盒上玩上面所有的[代码。](https://codesandbox.io/s/fetch-axios-and-axios-hooks-medium-w448q?file=/src/App.js)

# 获取与 Axios

我们来谈谈 Fetch 和 Axios 对多种功能的支持有多好。

1.  **安装** — Axios 是一个独立的第三方软件包，可以轻松安装。Fetch 内置于大多数现代浏览器中；因此不需要安装。
2.  **基本语法** —从语法上来说，Fetch 和 Axios 都非常简单。但是 Axios 支持开箱即用地将响应转换为 JSON，所以在使用 Axios 时，我们跳过了将响应转换为 JSON 的步骤，不像`Fetch()`那样，我们仍然必须将响应转换为 JSON。
3.  **处理响应超时** —在 Axios 中，通过使用请求对象中的`timeout`选项，设置响应超时非常容易。但是在 Fetch 中，要做到这一点并不容易。通过使用`AbortController()`接口，Fetch 提供了一个类似的特性，但是它需要更多的时间来实现，并且可能会变得混乱。
4.  **拦截 HTTP 请求** — Axios 允许开发者拦截 HTTP 请求。当我们需要将 HTTP 请求从应用程序更改到服务器时，就需要 HTTP 拦截器。拦截器使我们能够通过在一个地方编写代码来为每个请求响应创建一个中间件。
5.  **同时发出多个请求** — Axios 允许我们使用`axios.all()`方法发出多个 HTTP 请求。`fetch()`提供了同样的特性，通过使用`promise.all()`方法，我们可以在其中发出多个`fetch()`请求。
6.  **浏览器兼容性** — Axios 拥有广泛的浏览器支持。Fetch 只支持 Chrome 42+，Firefox 39+，Edge 14+，Safari 10.1+。

Axios 和 fetch 在使用 API 方面同样出色，但是对于较小的应用程序，最好使用 Fetch。对于大规模的应用程序，总是使用 Axios，并考虑使用`axios-hooks`，因为它使代码更加清晰。

请在 [Medium](https://medium.com/@sahdevgarg) 或 [LinkedIn](https://www.linkedin.com/in/sahdev-garg-9961b0b4/) 上关注我，获取新的技术更新。

接下来读取 [Python 代码格式化程序](https://medium.com/@sahdevgarg/python-code-formatter-black-4d9349cad92d)。