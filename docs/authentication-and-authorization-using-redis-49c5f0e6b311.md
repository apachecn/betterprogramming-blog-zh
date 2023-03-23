# 使用 Redis 进行身份验证和授权

> 原文：<https://betterprogramming.pub/authentication-and-authorization-using-redis-49c5f0e6b311>

![](img/45e635a82f7dbbdec1088ec4a0ff070b.png)

本文是 [***系列文章***](https://medium.com/@pankaj.panigrahi/list-of-node-js-articles-ededa6dd304b) 的第 6 篇，它将帮助您掌握 Node.js 背后的不同概念，并使您能够创建生产就绪的应用程序。这篇文章期望读者了解巴别塔以及如何设置它。如果你需要了解相同的内容，请阅读这篇文章。

当你为你的网站/网络应用构建后端或者一个通用的后端来作为移动和网络应用的后端时，你需要用一个认证层来保护你的 API。这些 API 应该只由登录的用户访问，并且应该根据用户返回结果。

在当今时代，每个人都在寻求基于令牌的认证，而不是基于会话的 cookies。OAuth、JWT 和带有 memcached / redis 的随机令牌是最流行的基于令牌的身份验证形式。
这里有一个不错的电子表格，比较了一些常见的身份验证方法:

[](https://docs.google.com/spreadsheets/d/1tAX5ZJzluilhoYKjra-uHbMCZraaQkqIHl3RIQ8mVkM/edit#gid=0) [## API 的认证技术

### 描述将用户名和密码与每个带有用户信息的请求签名或加密的 cookie 一起传递。通常…

docs.google.com](https://docs.google.com/spreadsheets/d/1tAX5ZJzluilhoYKjra-uHbMCZraaQkqIHl3RIQ8mVkM/edit#gid=0) 

免责声明:电子表格由其他作者编写。

JWT 和带内存存储的随机令牌各有优缺点。您应该根据项目的需求来使用它们。您可以在下面的文章中找到两者的对比:

[](https://dzone.com/articles/stop-using-jwts-as-session-tokens) [## 停止使用 jwt 作为会话令牌

### JSON Web 令牌(jwt)现在非常热门。它们在 web 开发中风靡一时，因为它们时尚、安全…

dzone.com](https://dzone.com/articles/stop-using-jwts-as-session-tokens) 

说到 redis 和 memcached，我更喜欢 redis，原因如下:

a)支持更多数据类型

b)默认磁盘持久性

c)更大的键值大小

您可以在下面的文章中了解更多信息:

