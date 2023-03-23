# 如何用 Express 构建反向代理

> 原文：<https://betterprogramming.pub/how-to-build-a-reverse-proxy-with-express-87a31ec2ec80>

## 一种快速简单的构建 API 网关的方法

![](img/2649506b389b7738201d2eca1ca54efb.png)

如果您的分布式系统使用微服务架构，那么就需要一个 API 网关。构建 API 网关的一个好方法是使用反向代理。Node.js 是一个很好的反向代理，因为它很快，并且有很多库可以用来做反向代理。

Express 是构建 web 应用程序最流行的框架。这对于构建反向代理来说也是非常好的，因为有附加组件来表示可以为反向代理进行路由。

设计将非常简单。它只会根据请求的 URL 将流量重定向到我们不同的后端应用程序。此外，除了请求有效负载之外，它还必须能够将头数据、文件和 cookies 传递到我们的服务中。

为了做到这一切，我们首先按照[https://expressjs.com/en/starter/generator.html](https://expressjs.com/en/starter/generator.html)的说明运行快速应用生成器来搭建我们的应用

我们必须运行`npx express-generator`来生成我们需要的代码。

我建议使用`nodemon`在开发环境中运行我们的应用程序，这样每当我们的代码改变时，它就会重新启动。

接下来，我们必须安装一些软件包来完成反向代理功能，并启用 CORS，以便前端应用程序可以使用反向代理。为此，运行`npm i express-http-proxy glob node-env-file cookie-parser babel-register body-parser`。

`express-http-proxy`是 HTTP 反向代理库。`cors`是支持 CORS 的 Express 的附加组件。`cookie-parser`是一个允许 Express 解析 cookies 的插件。`babel-register`允许我们使用最新的 JavaScript 特性。`node-env-file`允许我们使用`.env`文件存储环境变量。`body-parser`将用于检查多部分请求。多部分请求是包含文件的请求。带有文件的请求在重定向前不会通过`body-parser`。

现在我们准备写代码了。我们在自己创建的`helpers`文件夹中创建一个名为`helper.js`的新文件。

在那里，我们添加:

```
module.exports = {
    isMultipartRequest: (req) => {
        let contentTypeHeader = req.headers['content-type'];
        return contentTypeHeader && contentTypeHeader.indexOf('multipart') > -1;
    }
}
```

该函数检查多部分请求，即作为表单数据发送的请求。它可以包括文本或文件。

在`app.js`中，我们写道:

```
require("babel-register");
let express = require('express');
let cors = require('cors')
let config = require('./config/config');
let env = require('node-env-file');
let helpers = require('./app/helpers/helpers');
let bodyParser = require('body-parser');
env(__dirname + '/.env');let app = express();app.use(cors({
  credentials: true,
  origin: true
}));const bodyParserJsonMiddleware = function () {
  return function (req, res, next) {
    if (helpers.isMultipartRequest(req)) {
      return next();
    }
    return bodyParser.json()(req, res, next);
  };
};app.use(bodyParserJsonMiddleware());app.all('*', (req, res, next) => {
  let origin = req.get('origin');
  res.header('Access-Control-Allow-Origin', origin);
  res.header("Access-Control-Allow-Headers", "X-Requested-With");
  res.header('Access-Control-Allow-Headers', 'Content-Type');
  next();
});module.exports = require('./config/express')(app, config);app.listen(config.port, () => {
  console.log('Express server listening on port ' + config.port);
});
```

该代码通过传递服务器端 cookies 来工作，并允许多方请求不通过`body-parser`，因为它不是 JSON。

此外，我们允许 CORS 在下面的块中:

```
app.all('*', (req, res, next) => {
  let origin = req.get('origin');
  res.header('Access-Control-Allow-Origin', origin);
  res.header("Access-Control-Allow-Headers", "X-Requested-With");
  res.header('Access-Control-Allow-Headers', 'Content-Type');
  next();
});
```

它允许接受来自所有来源的请求。默认情况下，只接受来自与后端相同的主机的请求，因为允许来自其他主机的请求是不安全的。然而，如果我们允许移动和独立的前端 web 应用程序通过我们的反向代理发出请求，我们必须允许所有来源。它从标头中获取来源，并允许来自该来源的请求继续进行。

然后我们在`controllers/home.js`中添加代理请求的代码:

```
const express = require('express');
let proxy = require("express-http-proxy");
let helpers = require('../helpers/helpers');let loginAppRoutes = [
  '/login*',
  '/loginms',
  '/register',
  '/resetPassword',
  '/getActivationKey*',
  '/activateAccount',
  '/logout',
  '/reports',
  '/'
]let uploadRoutes = [
  '/uploadlogo*',
]module.exports = (app) => {
  const proxyMiddleware = () => {
    return (req, res, next) => {
      let reqAsBuffer = false;
      let reqBodyEncoding = true;
      let contentTypeHeader = req.headers['content-type'];
      if (helpers.isMultipartRequest(req)) {
        reqAsBuffer = true;
        reqBodyEncoding = null;
      }
      return proxy(process.env.UPLOAD_URL, {
        reqAsBuffer: reqAsBuffer,
        reqBodyEncoding: reqBodyEncoding,
        parseReqBody: false,
        proxyReqOptDecorator: (proxyReq) => {
          return proxyReq;
        }, proxyReqPathResolver: (req) => {
          return `${process.env.UPLOAD_APP_URL}/${req.baseUrl}${req.url.slice(1)}`;
        }, userResDecorator: (rsp, data, req, res) => {
          res.set('Access-Control-Allow-Origin', req.headers.origin);
          return data.toString('utf-8');
        }
      })(req, res, next);
    };
  } uploadRoutes.forEach(r => {
    app.use(r, proxyMiddleware());
  }) loginAppRoutes.forEach(r => {
    app.use(r, proxy(process.env.LOGIN_URL, {
      proxyReqOptDecorator: (proxyReq) => {
        return proxyReq;
      },proxyReqPathResolver: (req) => {
        return `${process.env.LOGIN_URL}/${req.baseUrl}${req.url.slice(1)}`;
      }, userResDecorator: (rsp, data, req, res) => {
        res.set('Access-Control-Allow-Origin', req.headers.origin);
        return data.toString('utf-8');
      }
    }));
  })
};
```

我们通过以下方式检查多部分表单请求:

```
if (helpers.isMultipartRequest(req)) {
  reqAsBuffer = true;
  reqBodyEncoding = null;
}
```

会将带有文件的请求直接传递给我们的内部 API。

以下内容:

```
proxyReqPathResolver: (req) => {
  return `${process.env.LOGIN_URL}/${req.baseUrl}${req.url.slice(1)}`;
},
```

是设置从代理到内部 API 的实际重定向 URL 的地方。注意，`parseReqBody`是`false`，所以多部分表单请求不会被解析为 JSON。

`process.env`变量设置在`.env`文件中。

在`.env`文件中，我们有:

```
LOGIN_URL='http://urlForYourLoginApp'
UPLOAD_URL='http://anotherUrlForYourUploadApp'
```

借助反向代理插件，Express 是在 Node 中构建反向代理的最佳选择之一。

**在推特上关注我:**[**https://twitter.com/AuMayeung**](https://twitter.com/AuMayeung)