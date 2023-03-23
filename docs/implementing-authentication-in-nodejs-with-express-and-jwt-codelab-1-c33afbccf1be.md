# 用 Express 和 JWT 在 Node.js 中实现身份验证

> 原文：<https://betterprogramming.pub/implementing-authentication-in-nodejs-with-express-and-jwt-codelab-1-c33afbccf1be>

## 代码实验室#1

![](img/17233471778e75ffb7efeccd0ab6121f.png)

Deepak 的 CodeLab

我正在开始一个 CodeLab 系列，在这个系列中，我将创建一些很酷的东西，并与社区分享。

今天，我们将使用 [JWT](https://jwt.io/) 、Express 和 [MongoDB](https://www.mongodb.com/) 在 Node 中实现一个认证 API。

我建议你跟着目录走，不要错过任何步骤。我会在最后提供完整的应用程序代码链接。

# 目录

1.  介绍
2.  先决条件
3.  所需的工具和软件包
4.  启动项目
5.  设置 MongoDB 数据库
6.  配置用户模型
7.  用户注册
8.  用户登录
9.  获取登录用户
10.  结论

# 1.介绍

![](img/b92d15b08af315b4cfb1887e9603182d.png)

# 2.先决条件

您应该事先了解 JavaScript 基础知识和 Node.js。了解 ES6 语法更佳。最后，Node.js 应该安装在您的系统上。

# 3.需要的包

您将需要以下 npm 软件包。

1.  快递。Express 是一个最小且灵活的 Node.js web 应用程序框架，为 web 和移动应用程序提供了一组强大的功能。
2.  快速验证器。为了在 Express framework 中验证服务器上的主体数据，我们将使用这个库。这是一个服务器端数据验证库。因此，即使恶意用户绕过了客户端验证，服务器端数据验证也会捕捉到它并抛出错误。
3.  主体解析器。是用于解析正文数据的 Node.js 中间件。
4.  Bcryptjs。这个库将用于哈希密码，然后存储在数据库中。这样，即使是应用程序管理员也无法访问用户的帐户。
5.  JsonWebToken。JsonWebToken 将用于在注册时加密我们的数据负载并返回一个令牌。我们可以使用该令牌向仪表板等安全页面进行身份验证。还会有一个选项来设置这些令牌的有效性，因此您可以指定该令牌将持续多长时间。
6.  猫鼬。Mongoose 是一个 MongoDB 对象建模工具，设计用于在异步环境中工作。猫鼬支持承诺和回调。

# 4.启动项目

我们将从创建一个节点项目开始。因此，创建一个名为`node-auth`的新文件夹，并遵循下面的步骤。所有的项目文件都应该在`node-auth`文件夹中。

```
npm init
```

`npm init`会问你一些关于项目的基本信息。现在您已经创建了节点项目，是时候安装所需的包了。因此，继续运行下面的命令来安装软件包。

```
npm install express express-validator body-parser bcryptjs jsonwebtoken mongoose --save
```

现在，创建一个文件`index.js`并添加这段代码。

```
// File : index.jsconst express = require("express");
const bodyParser = require("body-parser");const app = express();// PORT
const PORT = process.env.PORT || 4000;app.get("/", (req, res) => {
  res.json({ message: "API Working" });
}); app.listen(PORT, (req, res) => {
  console.log(`Server Started at PORT ${PORT}`);
});
```

如果你在终端输入`node index.js`，服务器将在 4000 端口启动。

您已经成功设置了 Node.js 应用程序。是时候设置数据库以添加更多功能了。

# 5.设置 MongoDB 数据库

我们将使用 MongoDB 数据库来存储我们的用户。您可以使用云 MongoDB 服务器或本地 MongoDB 服务器。

在这个代码实验室中，我们将使用一个名为 [mLab](https://mlab.com/) 的云 MongoDB 服务器。

所以，首先，去注册 mLab。并遵循以下步骤。

1.  注册成功后，点击主页上的*新建*按钮。
2.  现在，选择任何云提供商，例如 AWS。在*计划类型*中选择空闲的沙盒，然后点击右下角的*继续*按钮。
3.  选择片段(任意)并点按“继续”。
4.  输入数据库名称(任意)。我在用`node-auth`。点击*继续*，在下一页提交订单。别担心，这是免费的。
5.  现在，您将被重定向到主页。选择您的数据库，即 node-auth。
6.  复制标准的 MongoDB URI。
7.  现在，您需要向数据库添加一个用户。从下面的五个选项卡中，点击*用户*并通过点击*添加数据库用户*来添加用户。

现在，您已经有了数据库用户。用您的 DB 用户名和密码替换`&&`。

```
mongodb://<dbuser>:<dbpassword>@ds257698.mlab.com:57698/node-auth
```

所以，Mongo 服务器地址(MongoURI)应该是这样的。不要试图在我的 MongoURI 上连接。这只是一个虚拟的用户名和密码。

```
mongodb://test:hello1234@ds257698.mlab.com:57698/node-auth
```

现在有了 mongoURI，就可以将 node-auth 应用程序连接到数据库了。请遵循以下步骤。

# 6.配置用户模型

让我们先创建一个`config`文件夹。该文件夹将保存数据库连接信息。

在`config`中创建一个名为`db.js`的文件。

```
//FILENAME : db.jsconst mongoose = require("mongoose");// Replace this with your MONGOURI.
const MONGOURI = "mongodb://testuser:testpassword@ds257698.mlab.com:57698/node-auth";const InitiateMongoServer = async () => {
  try {
    await mongoose.connect(MONGOURI, {
      useNewUrlParser: true
    });
    console.log("Connected to DB !!");
  } catch (e) {
    console.log(e);
    throw e;
  }
};module.exports = InitiateMongoServer;
```

现在，我们完成了数据库连接。让我们创建用户模型来保存我们的注册用户。

继续创建一个名为`model`的新文件夹。在`model`文件夹中，创建一个新文件`User.js`。

我们将使用猫鼬来创建`UserSchema`。

`User.js`

```
//FILENAME : User.jsconst mongoose = require("mongoose");const UserSchema = mongoose.Schema({
  username: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true
  },
  password: {
    type: String,
    required: true
  },
  createdAt: {
    type: Date,
    default: Date.now()
  }
});// export model user with UserSchema
module.exports = mongoose.model("user", UserSchema);
```

现在，我们完成了数据库连接和用户模式。所以，让我们继续更新我们的`index.js`,将我们的 API 连接到数据库。

`index.js`

```
const express = require("express");
const bodyParser = require("body-parser");
const InitiateMongoServer = require("./config/db");// Initiate Mongo Server
InitiateMongoServer();const app = express();// PORT
const PORT = process.env.PORT || 4000;// Middleware
app.use(bodyParser.json());app.get("/", (req, res) => {
  res.json({ message: "API Working" });
}); app.listen(PORT, (req, res) => {
  console.log(`Server Started at PORT ${PORT}`);
});
```

恭喜您，您已成功将您的应用连接到 MongoDB 服务器。

现在，我们要做的下一件事是创建一个`/user/signup`路由来注册一个新用户。我们将在下一节看到这一点。

# 7.用户注册

用户注册的路径将是`/user/signup`。

创建一个名为`routes`的文件夹。在`routes`文件夹中，创建一个名为`user.js`的文件。

`routes/user.js`

```
// Filename : user.jsconst express = require("express");
const { check, validationResult} = require("express-validator/check");
const bcrypt = require("bcryptjs");
const jwt = require("jsonwebtoken");
const router = express.Router();const User = require("../model/User");/**
 * @method - POST
 * @param - /signup
 * @description - User SignUp
 */router.post(
    "/signup",
    [
        check("username", "Please Enter a Valid Username")
        .not()
        .isEmpty(),
        check("email", "Please enter a valid email").isEmail(),
        check("password", "Please enter a valid password").isLength({
            min: 6
        })
    ],
    async (req, res) => {
        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({
                errors: errors.array()
            });
        } const {
            username,
            email,
            password
        } = req.body;
        try {
            let user = await User.findOne({
                email
            });
            if (user) {
                return res.status(400).json({
                    msg: "User Already Exists"
                });
            } user = new User({
                username,
                email,
                password
            }); const salt = await bcrypt.genSalt(10);
            user.password = await bcrypt.hash(password, salt); await user.save(); const payload = {
                user: {
                    id: user.id
                }
            }; jwt.sign(
                payload,
                "randomString", {
                    expiresIn: 10000
                },
                (err, token) => {
                    if (err) throw err;
                    res.status(200).json({
                        token
                    });
                }
            );
        } catch (err) {
            console.log(err.message);
            res.status(500).send("Error in Saving");
        }
    }
);
```

现在，我们已经在`routes/user.js`创建了用户注册。因此，我们需要将它导入到`index.js`中以使其工作。

因此，更新后的`index`文件代码应该如下所示:

`index.js`

```
const express = require("express");
const bodyParser = require("body-parser");
const user = require("./routes/user"); //new addition
const InitiateMongoServer = require("./config/db");// Initiate Mongo Server
InitiateMongoServer();const app = express();// PORT
const PORT = process.env.PORT || 4000;// Middleware
app.use(bodyParser.json());app.get("/", (req, res) => {
  res.json({ message: "API Working" });
}); /**
 * Router Middleware
 * Router - /user/*
 * Method - *
 */
app.use("/user", user);app.listen(PORT, (req, res) => {
  console.log(`Server Started at PORT ${PORT}`);
});
```

让我们使用邮递员开始用户注册。邮差是 API 测试的工具。

![](img/cefb5ba027885925273c359a6deb0e25.png)

# 8.用户登录

现在，是时候实现将安装在`/user/login`上的登录路由器了。

下面是登录功能的代码片段。将以下代码片段添加到`user.js`。

```
router.post(
  "/login",
  [
    check("email", "Please enter a valid email").isEmail(),
    check("password", "Please enter a valid password").isLength({
      min: 6
    })
  ],
  async (req, res) => {
    const errors = validationResult(req); if (!errors.isEmpty()) {
      return res.status(400).json({
        errors: errors.array()
      });
    } const { email, password } = req.body;
    try {
      let user = await User.findOne({
        email
      });
      if (!user)
        return res.status(400).json({
          message: "User Not Exist"
        }); const isMatch = await bcrypt.compare(password, user.password);
      if (!isMatch)
        return res.status(400).json({
          message: "Incorrect Password !"
        }); const payload = {
        user: {
          id: user.id
        }
      }; jwt.sign(
        payload,
        "secret",
        {
          expiresIn: 3600
        },
        (err, token) => {
          if (err) throw err;
          res.status(200).json({
            token
          });
        }
      );
    } catch (e) {
      console.error(e);
      res.status(500).json({
        message: "Server Error"
      });
    }
  }
);
```

![](img/2a306eb1aba865a7f631cc1150ab9f9d.png)

# 9.获取登录用户

现在，您的用户注册和用户登录工作正常，您将获得一个令牌作为回报。

因此，我们的下一个任务将是使用令牌检索登录的用户。让我们来添加这个功能。

如果您在头中传递令牌，`/user/me`路由将返回您的用户。在文件`route.js`中，添加以下代码片段。

```
/**
 * @method - POST
 * @description - Get LoggedIn User
 * @param - /user/me
 */router.get("/me", auth, async (req, res) => {
  try {
    // request.user is getting fetched from Middleware after token authentication
    const user = await User.findById(req.user.id);
    res.json(user);
  } catch (e) {
    res.send({ message: "Error in Fetching user" });
  }
});
```

现在，我们将添加一个中间件 auth，您可以在`/user/me`路线中看到它。

继续创建一个名为`middleware`的新文件夹。在这个文件夹中，创建一个名为`auth.js`的文件。

这个身份验证中间件将用于验证令牌，并基于令牌有效负载检索用户。

`middleware/auth.js`

```
const jwt = require("jsonwebtoken");module.exports = function(req, res, next) {
  const token = req.header("token");
  if (!token) return res.status(401).json({ message: "Auth Error" }); try {
    const decoded = jwt.verify(token, "randomString");
    req.user = decoded.user;
    next();
  } catch (e) {
    console.error(e);
    res.status(500).send({ message: "Invalid Token" });
  }
};
```

耶！您已经在 Node.js 中成功创建了一个身份验证 API，现在，您可以在登录后继续测试`/user/me`端点。

## 如何测试应用程序？

测试 API 需要邮递员[。如果没有安装 Postman，先安装。](https://www.getpostman.com/automated-testing)

1.  首先，注册用户或登录(如果您已经注册)。
2.  从步骤 1 开始，您将获得一个令牌。复制令牌并放入报头中。
3.  点击*提交。*

这里是测试的预览。

![](img/5e79bd8c2642fb3591dd4b05f8424fca.png)

# 10.结论

在这个代码实验室中，我们使用 Express、JsonWebToken 和 MongoDB 介绍了 Node.js 中的身份验证。我们学习了如何编写中间件。

这里是这个代码实验室的[完整代码](https://github.com/dipakkr/node-auth)的链接。

另外，我很想知道你在下一个 CodeLabs 中还想涉及什么。

订阅我的电子邮件简讯并保持更新！

干杯！