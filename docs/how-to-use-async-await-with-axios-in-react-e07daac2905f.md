# 如何在 React 中将 Async/Await 与 Axios 一起使用

> 原文：<https://betterprogramming.pub/how-to-use-async-await-with-axios-in-react-e07daac2905f>

## 使用 async/await 检索数据的最佳方式

![](img/4beed7be582a045ab8601468bc5b0664.png)

你好！我是 Morfsys 的前端开发人员，我喜欢写与 web、技术等相关的东西。在这篇文章中，我将讲述如何在 React 中将`async` / `await`与 Axios 配合使用。

# 安装 Axios

首先，要开始，我们需要安装 Axios:

```
npm install --save axios
```

# 发出 Get 请求

接下来，让我们尝试使用 Axios 从 react 组件发出一个简单的`get`请求:

上面的代码向 [https://reqres.in](https://reqres.in) 发出一个`get`请求，从他们的 API 中检索一些用户，并在我们的组件中显示用户的名字。

# 使用异步/等待

现在，我们想使用`async` / `await`发出同样的请求。

我们开始吧！

对于使用`await`的函数，我们必须将函数本身包装为异步函数:

异步函数与同步函数的不同之处在于，异步函数不会阻止它下面的代码的处理。

如果您试图发出 POST 请求，只需将参数作为第二个变量传递给 Axios:

```
axios.post('https://yourdomain.com', { name: 'Aditya' })
```

我是 Morfsys 的全栈开发人员。告诉我任何关于 aditya@morfsys.com 的事