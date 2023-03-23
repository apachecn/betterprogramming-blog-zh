# 如何使用 bcrypt 进行哈希运算

> 原文：<https://betterprogramming.pub/how-to-use-bcrypt-for-hashing-8c5b6212a511>

## 创建一个应用程序来显示`bcrypt`的密码哈希用法

![](img/6303f9be67880448bd0b20bd68ceebb1.png)

照片由 [CoinView App](https://unsplash.com/@coinviewapp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/app?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

`[bcrypt](https://www.npmjs.com/package/bcrypt)`是一个流行的文本哈希库。一个有用的应用是散列密码。我们只需传入我们的文本，然后我们得到一个散列，你想加盐多少次都可以。

一旦你用`bcrypt`杂凑了一些东西，你就无法恢复现有的文本。它有一个`compare`，所以你可以比较你的原始文本和散列版本，看看它们是否相同。这对于在应用程序中安全地存储密码非常方便。

为了展示`bcrypt`的密码哈希用法，我们将创建一个允许用户添加、保存和更新密码的应用程序。有了这个计划，我们可以开始了。

首先，我们创建前端和后端应用程序文件夹。每人做一个。然后，我们开始写后端的 app。

首先，我们安装一些包并生成我们的 [Express](https://expressjs.com/) 框架代码。我们运行`npx express-generator`来生成代码。然后，我们必须安装一些软件包。

我们通过运行`npm i @babel/register express-jwt sequelize bcrypt sequelize-cli dotenv jsonwebtoken body-parser cors`来做到这一点。`@babel/register`允许我们使用最新的 JavaScript 特性。

`express-jwt`生成 JWT，并对照秘密进行验证。对我们的密码进行哈希和加盐处理。是我们做 CRUD 的 ORM。

`cors`通过允许跨域通信，允许我们的 Angular app 与我们的后端进行通信。`dotenv`允许我们在`.env`文件中存储环境变量。`body-parser`是 Express 解析 JSON 请求所需要的。

然后，我们进行数据库迁移。首先，我们运行`npx sequelize-cli init`来为我们的数据库生成用于对象映射的框架代码。然后，我们运行:

```
npx sequelize-cli model:generate --name User --attributes username:string, password:string, email:string
```

我们进行另一次迁移，并输入:

```
'use strict';module.exports = {
  up: (queryInterface, Sequelize) => {
    return Promise.all([
      queryInterface.addConstraint(
        "Users",
        ["email"],
        {
          type: "unique",
          name: 'emailUnique'
        }),queryInterface.addConstraint(
        "Users",
        ["userName"],
        {
          type: "unique",
          name: 'userNameUnique'
        }),
  },down: (queryInterface, Sequelize) => {
    return Promise.all([
      queryInterface.removeConstraint(
        "Users",
        'emailUnique'
      ),queryInterface.removeConstraint(
        "Users",
        'userNameUnique'
      ),
    ])
  }
};
```

这确保我们不会有两个相同用户名或电子邮件的条目。一旦我们运行`npx sequelize-cli db:migrate`，它将创建`User`模型，并将创建用户表。

然后，我们写一些代码。首先，我们将以下内容放入`app.js`:

```
require("[@babel/register](http://twitter.com/babel/register)");
require("babel-polyfill");
require('dotenv').config();
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const user = require('./controllers/userController');
const app = express();app.use(cors())
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());app.use((req, res, next) => {
  res.locals.session = req.session;
  next();
});app.use('/user', user);app.get('*', (req, res) => {
  res.redirect('/home');
});app.listen((process.env.PORT || 8080), () => {
  console.log('App running on port 8080!');
});
```

我们需要下面的代码来使用 JavaScript 的最新特性:

```
require("[@babel/register](http://twitter.com/babel/register)");
require("babel-polyfill");
```

我们需要这段代码来读取我们在`.env`文件中的配置:

```
require('dotenv').config();
```

这是切入点。我们将很快在`controllers`文件夹中创建`userController`。

`app.use(‘/user’, user);`将任何以`user`开头的 URL 路由到`userController`文件。

接下来，我们添加`userController.js`文件:

```
const express = require('express');
const bcrypt = require('bcrypt');
const router = express.Router();
const models = require('../models');
const jwt = require('jsonwebtoken');
import { saltRounds } from '../exports';
import { authCheck } from '../middlewares/authCheck';router.post('/login', async (req, res) => {
    const secret = process.env.JWT_SECRET;
    const userName = req.body.userName;
    const password = req.body.password;
    if (!userName || !password) {
        return res.send({
            error: 'User name and password required'
        })
    }
    const users = await models.User.findAll({
        where: {
            userName
        }
    })const user = users[0];
    if (!user) {
        res.status(401);
        return res.send({
            error: 'Invalid username or password'
        });
    }try {
        const compareRes = await bcrypt.compare(password, user.hashedPassword);
        if (compareRes) {
            const token = jwt.sign(
                {
                    data: {
                        userName,
                        userId: user.id
                    }
                },
                secret,
                { expiresIn: 60 * 60 }
            );
            return res.send({ token });
        }
        else {
            res.status(401);
            return res.send({
                error: 'Invalid username or password'
            });
        }
    }
    catch (ex) {
        logger.error(ex);
        res.status(401);
        return res.send({
            error: 'Invalid username or password'
        });
    }});router.post('/signup', async (req, res) => {
    const userName = req.body.userName;
    const email = req.body.email;
    const password = req.body.password;
    try {
        const hashedPassword = await bcrypt.hash(password, saltRounds)
        await models.User.create({
            userName,
            email,
            hashedPassword
        })
        return res.send({ message: 'User created' });
    }
    catch (ex) {
        logger.error(ex);
        res.status(400);
        return res.send({ error: ex });
    }
});router.put('/updateUser', authCheck, async (req, res) => {
    const userName = req.body.userName;
    const email = req.body.email;
    const token = req.headers.authorization;
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const userId = decoded.data.userId;
    try {
        await models.User.update({
            userName,
            email
        }, {
                where: {
                    id: userId
                }
            })
        return res.send({ message: 'User created' });
    }
    catch (ex) {
        logger.error(ex);
        res.status(400);
        return res.send({ error: ex });
    }});router.put('/updatePassword', authCheck, async (req, res) => {
    const token = req.headers.authorization;
    const password = req.body.password;
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const userId = decoded.data.userId;
    try {
        const hashedPassword = await bcrypt.hash(password, saltRounds)
        await models.User.update({
            hashedPassword
        }, {
                where: {
                    id: userId
                }
            })
        return res.send({ message: 'User created' });
    }
    catch (ex) {
        logger.error(ex);
        res.status(400);
        return res.send({ error: ex });
    }});module.exports = router;
```

`login`路由搜索`User`条目，如果找到，它将使用`bcrypt`的`compare` 函数检查散列密码。如果两者都成功，就会生成一个 JWT。

`signup` route 获取用户名和密码的 JSON 负载并保存它。**请注意**在保存之前，会对密码进行哈希和加盐处理。密码不应以纯文本形式存储。

第一个是纯文本密码，第二个是 salt 轮数。`bcrypt`完全支持承诺，这使得代码比使用回调干净得多。

`hashedPassword`无法恢复，只能使用比较，使用`bcrypt.compare`功能。

`updatePassword`路由是经过认证的路由。它检查令牌，如果有效，它将通过从解码的令牌中搜索带有用户 id 的`User`来继续保存用户的密码。

接下来我们将添加`authCheck`中间件。

创建一个`middlewares`文件夹，并在里面创建`authCheck.js`。

```
const jwt = require('jsonwebtoken');
const secret = process.env.JWT_SECRET;export const authCheck = (req, res, next) => {
    if (req.headers.authorization) {
        const token = req.headers.authorization;
        jwt.verify(token, secret, (err, decoded) => {
            if (err) {
                res.send(401);
            }
            else {
                next();
            }
        });
    }
    else {
        res.send(401);
    }
}
```

您应该使用相同的`process.env.JWT_SECRET`来生成和验证令牌。否则，验证将失败。这个秘密不应该在任何地方共享，也不应该签入版本控制。

这允许我们在经过身份验证的路由中检查身份验证，而无需重复代码。通过导入并引用它，我们将`if`放在每个认证路由的 URL 和主路由代码之间。

我们用这个内容做一个后端 app 文件夹根目录的`.env`文件。这不应该签入版本控制:

```
DB_HOST='localhost'
DB_NAME='login-app'
DB_USERNAME='db-username'
DB_PASSWORD='db-password'
JWT_SECRET='secret'
```

后端应用程序现在已经完成。现在我们可以使用前端应用程序、移动应用程序或任何 HTTP 客户端进行登录。