[https://www . LinkedIn . com/pulse/memcached-vs-redis-which-one-pick-ran Jeet-vimal/](https://www.linkedin.com/pulse/memcached-vs-redis-which-one-pick-ranjeet-vimal/)

既然我们已经决定借助 redis 实现基于令牌的身份验证，那么让我们开始吧。

***Redis*** 是一个开源的(BSD 许可的)、内存中的数据结构存储，用作数据库、缓存和消息代理。它支持诸如字符串、散列、列表、集合、带有范围查询的排序集合、位图等数据结构。redis 最流行的两种用法是构建缓存层或充当会话存储系统。

请按照以下文章开始使用 redis。

[](https://redis.io/topics/introduction) [## Redis 介绍

### Redis 是一个开源的(BSD 许可的)，内存中的数据结构存储，用作数据库，缓存和消息代理…

redis.io](https://redis.io/topics/introduction) [](https://redis.io/topics/quickstart) [## 快速入门

### 建议安装 Redis 的方法是从源代码中编译它，因为 Redis 除了一个工作…

redis.io](https://redis.io/topics/quickstart) [](https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/) [## 第 1 章:了解 Redis | Redis 实验室

### 第一章:了解 Redis 这一章讲述了 Redis 是怎样的，与你用过的其他软件不同，如何使用…

redislabs.com](https://redislabs.com/ebook/part-1-getting-started/chapter-1-getting-to-know-redis/) 

在本文中，我们将使用 redis 的本地安装。但是你也可以从 redis 实验室得到一个免费的服务器。

[](https://redislabs.com/get-started/) [## Redis Enterprise | Redis Labs 入门

### 选择您的部署选项云托管注册部署 Redis 数据库的最快方法。全面管理…

redislabs.com](https://redislabs.com/get-started/) 

让我们从第五篇文章中离开的地方开始。建议你先看第 5 篇，再看这篇文章。但是，如果您对 node.js 有一些经验，并且能够找到自己的代码，您可以从下面的样板文件开始:

> 您可以在此处找到代码:
> 
> https://github.com/pankaj805/medium-05_mongo_client

在我们的项目中，我们将使用以下 npm 模块作为 redis 客户端库

[](https://www.npmjs.com/package/redis) [## 雷迪斯

### Redis 客户库

www.npmjs.com](https://www.npmjs.com/package/redis) 

因此，让我们首先在项目中安装以下库。

```
npm install redis --save
```

和另一个库来创建随机的唯一 id

```
npm install node-uuid --save
```

在本教程中，我们将构建两个 api:第一个 api 是“登录”api，它应该返回一个新的会话令牌，其他 API 将更新密码，这将需要登录用户的有效会话令牌。

让我们首先添加一个服务方法来更新用户的密码

```
export const updateUserPassword = (db, userName,pwd) => {
  return db.collection('user').updateOne({'username': userName }, {
    $set: {password:pwd} 
  })
  .then((r) => {
    return Promise.resolve(r.matchedCount);
  })
  .catch((err) => {
    return Promise.reject(err);
  })
}
```

所以最后 *services/UserService.js* 应该是这样的:

```
export const getUserDetails = (db, userName) => {
   return new Promise((resolve, reject) =>
      db.collection('user')
        .find({ 'username': userName })
        .toArray((err, docs) => {
           if(docs && docs.length>0){
              resolve(docs[0]);
           }else{
              reject();
           }
      });
   });
}export const updateUserPassword = (db, userName,pwd) => {
  return db.collection('user').updateOne({'username': userName }, {
    $set: {password:pwd} 
  })
  .then((r) => {
    return Promise.resolve(r.matchedCount);
  })
  .catch((err) => {
    return Promise.reject(err);
  })
}
```

让我们创建一个用来存储会话数据的类。让我们在公共文件夹中创建一个文件。

```
class Session {
  constructor() {
    this.userData = {};
    this.sessionID = '';
  }

  set(obj) {
    this.userData = obj;
  } save(client){
    if(this.sessionID){
      client.set(this.sessionID, JSON.stringify(this.userData));
      client.expire(this.sessionID, 60 * 60 * 2);
    }
  } destroy(client) {
    client.del(this.sessionID);
  }
}export default Session;
```

除了**保存**和**销毁**方法之外，这个文件非常简单明了。save 方法被传递了一个参数，该参数应该是对 redis 客户机实例的引用。

```
client.set(this.sessionID, JSON.stringify(this.userData));
```

上面的行用于用用户数据更新 redis 键-值对。

```
client.expire(this.sessionID, 60 * 60 * 2);
```

而 client.del()就是从 redis 中删除某个键值对。

现在让我们编辑我们的 common/authUtils.js 文件来处理所需的场景。

```
import uuid from 'node-uuid';const newSessionRoutes = [{ path: '/user/login', method: 'POST' }];const authRoutes = [{ path: '/user/password', method: 'PUT' }];
```

导入 uuid 模块并定义两个常量，第一个常量包含需要新会话的 api 方法数组，第二个常量包含需要有效会话的 api 方法数组。

```
export const isNewSessionRequired = (httpMethod, url) => {
  for (let routeObj of newSessionRoutes) {
    if (routeObj.method === httpMethod && routeObj.path === url) {
      return true;
    }
  } return false;
}export const isAuthRequired = (httpMethod, url) => {
  for (let routeObj of authRoutes) {
    if (routeObj.method === httpMethod && routeObj.path === url) {
      return true;
    }
  }
  return false;
}
```

添加以上两个方法来检查传递的 api 需要新的会话还是旧的会话。

```
export const generateRandomSessionID = () => {
  return uuid.v4();
}
```

添加上述方法来创建随机 uuid 字符串。我们将使用它为 redis 键-值对创建一个惟一的键。

我们还需要一个从 redis 服务器读取值的方法。根据文档，我们需要执行以下操作:

```
client.get("redis-key", function (err, reply) {
  console.log(reply.toString());
});
```

让我们定义一个方法，将上述方法封装在一个承诺中:

```
export const getRedisSessionData = (redisClient,sessionId) => {
  return new Promise((resolve, reject) => {
    redisClient.get(sessionId, function (err, data) {
      if (err) {
        reject(err);
      }
      resolve(data);
    });
  })
}
```

因此，我们的 authUtils 文件应该如下所示:

```
import {getClientDetails} from '../services/ClientService';
import uuid from 'node-uuid';const newSessionRoutes = [{ path: '/user/login', method: 'POST' }];
const authRoutes = [{ path: '/user/password', method: 'PUT' }];export const clientApiKeyValidation = async (req,res,next) => {
   let clientApiKey = req.get('api_key');if(!clientApiKey){
      return res.status(400).send({
         status:false,
         response:"Missing Api Key"
      });
   }try {
      let clientDetails = await getClientDetails(req.db, clientApiKey);
      if (clientDetails) {
         next();
      }
   } catch (e) {
      console.log('%%%%%%%% error :', e);
      return res.status(400).send({
         status: false,
         response: "Invalid Api Key"
      });
   }}export const isNewSessionRequired = (httpMethod, url) => {
  for (let routeObj of newSessionRoutes) {
    if (routeObj.method === httpMethod && routeObj.path === url) {
      return true;
    }
  }return false;
}export const isAuthRequired = (httpMethod, url) => {
  for (let routeObj of authRoutes) {
    if (routeObj.method === httpMethod && routeObj.path === url) {
      return true;
    }
  }
  return false;
}export const generateRandomSessionID = () => {
  return uuid.v4();
}export const getRedisSessionData = (redisClient,sessionId) => {
  return new Promise((resolve, reject) => {
    redisClient.get(sessionId, function (err, data) {
      if (err) {
        reject(err);
      }
      resolve(data);
    });
  })
}
```

在开始下一个文件之前，这里有一些注意事项:

这不是生产质量代码。我们过分简化了事情，忽略了一些最佳实践。这里列出了一些过于简单的东西，限制了本文的重点:

API 授权配置已放入常量变量中。理想情况下，它们应该从数据库中获取，或者应该放在 json 文件中。甚至配置也可能更复杂，以处理基于用户角色的身份验证。

**ii)** 密码应以加密方式保存。这可以通过使用 bcrypt 等各种 npm 库轻松实现

现在让我们编辑 app.js 文件。

```
import {clientApiKeyValidation} from './common/authUtils';
```

将上面的行更新如下:

```
import { clientApiKeyValidation, isNewSessionRequired, isAuthRequired, generateRandomSessionID, getRedisSessionData } from './common/authUtils';
```

和另外两个进口货

```
import redis from 'redis';
import Session from './common/Session';
```

在 mongo connect 代码之前/之后添加以下代码。

```
let redisClient = null;
redisClient = redis.createClient({
  prefix: 'node-sess:',
  host: 'localhost'
});
```

我们正在实例化一个 redis 客户端连接，并将其分配给一个全局变量。前缀值将用于该客户端写入/读取的所有密钥。这里使用的默认端口是 6379。

现在，在我们注入了 **clientApiKeyValidation 的那一行之后添加下面的代码。**

```
app.use(async (req, res, next) => {
  var apiUrl = req.originalUrl;
  var httpMethod = req.method;if (isNewSessionRequired(httpMethod, apiUrl)) {
    let sessionID = generateRandomSessionID()
    req.session = new Session();
    req.session.sessionID = sessionID;
    req.sessionID = sessionID;
  } else if (isAuthRequired(httpMethod, apiUrl)) {
    let sessionID = req.header('Authorization');
    if (sessionID) {
      let redisData = await getRedisSessionData(redisClient, sessionID);
      if (redisData) {
        redisData = JSON.parse(redisData);
        req.session = new Session();
        req.sessionID = sessionID;
        req.session.sessionID = sessionID;
        req.session.userData = redisData;
      } else {
        return res.status(401).send({
          ok: false,
          error: {
            reason: "Invalid Sessiontoken",
            code: 401
          }
        });
      }
    } else {
      return res.status(401).send({
        ok: false,
        error: {
          reason: "Missing Sessiontoken",
          code: 401
        }
      });
    }
  }
  next();
})
```

通过上面的代码，我们注入了另一个请求处理器，它将为所有 API 处理与会话相关的东西。首先，我们检查被调用的 api 是否需要一个新的会话。在这种情况下，我们创建一个随机的 uuid 和新的会话对象，并将其指定为 express request 对象的一个属性。但是我们还没有把这些数据发送到 redis 服务器上。我们以后再做。

```
let sessionID = generateRandomSessionID()
req.session = new Session();
req.session.sessionID = sessionID;
req.sessionID = sessionID;
```

在另一个条件下，我们检查 api 是否需要一个有效的会话。这里有一点非常重要。通常的做法是以下列方式传递授权令牌报头:

```
Authorization: Bearer a1194981-b541-416e-935
```

头字段应该是“Authorization ”,值的第一部分应该是关键字“Bearer ”,然后由空格分隔的第二部分应该包含令牌。

这是 W3C 给出的一个标准。关键字“持票人”被用来区别于现有的基本认证。**但在本文中，我们将使用自定义标题，只是为了展示我们也可以使用自定义标题字段。我们将在下一篇文章中使用不记名授权头。**

因此，我们希望客户端在请求头中发送会话令牌，字段名为 ***sessiontoken***

```
let sessionID = req.header('sessiontoken');
```

如果 sessionID 丢失，我们将在 api 中返回一个错误。但是如果 sessionID 出现在头中，我们尝试访问 redis 服务器并获取传递的键的记录。

```
let redisData = await getRedisSessionData(redisClient, sessionID);
```

如果 redisData 为 null，那么我们将在 api 中返回一个错误，指出令牌无效。如果会话过期并从 redis 服务器中删除，或者在头中传递了随机无效值，就会发生这种情况。

但如果我们从 redis 获取用户信息。我们解析相同的数据。{我们将在 redis 中以字符串形式存储我们的数据}。除了保留旧的值之外，我们为新的会话做了类似的事情。

```
 redisData = JSON.parse(redisData);
        req.session = new Session();
        req.sessionID = sessionID;
        req.session.sessionID = sessionID;
        req.session.userData = redisData;
```

现在，在我们所有的路由注入之后，让我们也为所有的 API 添加一个响应处理程序。

```
app.use((req, res, next) => {
  if (!res.data) {
    return res.status(404).send({
      ok: false,
      error: {
        reason: "Invalid Endpoint", code: 404
      }
    });
  } if (req.session && req.sessionID) {
    try {
      req.session.save(redisClient);
      res.setHeader('sessiontoken', req.sessionID);
      res.data['sessiontoken'] = req.sessionID;
    } catch (e) {
      console.log('Error ->:', e);
    }
  }
  res.status(res.statusCode || 200)
    .send({ ok: true, response: res.data });
})
```

在这个响应处理程序中。我们首先检查响应对象是否具有**数据**属性。如果缺少数据属性，我们将在 api 响应中发送 404 错误。

但是，如果设置了数据，我们会以定义好的格式发送响应，并附带所需的 http 状态代码。

```
res.status(res.statusCode||200).send({ok: true, response:res.data});
```

如果快速请求对象有一个 sessionID 字段，我们将执行两个操作:

首先，我们使用 Session.save()方法将这些数据保存在 redis 服务器中。

**ii)** 我们将 sessiontoken 值添加到响应报头和响应有效载荷中。

因此，我们的 app.js 代码如下所示:

```
import express from 'express';
import bodyParser from 'body-parser';
import user from './routes/user';
import {MongoClient} from 'mongodb';
import { clientApiKeyValidation, isNewSessionRequired, isAuthRequired, generateRandomSessionID, getRedisSessionData } from './common/authUtils';
import redis from 'redis';
import Session from './common/Session';const CONN_URL = 'mongodb://localhost:27017';let mongoClient = null;MongoClient.connect(CONN_URL,{ useNewUrlParser: true }, function (err, client) {
   mongoClient = client;
})let redisClient = null;
redisClient = redis.createClient({
  prefix: 'node-sess:',
  host: 'localhost'
});let app = express();// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }));// parse application/json
app.use(bodyParser.json());app.use((req,res,next)=>{
   req.db = mongoClient.db('test');
   next();
})app.get('/',(req,res,next)=>{
    res.status(200).send({
      status:true,
      response:'Hello World!'
    });
});app.use(clientApiKeyValidation);app.use(async (req, res, next) => {
  var apiUrl = req.originalUrl;
  var httpMethod = req.method;if (isNewSessionRequired(httpMethod, apiUrl)) {
    let sessionID = generateRandomSessionID()
    req.session = new Session();
    req.session.sessionID = sessionID;
    req.sessionID = sessionID;
  } else if (isAuthRequired(httpMethod, apiUrl)) {
    let sessionID = req.header('sessiontoken');
    if (sessionID) {
      let redisData = await getRedisSessionData(redisClient, sessionID);
      if (redisData) {
        redisData = JSON.parse(redisData);
        req.session = new Session();
        req.sessionID = sessionID;
        req.session.sessionID = sessionID;
        req.session.userData = redisData;
      } else {
        return res.status(401).send({
          ok: false,
          error: {
            reason: "Invalid Sessiontoken",
            code: 401
          }
        });
      }
    } else {
      return res.status(401).send({
        ok: false,
        error: {
          reason: "Missing Sessiontoken",
          code: 401
        }
      });
    }
  }
  next();
})app.use('/user',user);app.use((req, res, next) => {
  if (!res.data) {
    return res.status(404).send({
      ok: false,
      error: {
        reason: "Invalid Endpoint", code: 404
      }
    });
  }if (req.session && req.sessionID) {
    try {
      req.session.save(redisClient);
      res.setHeader('sessiontoken', req.sessionID);
      res.data['sessiontoken'] = req.sessionID;
    } catch (e) {
      console.log('Error ->:', e);
    }
  }
  res.status(res.statusCode || 200)
    .send({ ok: true, response: res.data });
})app.listen(30006,()=>{
   console.log(' ********** : running on 30006');
})process.on('exit', (code) => {
   mongoClient.close();
   console.log(`About to exit with code: ${code}`);
});process.on('SIGINT', function() {
   console.log("Caught interrupt signal");
   process.exit();
});module.exports = app;
```

现在让我们转到我们的 **routes/user.js** 文件。在添加两个新的 api 路由之前，让我们编辑前面的路由" */hello* "以使用新的响应处理程序。

早期的代码如下所示:

```
router.post('/hello',async (req,res)=>{
  let uname = req.body.username;
  let userDetails = await getUserDetails(req.db,uname)
  res.status(200).send({
    status:true,
    response:userDetails
  });
});
```

在成功检索数据后，我们通常直接发送响应。现在让我们将上面的方法编辑成下面的方法:

```
router.post('/hello', async (req, res, next) => {
  let uname = req.body.username;
  let userDetails = await getUserDetails(req.db, uname)
  res.data = {
    status: true,
    response: userDetails
  };
  next();
});
```

这里我们只是设置响应对象的数据属性并调用 next()方法。

更新 import 语句，使其也包含 updateUserPassword 方法

```
import { getUserDetails, updateUserPassword } from '../services/UserService';
```

登录 api 的代码如下:

```
router.post('/login', async (req, res, next) => {
  let uname = req.body.username;
  let pwd = req.body.password;
  let userDetails = await getUserDetails(req.db, uname);
  if (userDetails) {
    let { password } = userDetails;
    if (pwd === password) {
      res.data = {
        status: true,
        response: userDetails
      };
      req.session.set(userDetails);
    } else {
      res.statusCode = 400;
      res.data = {
        status: false,
        error: 'Invalid Password'
      };
    }
  } else {
    res.statusCode = 400;
    res.data = {
      status: false,
      error: 'Invalid Username'
    };
  }
  next();
});
```

在这里，我们还可以添加另一个条件来检查请求数据中是否缺少用户名或密码。首先，我们尝试通过传递用户名来获取用户的详细信息。如果数据丢失，我们会发送一条错误消息，声明用户名无效。否则我们尝试比较密码。如果密码不匹配，我们会发送一个错误。否则，我们更新响应数据对象和请求会话对象中的用户数据。由于请求会话对象现在有了 userdata，在我们的响应处理程序中调用的 save()函数将把数据保存到我们的 redis 中。

现在让我们为更新密码 api 添加代码。

```
router.put('/password', async (req, res, next) => {
  try {
    let oldPwd = req.body.old_password;
    let newPwd = req.body.new_password;
    if (!oldPwd && !newPwd) {
      res.statusCode = 400;
      res.data = {
        status: false,
        error: 'Invalid Parameters'
      }
    }
    let uname = req.session.userData.username;
    let userDetails = await getUserDetails(req.db, uname);
    if (oldPwd !== userDetails.password) {
      res.statusCode = 400;
      res.data = {
        status: false,
        error: "Old Password doesn't match"
      }
    } else {
      let updateRes = await updateUserPassword(req.db,uname,newPwd)
      res.data = {
        status: true,
        response:updateRes,
        message: "Password updated successfully"
      }
    }  
    next();
  } catch (e) {
    next(e)
  }
})
```

这是代码中有趣的部分。在这里，我们将看到如何从会话令牌中获取所有用户详细信息。请求中没有提到用户详细信息或用户名。但是我们作为请求处理程序注入的会话处理程序必须在请求会话对象中注入用户数据。

```
let uname = req.session.userData.username;
```

我们从请求会话对象中获取用户名，然后执行更新用户密码所需的所有操作。

唷！！！！！

![](img/ad007796f5d739a4dbe5b54b253fe4d6.png)

肯德尔·詹姆斯在 [Unsplash](https://unsplash.com/search/photos/tired?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

现在让我们测试我们的代码。使用 node index.js 命令运行项目。

让我们首先点击我们的旧 API“/hello”来检查我们的新响应处理程序。

![](img/b89c225300ba50f0d819b78bd85f9c14.png)

现在让我们点击登录 api:

![](img/d44a3afdb3cefa3ad98b8646ccdd2318.png)

我们可以在 api 响应中看到会话令牌。我们还可以检查 redis 实例，以确认我们有用户的详细信息。

![](img/25a6fe0d8e4030f747ab25d9224c46f7.png)

现在让我们尝试用一个无效的令牌来攻击更新密码 api:

![](img/059ffa2c9e20c82de1f49b44dfca42f6.png)

现在让我们复制之前在登录 api 响应中获得的会话令牌，并将其传递到更新密码 api 的头中。

![](img/4fb26e364d3c2c07eeea699e85ddba58.png)![](img/b0b10910424d2651eb257c131a4313ff.png)![](img/d619e7e2fefa66209a327c6144de7de1.png)

我希望您喜欢这篇文章，在下一篇文章中，我们将看到如何使用 JWT 实现身份验证。

> 您可以在此处找到代码:
> 
> [https://github.com/pankaj805/medium-06_redis](https://github.com/pankaj805/medium-06_redis)

如果你喜欢这篇文章，你可以👏并与他人分享。你可以在这里找到整个 [***文章系列***](https://medium.com/@pankaj.panigrahi/list-of-node-js-articles-ededa6dd304b) 。

![](img/0bf5df4c347f4b7878add00ed9d2eeef.png)

今天到此为止。