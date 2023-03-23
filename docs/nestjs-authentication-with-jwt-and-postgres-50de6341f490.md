# NestJS:用 JWT 和 Postgres 进行认证

> 原文：<https://betterprogramming.pub/nestjs-authentication-with-jwt-and-postgres-50de6341f490>

## 构建一个使用 Postgres、JWT 和环境变量进行身份验证的 NestJS 应用程序

![](img/4cae97d68af644800ae847bbb38f14b4.png)

有时候，作为初学者，NestJS 可能会变得有点让人不知所措。我知道这一点，因为我也曾经在这一点上。我的意思是，为了编写一个带认证的 API，我们必须处理很多事情:NestJS 本身、TypeORM、数据库、JWT、护照、路径别名、dto、验证、守卫、管道等等。

但是我告诉你，没有看起来那么难！

# 要求

需要对 Node.js 和 PostgreSQL 有一个基本的了解，它们已经安装在您的机器上。我会选择 [Visual Studio 代码](https://code.visualstudio.com/)作为我的代码编辑器。你可以用你喜欢的任何东西。

# 数据库ˌ资料库

首先，我们需要创建 PostgreSQL 数据库。我知道，每个人都有不同的处理方式，有些人使用 GUI，但我们将使用我们的终端。同样，您需要在您的机器上安装 PostgreSQL。如果您安装了 PostgreSQL，以下四个命令将在 Linux、Mac 和 Windows 机器上运行。

```
$ psql postgres
$ CREATE DATABASE nest_auth_api;
$ \l
$ \q
```

## **命令解释:**

*   `psql postgres`使用用户`postgres`打开 psql CLI
*   `CREATE DATABASE nest_auth_api;`创建我们需要的数据库
*   `\l`列出所有数据库
*   `\q`退出 psql CLI

在我们成功地执行了所有四个命令之后，我的终端看起来应该是这样的。如我们所见，数据库`nest_auth_api`被创建。

![](img/d2a5ceedb06e956c568459c8b11f6b4c.png)

# NestJS 应用程序

让我们继续讨论 NestJS。我们将安装 NestJS CLI，因此打开您选择的终端并键入:

```
$ npm i -g @nestjs/cli
```

我们用它的 CLI 初始化一个新的 NestJS 项目。这可能需要一分钟。

```
$ nest new nest-auth-api -p npm
```

完成该命令后，您可以在代码编辑器中打开您的项目。由于我使用 Visual Studio 代码，我将通过键入以下命令打开项目:

```
$ cd nest-auth-api
$ code .
```

我的项目在 VSCode (Visual Studio 代码)中是这样的:

![](img/94227ff09501c6ede421a65d956e7305.png)

下一步是**可选的**，但是通常，我会提交初始初始化，所以我会在终端内部键入:

```
$ git add .
$ git commit -m "chore(): init nest.js"
```

## 安装依赖项

让我们安装一些我们将需要的依赖项。

```
$ npm i @nestjs/config @nestjs/typeorm @nestjs/jwt @nestjs/passport passport-jwt typeorm pg passport class-transformer class-validator class-sanitizer bcryptjs
$ npm i -D @types/passport-jwt @types/node
```

## 创建项目结构

为了有一个干净的**项目结构**，我们将创建一些文件夹和文件。对于诸如环境文件或一般帮助文件之类的杂项文件，我通常会创建一个名为 common 的新文件夹。

```
$ mkdir src/shared && mkdir src/shared/typeorm
$ mkdir src/common && mkdir src/common/envs && mkdir src/common/helper
```

另外，让我们**创建**一些通用文件。

```
$ touch src/shared/typeorm/typeorm.service.ts
$ touch src/common/envs/development.env
$ touch src/common/helper/env.helper.ts
```

现在，我们还需要创建我们的**模块**。为了简单起见，我们只使用**用户**和**认证**模块。

```
*$ nest g mo api
$ nest g mo api/user && nest g co api/user* --no-spec *&& nest g s api/user* --no-spec *$ nest g mo api/user/auth && nest g co api/user/auth* --no-spec *&& nest g s api/user/auth* --no-spec
$ touch src/api/user/user.dto.ts
$ touch src/api/user/user.entity.ts
$ touch src/api/*user/*auth/auth.dto.ts
$ touch src/api/*user/*auth/auth.guard.ts
$ touch src/api/*user/*auth/auth.helper.ts
$ touch src/api/*user/*auth/auth.strategy.ts
```

我们的项目树现在应该是这样的:

![](img/e0e7b7204402e9ac651a08ebe5ce9cae.png)

你可以看到，我们有很多事情要做。

## 环境变量

为了在我们构建应用程序时将环境文件放在我们的`dist/`文件夹中，我们需要向我们的`nest-cli.json`添加一个编译器规则来映射我们的环境文件。

所以请把`nest-cli.json`从

nest-cli.json

到

nest-cli.json

现在让我们修改我们的`src/common/helper/env.helper.ts`文件，以便在稍后导入我们的 ConfigModule 时加载我们的环境文件。

src/common/helper/env . helper . ts

这个函数根据我们的`NODE_ENV`返回环境文件的绝对路径。它基本上有两个回落。首先，如果没有设置`NODE_ENV`，我们尝试获取`development.env`文件的绝对路径。让我们继续给我们的环境文件添加一些生命。

> ***你可以看到每个代码片段下面的文件路径。***

src/common/envs/development . env

## 别名路径

为了获得干净的文件路径，我们将修改根目录中的 tsconfig.json。如果你不知道什么是别名路径，请阅读我关于[在 NestJS](https://javascript.plainenglish.io/a-simple-way-to-use-path-aliases-in-nestjs-ab0db1be1545) 中使用路径别名的简单方法的文章。

**简而言之:**它将文件路径从类似`../../../folder/file.ts`的东西导入到`@/folder/file.ts`中。

现在，让我们将`tsconfig.json`从:

tsconfig.json

到

tsconfig.json

## 配置并连接到数据库(Postgres)

我们需要将我们的 NestJS 应用程序连接到我们之前创建的 Postgres 数据库。记住，我们创建了一个数据库和一个环境文件，我们在其中存储敏感的数据库信息。因此，我们必须分两步将所有这些整合在一起。

1.  我们创建了一个 TypeORM 配置服务。
2.  我们在主模块中加载这个服务和 ConfigModule。

让我们给我们的`src/shared/typeorm/typeorm.service.ts`文件添加一些内容:

src/shared/type ORM/type ORM . service . ts

现在，我们需要将`src/app.module.ts`从:

src/app.module.ts

到

src/app.module.ts

太好了！现在，让我们向 main.ts 文件添加一些额外的配置，让我们将它从:

到

我们在这里做的最重要的事情是第 12 行。我们添加了一个全局验证[管道](https://docs.nestjs.com/pipes)，以便[稍后验证](https://docs.nestjs.com/techniques/validation)请求体。

现在，我们已经做了很多事情。我们创建了环境文件、一个连接到数据库的服务和许多其他配置，为我们的 NestJS 应用程序做好准备。

无论如何，是时候通过运行以下命令来测试我们的代码了:

```
$ npm run start:dev
```

我们终端的输出应该是这样的:

![](img/2fa96e6990446b20e2c5628b0088ea80.png)

如果你遇到错误，请在评论中告诉我。如果一切正常，你可以通过按 CTRL+C 来停止这个应用程序。

## 用户实体

在我们开始验证模块之前，我们需要添加我们的用户实体，因为验证模块依赖于此。

让我们给我们的`src/api/user/user.entity.ts`文件添加一些内容:

src/api/user/user.entity.ts

由于我们在 TypeORM 服务中启用了同步，TypeORM 将自动基于这个实体创建一个表。

**什么是** `@Exclude()` **？**

你可能在第 12 行见过这个装修工。这基本上意味着，我们将从响应数据中删除该属性(密码)。你可以在这里阅读更多关于这个[的内容](https://docs.nestjs.com/techniques/serialization#exclude-properties)。

# 证明

这是我们最重要的模块，这里统计了魔法。

## AuthHelper 类

这个类非常重要，因为我们在这里有一些加密和验证助手。查看方法上方的注释，了解每个方法的作用。

让我们给`src/api/user/auth/auth.helper.ts`添加一些代码

src/API/user/auth/auth . helper . ts

## 防护装置

每当我们需要在敏感端点上进行身份验证时，也会调用这个类。

让我们给`src/api/user/auth/auth.guard.ts`添加一些代码

src/API/user/auth/auth . guard . ts

## JWT 战略

现在我们可以处理最后一个需求了:通过要求请求中存在有效的 JWT 来保护端点。护照在这里也能帮到我们。它为以后保护我们的端点提供了 passport-jwt 策略。

让我们给`src/api/user/auth/auth.strategy.ts`添加一些代码

src/API/用户/验证/验证策略. ts

现在，我们已经完成了 JWT 战略的实施。让我们继续我们的端点实现。

## 授权服务

如果你熟悉 NestJS，你就知道这是怎么回事了。这是我们端点的业务逻辑。这里的方法是从我们的控制器调用的。

> ***注意:*** *如果这个应用程序正在运行，这个修改将会破坏您的构建，因为我们将要注入一个我们还没有导入的库。为了避免混乱，你最好通过 CTRL+C 杀死我们终端中的进程*

让我们把`src/api/user/auth/auth.service.ts`从:

src/API/user/auth/auth . service . ts

到

src/API/user/auth/auth . service . ts

## 确认

请记住，在本教程的前面，我们在 main.ts 文件中添加了一个用于验证的全局管道。这种配置结合数据传输对象将有助于我们验证和转换传入 HTTP 请求的主体。

让我们给`src/api/user/auth/auth.dto.ts`添加一些代码

src/api/user/auth/auth.dto.ts

## 授权控制器

我们几乎完成了认证模块，只剩下两个步骤。

现在，我们需要添加端点来注册、登录和刷新我们的 JWT 令牌。请记住，我们在这里保持简单，我们也可以破坏这些端点，但为了简单起见，我们不这样做，因为我向您介绍了基础知识。

让我们将文件`src/api/user/auth/auth.controller.ts`从:

src/API/用户/验证/验证控制器. ts

到

src/API/用户/验证/验证控制器. ts

## 授权模块

最后但同样重要的是，我们需要导入我们刚刚编码的所有模块、提供者和控制器。

我们需要改变我们的`src/api/user/auth/auth.module.ts`,从:

src/API/用户/验证/验证模块. ts

到

src/API/用户/验证/验证模块. ts

# 用户

现在我们几乎完成了这个项目。我们只需要为演示添加一些糖，考虑这一步是可选的，因为实际上，我们的 NestJS 认证 API 已经工作了，无论如何，让我们很快完成它。我们在这里要做的就是创建一个端点来修改用户名。

请记住，我们已经在本教程前面添加了用户实体。

## 确认

我们需要对端点进行一些简单的验证，所以让我们给`src/api/user/user.dto.ts`添加一些代码

src/api/user/user.dto.ts

## 服务

我们将创建一个小方法，让我们改变用户名。很简单。

> ***注意:*** *如果这个应用程序正在运行，这个修改将会破坏您的构建，因为我们将要注入一个我们还没有导入的存储库。为了避免混乱，你最好通过 CTRL+C 杀死我们终端中的进程*

让我们把文件`src/api/user/user.service.ts`从:

src/api/user/user.service.ts

到

src/api/user/user.service.ts

## 控制器

在这里，我们将创建我们的端点。我们将再次放置 JwtAuthGuard，这意味着只有具有有效 JWT 令牌的请求才能成功。

因此，让我们将文件`src/api/user/user.controller.ts`从:

src/API/user/user . controller . ts

到

src/API/user/user . controller . ts

## 组件

欢迎来到本教程中非常简单的最后一步，我们需要做的就是导入用户实体。

让我们将文件`src/api/user/user.module.ts`从:

src/API/用户/用户.模块. ts

到

src/API/用户/用户.模块. ts

哇，多棒的旅程啊！信不信由你，我们终于完成了我们的项目。现在，让我们执行以下命令来运行我们的应用程序:

```
$ npm run start:dev
```

您的终端应该给出以下响应:

![](img/0176c1eceee0e09cccc67560fce11148.png)

# 测试我们的端点

现在让我们测试我们的端点。你可以使用软件如[邮差](https://www.postman.com/)或简单地通过[卷曲](https://curl.se/)命令在你的终端。

## **注册**

```
$ curl -X POST [http://localhost:3000/auth/register](http://localhost:3000/auth/register) -H "Content-Type: application/json" -d '{"name": "Elon Musk", "email": "elon@gmail.com", "password": "12345678"}'**Server Response:** {"name": "Elon Musk", "email": "[elonn@gmail.com](mailto:elonn@gmail.com)", "lastLoginAt": null, "id": 1}
```

如果您再次运行此命令，您将遇到 409(冲突)错误，因为使用此电子邮件的用户已经存在。

如果您选择少于 8 个字符的密码，您将运行 400(错误请求)错误，因为我们要求密码的最小长度为 8 个字符。

## 注册

```
$ curl -X POST [http://localhost:3000/auth/login](http://localhost:3000/auth/login) -H "Content-Type: application/json" -d '{"email": "elon@gmail.com", "password": "12345678"}'**Server Response:** eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NSwiZW1haWwiOiJlbG9uQGdtYWlsLmNvbSIsImlhdCI6MTY0NjU5NzY2NiwiZXhwIjoxNjc4MTMzNjY2fQ.fBG9QJKOUIvrB1iaDDs-2gJKH_qephHNRtJyiKG-aVk
```

如果您选择了错误的电子邮件或密码，您将遇到 404(未找到)错误，每当电子邮件或密码是错误的，我们将抛出。

## 刷新(仅针对登录用户)

请记住，您可能需要替换不记名令牌: *ezJhbGci0i…*

```
$ curl -X POST http://localhost:3000/auth/refresh -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NSwiZW1haWwiOiJlbG9uQGdtYWlsLmNvbSIsImlhdCI6MTY0NjU5NzY2NiwiZXhwIjoxNjc4MTMzNjY2fQ.fBG9QJKOUIvrB1iaDDs-2gJKH_qephHNRtJyiKG-aVk"**Server Response:** eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NSwiZW1haWwiOiJlbG9uQGdtYWlsLmNvbSIsImlhdCI6MTY0NjU5NzkyOSwiZXhwIjoxNjc4MTMzOTI5fQ.B8hCJmzoiLKSlZLVDIllLjYm5z7N9soDHSdRg_Iax2s
```

如果 JWT 令牌无效或未定义，此端点将抛出 403(禁止)错误。

## 更改用户名

```
$ curl -X PUT [http://localhost:3000/user/name](http://localhost:3000/user/name) -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NSwiZW1haWwiOiJlbG9uQGdtYWlsLmNvbSIsImlhdCI6MTY0NjU5NzkyOSwiZXhwIjoxNjc4MTMzOTI5fQ.B8hCJmzoiLKSlZLVDIllLjYm5z7N9soDHSdRg_Iax2s" -d '{"name": "Super Elon"}'**Server Response:** {"id": 1, "email": "[elon@gmail.com](mailto:elon@gmail.com)", "name": "Super Elon", "lastLoginAt": "2022-03-06T20:18:49.356Z"}
```

如您所见，我们已经更改了用户名。

终于，我们完成了！我已经把项目上传到了 [GitHub](https://github.com/hellokvn/nestjs-auth-api-medium) 上。看看这个。

感谢您阅读我关于如何在 NestJS 中使用 JWT 认证的文章。我希望，你能学到新的东西。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，可以考虑注册[成为](https://medium.com/@hellokevinvogel/membership)中的一员。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？给我买杯咖啡。