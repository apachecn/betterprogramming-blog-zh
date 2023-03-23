# 如何在剧作家和 JavaScript 中构造自动化测试的 API 调用

> 原文：<https://betterprogramming.pub/how-to-structure-api-calls-for-automation-tests-in-playwright-javascript-d3bf9cb670e1>

## 创建可读和灵活的 API

![](img/a769dcaa9d2dd9af29194f9e98bc2f0c.png)

托拜厄斯·凯勒在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

为复杂系统创建 e2e 自动化测试有许多困难。其中之一就是处在一个“完美”的环境中。这个环境必须完全在您的控制之下，并且包括开发或生产环境的许多(如果不是全部)特征。实际情况往往并非如此。这种环境需要定期监督、数据重置和对每个测试要求的动态配置。

在使用 REST 架构和 API 请求的在线应用程序的情况下，我们可以避开这个问题，并在每个测试或测试套件之前在必要的条件下设置系统。

对于这个例子，我将使用[剧作家](https://playwright.dev/)和它提供的用于发送 API 请求的请求模块。

假设我们正在测试的应用程序是一个简单的产品商店。JWT(JSON Web Token)技术用于授权。因此，登录后的每个后续调用都必须包含给定的令牌。

特定产品必须有库存，某些测试才能进行。因此，用户在收到令牌并使用它来增加某个产品的库存之前，必须首先进行身份验证。

# “快速和肮脏”的方法

在指定文件或测试套件中的所有测试之前运行的`beforeAll`部分中发送调用，是显而易见的做法。下面是如何做到这一点:

```
test.beforeAll(async (request) => {
 let response = await request.post(`https://someapp.com/api/login`, {
  headers: this.standardHeaders,
  data: {
   username: "USERNAME",
   password: "PASSWORD",
  },
 })

 let { accessToken, refreshToken } = JSON.parse(await response.text())

 let setupCall = await request.post(`https://someapp.com/product/00001`, {
  headers: {
   authorization: `Bearer ${accessToken}`,
  },
  data: {
   stock: 10,
  },
 })

 if (setupCall.status() == 200) {
  console.log("Product is now setup correctly")
 }
})

test("Test that requires product with id 00001 in stock", async (request) => {
 // TEST ITSELF RUNS HERE
})
```

这种策略有几个缺点。最明显的一个是代码可读性，因为测试中充满了与其主要操作无关的不必要的代码。第二个是代码冗余，因为产品设置功能将要求每次测试都重复代码。对于十次测试，用户必须登录十次，浪费时间和资源并导致性能问题。

# 经典 OOP 方法和异步问题

像许多其他架构问题一样，这个问题可以通过利用 OOP 概念来解决。通过开发一个类来为测试构建数据，提供了一种访问 API 的方式，这种方式是标准化的、可重复的和可伸缩的。

这个类看起来会像这样:

```
class SetupCalls {
 constructor(request) {
  this.baseUrl = "https://someapp.com"
  this.accessToken = null
  this.refreshToken = null
 }

 async getToken(username, password) {
  this.request = await request.newContext()
  let response = await this.request.post(`${this.baseUrl}/api/login`, {
   headers: this.standardHeaders,
   data: {
    username: username,
    password: password,
   },
  })
  let { accessToken, refreshToken } = JSON.parse(await response.text())
  this.accessToken = accessToken
  this.refreshToken = refreshToken
 }

 async setStock(productId) {
  let setupCall = await this.request.post(`${this.baseUrl}/product/${productId}`, {
   headers: {
    authorization: `Bearer ${this.accessToken}`,
   },
   data: {
    stock: 10,
   },
  })

  if (setupCall.status() == 200) {
   console.log("Product is now setup correctly")
  }
 }
}I
```

现在这些函数已经被有效地封装了，理解每个函数的功能是非常简单的。可以使用同一个令牌进行多次调用，因为它是在类本身中共享的，这样可以节省重复登录的时间。让我们看看测试当前的样子:

```
test.beforeAll(async () => {
   let setupCalls = new SetupCalls()
   await setupCalls.getToken('John', 'john123')
   await setupCalls.setStock("00001")
   await setupCalls.setStock("00002")
   await setupCalls.setStock("00003")
})

