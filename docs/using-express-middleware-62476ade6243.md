# 使用 Express 中间件

> 原文：<https://betterprogramming.pub/using-express-middleware-62476ade6243>

## 我们看看如何使用应用程序和路由级中间件

![](img/f25e1f21b44a6ed8d67e1ace0a8fc7bd.png)

照片由[康纳·杜根](https://unsplash.com/@duganphoto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/middle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

> 中间件功能是可以访问请求对象(`req`)、响应对象(`res`)以及应用程序请求-响应周期中的下一个中间件功能的功能— [快递单据](https://expressjs.com/en/guide/using-middleware.html)

在这一部分中，我们将看看 Express 中间件功能是做什么，以及我们如何使用它们。

# 中间件的特点

中间件功能可以运行任何代码，对请求和响应对象进行更改，结束请求-响应循环，并调用堆栈中的下一个中间件。

# 应用层中间件

当任何路由被调用时，我们可以通过将它们作为回调传递给`app.use`方法来运行中间件函数。

如果我们想让一个中间件函数只在一个请求方法的请求被调用时被调用，那么我们可以把它作为回调传递给`app.METHOD`方法，这里`METHOD`是`get`、`post`、`put`、`delete`等等。

例如，我们可以编写一个中间件函数，并将其传递给`app.use`来记录一个请求的请求方法，如下所示:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use((req, res, next) => {
  console.log(req.method);
  next();
});app.get('/', (req, res) => {
  res.json();
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/`发出 get 请求时，我们应该将`GET`记录下来。

我们可以将中间件功能限制为只在 GET 请求上运行，并限制在路径`/`上，方法是:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res, next) => {
  console.log(req.method);
  next();
});app.get('/', (req, res) => {
  res.json();
})app.post('/', (req, res) => {
  res.json();
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/`路径发出 GET 请求时，我们只看到记录的`GET`。

# 运行一系列中间件功能

我们可以使用`next`方法调用系列中的下一个中间件函数，因此我们可以使用它将中间件函数调用链接在一起。

例如，如果我们有…

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.use(
  (req, res, next) => {
    console.log('middleware 1 called');
    next();
  },
  (req, res, next) => {
    console.log('middleware 2 called');
    next();
  }
);app.get('/', (req, res) => {
  res.json();
})app.listen(3000, () => console.log('server started'));
```

…然后我们从每个中间件的`console.log`输出中看到:

```
middleware 1 called
middleware 2 called
```

如果我们在中间件中发送一个响应，那么下一个就不会被调用。例如，如果我们有:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))
app.get('/',
  (req, res, next) => {
    next()
  },
  (req, res, next) => {
    res.send('Second middleware');
  }
)app.get('/',  (req, res, next) => {
  res.end();
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/`发出请求时，我们得到输出`‘Second middleware’`。

我们的路线管理员在…

```
app.get('/', function (req, res, next) {
  res.end();
})
```

…没有被呼叫。

我们可以调用`next`，如下所示，直接进入我们的路由处理器，绕过其他中间件功能:

```
next('route');
```

例如，如果我们有:

```
const express = require('express')
const app = express()
app.use(express.json())
app.use(express.urlencoded({ extended: true }))
app.get('/:id',
  (req, res, next) => {
    if (req.params.id === '0') {
      next('route');
      return;
    }
    next();
  },
  (req, res, next) => {
    res.send('Second middleware');
  }
)app.get('/:id', (req, res, next) => {
  res.end(req.params.id);
})app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/0`发出请求时，我们得到`0`。否则，我们输出`‘Second Middleware’`。

# 路由器级中间件

路由器级中间件的工作方式与应用程序级中间件相同，但是它们被绑定到`express.Router()`的实例，而不是`express`实例。

例如，我们可以如下使用它:

```
const express = require('express')
const app = express()
const router = express.Router();app.use(express.json())
app.use(express.urlencoded({ extended: true }))router.use((req, res, next) => {
  req.requestTime = new Date();
  next();
})router.get('/', (req, res, next) => {
  res.json(req.requestTime);
})app.use('/', router);app.listen(3000, () => console.log('server started'));
```

然后，当我们向`/`路由发出请求时，我们得到时间戳作为输出。

链接中间件和跳过路由的工作方式与应用级中间件相同。例如，我们可以编写以下代码来链接中间件:

```
const express = require('express')
const app = express()
const router = express.Router();app.use(express.json())
app.use(express.urlencoded({ extended: true }))router.use(
  (req, res, next) => {
    console.log('middleware 1 called');
    next();
  },
  (req, res, next) => {
    console.log('middleware 2 called');
    next();
  }
)router.get('/', (req, res, next) => {
  res.json();
})app.use('/', router);app.listen(3000, () => console.log('server started'));
```

然后我们从每个路由中间件的`console.log`输出中看到:

```
middleware 1 called
middleware 2 called
```

我们可以使用`next('route')`跳到路线，如下所示:

```
const express = require('express')
const app = express()
const router = express.Router();app.use(express.json())
app.use(express.urlencoded({ extended: true }))router.get('/:id',
  (req, res, next) => {
    if (req.params.id === '0') {
      next('route');
      return;
    }
    next();
  },
  (req, res, next) => {
    res.send('Second middleware');
  }
)router.get('/:id', (req, res, next) => {
  res.end(req.params.id);
})app.use('/', router);app.listen(3000, () => console.log('server started'));
```

然后当我们向`/0`发出请求时，我们得到`0`。否则，我们输出`‘Second Middleware’`。

# 结论

使用快速中间件很简单。我们可以将它们传递给`app.use`以使中间件为所有请求方法运行，或者传递给`app.METHOD`以使它们为给定方法运行。

我们可以调用`next`调用下一个中间件，调用`next('route')`直接从一个中间件调用路由处理器。

除了分别绑定到`express.Router()`和`express()`之外，一切都适用于路由和应用级中间件。