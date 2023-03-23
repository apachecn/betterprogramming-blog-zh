# 使用 Mocha 和 Chai 在 Node.js 中进行测试(第 2 部分)

> 原文：<https://betterprogramming.pub/testing-in-node-js-using-mocha-and-chai-part-2-5b5c56bf2075>

## 测试 Node.js APIs 的高级用例

![](img/4aae73d52e8088a07c5f7cf1f25ded88.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/@spacex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在上一篇文章中，我们向您介绍了 Node.js 中测试的[基础。正如我们所承诺的，本文继续上一篇文章的内容，讨论与测试一个](https://medium.com/better-programming/testing-in-node-js-using-mocha-and-chai-part-1-d5a9e91f4b06) [Node.js](https://nodejs.org/en/) 程序相关的更高级的概念。

在本文中，我们将向您介绍测试软件的第二层，集成测试，以及常见的测试实践，如模仿和存根。

首先，让我们了解什么是集成测试，以及我们为什么需要它。

# 什么是集成测试？

集成测试将几个单元(我们在单元测试中测试过的)组合在一起，并作为一个组进行测试。它检查系统中与单元之间的交互相关的错误。

我们可以使用集成测试来测试来自数据库、远程 API 和 API 端点的数据检索。集成测试的使用增加了测试覆盖的代码的广度。

通常，集成测试是开发人员的责任。但是由独立测试人员来执行测试的情况并不少见。

# Node.js 与 Mocha 和 Chai 的集成测试

从 Node.js 集成测试开始，我们将使用 [Mocha](https://www.npmjs.com/package/mocha) 和 [Chai](https://www.npmjs.com/package/chai) npm 包。出于测试目的，我们将使用带有 REST 端点的 Express 服务器。为了在测试期间向这个服务器发送 HTTP 请求，我们使用了一个名为 [Chai HTTP](https://www.npmjs.com/package/chai-http) 的新包。在继续之前，请确保使用 npm install 命令安装 Chai HTTP。

# 测试异步函数

当使用 Mocha 测试异步函数时，我们必须遵循与上一教程中使用的格式略有不同的格式。尽管我使用了 *new* 这个词来描述这种格式，但它对于 Node.js 开发人员来说并不陌生。我们可以传递一个回调，或者如果异步函数返回一个承诺，在`it`函数中使用承诺或 async/await 来处理异步代码，就像我们通常在 Node.js 中做的那样。

# 编写测试 API 的测试用例

对于集成测试的第一个实现，我们使用 REST API，它根据请求从数据库中检索相关数据，并将它们发送回客户端。为了测试实现的清晰性，假设数据库(我们使用的是 MongoDB 数据库)和应用程序的数据模型已经设置好了。

POST `route /dogs`用于使用通过 POST 请求从客户端发送的数据将新狗保存到数据库中。我们在这一步中使用的`Dog`模型有三个属性:狗的名字、年龄和品种。我们需要通过 HTTP 请求将这些数据传递给`/dog`路由来创建一条新的狗。

当处理完 API 的所有路由时，不要忘记导出`app`对象，以便我们可以访问服务器进行测试。

```
module.exports = app
```

在测试目录中创建一个名为`dogs.js`的文件，为这条路径编写测试用例。在这个文件中，我们正在测试这个路由，看它是否响应了我们期望的结果。在这种情况下，这是一个新狗被成功保存到数据库的确认。

然而，因为我们只是测试这段代码，所以在测试用例完成后，我们需要让数据库保持初始状态。因此，我们必须在每个测试用例之后删除输入到数据库中的每个新记录。我们可以用摩卡的`afterEach`钩子来实现这个。

我们使用 async/await 来处理异步路由处理功能。然后，我们使用 Chai(它正在使用`chai-http`)向服务器发送 POST 请求。我们可以使用`send`方法发送 POST 请求的主体。Chai 的`expect`函数用于断言响应等于我们所期望的。

当测试复杂得多的 API 端点时，您可以遵循相同的测试过程。如果测试涉及到从数据库中输入或检索数据，确保使用 Mocha 的`beforeEach`和`afterEach`函数输入和删除记录，使数据库保持初始状态。

# 对 Sinon 使用存根

存根被用作被测试组件使用的函数的临时替代。我们使用存根来模拟给定函数的行为。使用存根而不是函数的实际实现的原因因情况而异。当我们只想测试单个单元的行为时，存根在单元测试中特别有用。在集成测试中，当给定的函数尚未实现，但需要测试当前组件时，使用存根。当几个组件被绑定到另一个组件上进行集成测试时，如果我们想测试只有几个组件如何一起工作，我们可以使用存根来替换其中的一些组件。

假设上面的 POST `/dogs`路由有中间件检查发送请求的用户是否登录。(可以适当实现`isLoggedIn`函数。)

但目前，我们只想测试将狗的详细信息保存到数据库并发送响应的功能。换句话说，我们现在不想测试路由处理功能和中间件如何协同工作。所以我们可以创建一个存根来代替中间件功能。

我们使用名为 [Sinon](https://www.npmjs.com/package/sinon) 的 npm 包为 Node.js 程序创建存根。在继续之前，您应该继续将软件包安装到您的应用程序中。

我们使用 Sinon 的`callsFake`函数来创建中间件存根。我们在每个测试用例之前使用`beforeEach`钩子创建这个存根。

我们需要在每个测试用例之前重新导入 app 对象，所以我们也把它放在了`beforeEach`钩子中。在每个测试用例之后，我们需要恢复存根。

# 用 Nock 模仿 HTTP 请求

如果被测试的组件必须从外部 API 或服务中检索数据，它需要向这个 API/服务发送一个 HTTP 请求，并等待响应到达。如果我们没有显式地测试到这个 API 的连接性，不向外部 API 发送实际的请求可以减少测试时间，并保证测试不会因为不良的网络连接等原因而失败。如果我们没有向 API 发送实际的请求，我们需要在测试期间伪造这个请求，这种做法被称为模仿 HTTP 请求。

在本教程中，我们使用另一个名为 [Nock](https://www.npmjs.com/package/nock) 的 npm 模块来模拟对外部 API 的 HTTP 请求。它拦截外部请求，并允许我们返回定制的响应，以适应特定的测试用例。

假设我们的 API 有路由 GET `/dogs/:breed`。它通过向[狗 API](https://dog.ceo/dog-api/documentation/) 发送请求来返回给定狗品种的子品种。([https://dog.ceo/api/breed/](https://dog.ceo/api/breed/)/列表)。我们的应用服务器向 Dog API 发送 GET 请求，然后从 Dog API 返回的数据被发送回客户机。

我们使用包 [Superagent](https://www.npmjs.com/package/superagent) 向外部 API 发送请求。

它从 URL 中检索品种名称，并向 Dog API 发送 GET 请求以获取子品种。

现在，我们可以测试这条路线是否有问题。因为我们模拟了对外部 API 的请求，并在测试期间发送了一个定制响应，所以我们需要将这个定制响应保存在一个文件中，以便能够在任何时候检索它。我将它保存在测试目录中一个名为`response.js`的文件中。

让我们为上述路线编写测试用例。类似于我们之前所做的，这里我们定义了使用 Nock 在`beforeEach`钩子内发送的模拟 HTTP 请求。

当我们向`/dogs/:breed`路由发送 GET 请求时，Nock 拦截对 Dog API 的调用，并返回我们保存在文件中的自定义响应。我们可以适当地改变响应的输出，以用于不同的测试用例。

# 结论

在今天的教程中，我们讨论了集成测试和何时应用集成测试，以及两种常见但高级的测试实践:stubbing 和 mock。至此，我们的“Node.js 中的测试”系列文章就结束了。我们希望你喜欢这些教程，并且至少获得了如何为你的程序编写测试用例的基本概念。

感谢阅读！