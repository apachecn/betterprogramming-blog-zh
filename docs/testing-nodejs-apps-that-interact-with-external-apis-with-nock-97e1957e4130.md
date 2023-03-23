# 如何测试使用 Nock 与外部 API 交互的 Node.js 应用程序

> 原文：<https://betterprogramming.pub/testing-nodejs-apps-that-interact-with-external-apis-with-nock-97e1957e4130>

## 用 Nock 模仿你的 API 请求

![](img/16bb5ac500c79df815a0543b541ac3f4.png)

照片由[hello queue](https://unsplash.com/@helloquence?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄。

今天的网络应用很少单独存在。它们经常通过 HTTP 请求向外部 API 发出请求。

幸运的是，测试这些类型的代码并不太难，因为我们可以模拟 HTTP 响应。这意味着我们的测试不必在我们的测试中提出请求，这对于编写可重复和独立的测试是至关重要的。

在本文中，我们将研究如何测试一个与外部 API 交互的 Express 应用程序。

# 创建简单的应用程序

首先，我们创建将要测试的应用程序。该应用程序将从查克·诺里斯笑话应用程序接口[获得一个随机笑话。](http://www.icndb.com/api/)

接下来，我们创建一个空项目文件夹并运行:

```
npm init -y
```

用默认答案创建一个空的`package.json`文件。

接下来，我们分别安装 Express 和 Node-Fetch 来创建应用程序和从 API 获取数据。

我们运行:

```
npm i express node-fetch
```

安装软件包。

然后我们创建`app.js`并添加:

让应用程序理解这个笑话并将其作为回应返回。

# 添加测试

为了添加测试，我们使用 Jest test runner 运行测试并检查结果，使用 Nock 模拟我们在`app.js`中发出的请求的响应，使用 Supertest 运行 Express 应用程序并获得我们可以检查的响应。

为此，我们运行:

```
npm i nock supertest jest
```

然后我们为测试代码创建`app.test.js`，然后添加:

在文件里。

上面的代码从包含我们来自`app.js`的应用和我们用于构建测试的库开始。

```
const app = require('./app');
const nock = require('nock');
const request = require('supertest');
```

上面的第一行从`app.js`导入 Express app 实例。然后另外两行分别导入 Nock 和 Supertest 库。

然后，我们通过从 Jest 调用`test`来添加测试框架，并传入描述和`async`函数，因为我们将使用 Supertest 的 promise 版本。

然后使用 Nock，我们模拟如下响应:

正如我们所看到的，模拟请求是一个 GET 请求，这与我们在`app.js`中的请求相同。此外，网址是一样的，我们有。

不同之处在于，我们在测试中使用`mockResponse`对象返回 200 响应。

现在，实际的 API 返回什么并不重要。Nock 将拦截请求，并总是返回`mockResponse`的内容，作为在`app.js`中路由请求的响应体。

然后我们只需调用带有 Supertest 的路由，如下所示:

```
const res = await request(app).get('/');
```

调用`app.js`中的路线，然后检查如下响应:

```
expect(res.status).toBe(200);
expect(res.body).toEqual(mockResponse);
```

`toEqual`方法检查深度相等，因此我们可以传入整个对象进行检查。

![](img/80bc17a524af7706360965f8e6db0268.png)

由 [Filip Mroz](https://unsplash.com/@mroz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

# 运行测试

现在要运行我们的测试，我们必须添加:

```
"test": "jest --forceExit"
```

到`package.json`的`scripts`段。

我们需要`--forceExit`选项，这样一旦测试运行，测试就会退出。这是一个有待解决的问题。

现在，我们可以通过运行以下命令来运行测试:

```
npm test
```

那么我们应该得到:

无论我们运行多少次测试，我们都会得到相同的结果，因为我们嘲笑了 API 的响应。

真正的 API 在我们每次向它发出请求时都返回不同的东西，所以我们不能在测试中使用它。

即使我们可以，它也会慢得多，而且 API 可能并不总是可用的。

# 结论

使用 Nock，我们可以在应用程序中轻松模拟外部 API 请求的响应。然后，我们可以专注于运行我们的应用程序代码并检查结果。

现在我们有了运行快速并产生可重复结果的测试，它不依赖于测试之外的任何东西。