test("Test that requires product with id 00001 in stock", async (request) => {
 // TEST ITSELF BEGINS
})
```

尽管实施情况看起来很好，但仍存在一些问题。在使用安装类之前，用户必须了解它的具体工作方式。如果在调用所有其他方法之前没有调用`getToken`方法，测试将会失败，因为令牌将不会被填充。

对令牌`getToken`的调用最好在构造函数中进行，然而，构造函数不能是异步函数。因此，使用`Promises`似乎是答案。

```
constructor() {
  this.baseUrl = "https://someapp.com"
  let {accessToken, refreshToken} = Promise.resolve(getToken()).then(res => {
       // do something with the token
    });
 }j
```

然而，这改变了构造函数的正常行为，需要仔细保存这个上下文。这种方法破坏了使用异步等待的目标，即保持代码可读和简单。

# 解决方案—具有工厂功能的面向对象程序设计

代替传统的构造函数，我们可以用[标准静态函数/方法](https://dev.to/somedood/the-proper-way-to-write-async-constructors-in-javascript-1o8c)初始化一个对象，并在其中使用异步方法，而不会失去后者提供给我们的任何优势。

静态方法不是在类的实例上调用，而是在类本身上调用。将构造函数设为私有可以确保用户使用 init 而不是默认构造函数初始化对象。类的 init 方法只给了该类必要的信息，如访问令牌，该方法创建了一个新的类实例。

让我们看看代码:

```
class SetupCalls {
 /**
  * @private
  */
 constructor(config) {
  this.baseUrl = "https://someapp.com"
  this.accessToken = config.accessToken
  this.refreshToken = config.refreshToken
 }

 static async init(username, password) {
  let config = await this.getToken(username, password)
  return new SetupCalls(config)
 }

 async getToken(username, password) {
  this.request = await request.newContext()
  let response = await this.request.post(`https://someapp.com/api/login`, {
   data: {
    username: username,
    password: password,
   },
  })
  return JSON.parse(await response.text())
 }

 async setStock(productId) {....}
}
```

现在，测试更加简化，在任何情况下都采用异步/等待:

```
test.beforeAll(async () => {
 let setupCalls = await SetupCalls.init("John", "John1213!")
 await setupCalls.setStock("00001")
})

test("Test that requires product with id 00001 in stock", async (request) => {
 // TEST ITSELF BEGINS
})
```

# 最后的润色和改进

在测试运行之前设置数据通常是可能的。`playwright.config.js`配置文件中的`globalSetup`标志允许我们为处理全局设置的文件指定一个位置:

```
const config = {
 globalSetup: require.resolve("./globalSetup"),
 testDir: "./tests",
 timeout: 80 * 1000,
 expect: {
  timeout: 5000,
 },
...
}
```

现在我们已经构建了类，文件可以调用它，并在一个函数中设置整个数据集。我们甚至可以将所有的调用设置为并行运行，以加快测试速度，因为发布产品的调用是不相关的。这就是`globalConfig.js`文件的样子:

```
module.exports = async (config) => {
 let setupCalls = await SetupCalls.init(config.username, config.password)
 let dataset = ["00001", "00002", "00003", "00004"]

 const responses = await Promise.all(
  dataset.map(async (id) => {
   const res = await setupCalls.setStock(id)
  })
 )

 console.log(responses)
}
```

# 结论

如果您正在进行 API 测试，或者只是利用 API 进行数据准备或其他自动化，那么最好是在提供可读性和结构的同时，以支持灵活性的方式来安排调用。

由于其异步特性，JavaScript 有时可能会令人困惑。因此，在保持性能的同时保持事情的简单明了可能是合格代码和优秀代码之间的区别。