# 使用基于承诺的 HTTP 请求的现代方式:axios-hooks

> 原文：<https://betterprogramming.pub/the-modern-way-to-use-promise-based-http-requests-axios-hooks-f00791345a37>

## axios 的 React 挂钩，内置对服务器端渲染的支持

![](img/57016f6524cbda944c096db4c7550817.png)

[https://github.com/axios/axios](https://github.com/axios/axios)

# axios-hooks 是什么？

您可能需要在 React 应用程序中向您自己的或外部的 API 发出请求，并且您通常需要使用 Promises 来获得结果，尝试在您的 React 组件中使用它们和 [axios](https://github.com/axios/axios) 库。

*Axios-hooks* 在这里用一个更简单的语法来解决这个问题，在几行代码中使用 React Hooks 的全部功能。

# 特征

所有你熟悉的 axios 牛逼，但用钩子简化了。

*   零配置，但在需要时可以配置。
*   单行用法。
*   服务器端渲染使用起来非常简单。

# 装置

用 [npm](https://www.npmjs.com/) 安装:

`npm install axios axios-hooks`

用[纱线](https://yarnpkg.com/)安装:

`yarn add axios axios-hooks`

Axios 是对等依赖，需要显式安装。

# 我如何使用它？

感觉很简单，就像使用 [Apollo 中的`useQuery`用](https://medium.com/better-programming/use-graphql-query-hooks-with-react-d440c6dcb57d?source=friends_link&sk=e88407b33a8747d34fc7d931b2354843) [GraphQL](https://medium.com/better-programming/use-graphql-query-hooks-with-react-d440c6dcb57d?source=friends_link&sk=e88407b33a8747d34fc7d931b2354843) 查询一样。

## useAxios

这个钩子将返回三个简单的元素:

*   `data`:包含服务器响应的对象。
*   `loading`:如果请求待定，则为布尔值。
*   `error`:如果响应包含错误代码和相关消息。

您将访问`useAxios` 钩子来完成几乎所有的操作，在某些情况下，还将访问`configure`钩子来定义一个基于 API 的端点 URL。

```
import { configure } from 'axios-hooks'
import LRU from 'lru-cache'
import Axios from 'axios'

const axios = Axios.create({
  baseURL: 'https://api.myjson.com/'
})

const cache = new LRU({ max: 10 })

configure({ axios, cache })
```

# CodeSandbox React axios 游乐场

*   [完成代码沙盒](https://codesandbox.io/s/axios-hooks-medium-tpzue)

# 如果你想了解更多关于钩子的知识

当我开始使用钩子时，我亲自阅读了《学会反应钩子》**,它帮助我理解了它们如何使用工具，如**use axios**:[https://amzn.to/2Y8hoX9](https://amzn.to/2Y8hoX9)**

# **资源和参考资料**

*   **[https://github.com/simoneb/axios-hooks](https://github.com/simoneb/axios-hooks)**
*   **[https://codesandbox.io/s/axios-hooks-medium-tpzue](https://codesandbox.io/s/axios-hooks-medium-tpzue)**
*   **[https://github.com/axios/axios](https://github.com/axios/axios)**
*   **[https://amzn.to/2CH3GQX](https://amzn.to/2CH3GQX)**