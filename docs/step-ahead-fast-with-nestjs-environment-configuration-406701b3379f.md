# 在 NestJS 中实现外部定义的配置设置

> 原文：<https://betterprogramming.pub/step-ahead-fast-with-nestjs-environment-configuration-406701b3379f>

## 使用 NestJS 环境配置快速前进

![](img/47c7ee34abfd7614dfa636f772ad4886.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

您可能已经花了一些时间使用令人惊叹的 NestJS 框架，现在是时候看看如何为您的配置设置获取一些外部定义的值，如一些关键或全局变量，例如:

*   应用程序监听端口号
*   API 全局路由前缀
*   JWT 参数(令牌秘密、令牌到期时间，例如以秒为单位)
*   数据库连接参数(类型、主机、端口、用户名、密码、数据库)
*   等等。,

[NestJS](https://docs.nestjs.com/) 提供必要的[文档](https://docs.nestjs.com/techniques/configuration#cache-environment-variables)用于在不同的环境下工作(开发、生产等)。)使用外部配置文件和环境变量。所以，如果你已经看过了，那么下面提供的案例将会帮助你开始实施你的(相对简单的)项目。

注意:假设您已经为您的项目安装了 [@nestjs/config](https://www.npmjs.com/package/@nestjs/config) 包，如果您也安装了 [@nestjs/jwt](https://www.npmjs.com/package/@nestjs/jwt) 包并开始使用 [JSON Web Tokens](https://jwt.io/) 也很好。

因此，下面您可以找到 3 个最简单、最常见的案例，供您在实施中使用。

# 基础知识

带有已定义属性及其值(如键/值对)的`.env.dev` 环境配置文件

`.env.dev`:

`package.json`文件中的脚本

例如，我们可以在终端中运行 dev 配置:

```
npm run start:dev
```

## **在应用模块**中全局配置配置模块

`.forRoot()` —这个静态方法在`ConfigModule`中加载环境变量，配置`ConfigService`提供者，并使其在模块范围内可用，由于这是`AppModule`，它使其在整个应用程序中都可用。

`isGlobal` — 我们使用`isGlobal`属性并将其设置为 true，这样我们就可以在我们的项目中普遍使用`ConfigModule`，而不必在每个模块中单独导入它。

`envFilePath` —我们使用`envFilePath`属性从文件中加载环境变量——我们传递包含它们的文件的完整路径名。

现在，是时候在我们的项目中使用外部定义的值了(通过`ConfigModule`)。

# 在类/repo/服务中使用 ConfigService

首先，我们必须将它添加到这个类/属性/服务所属的`Module`的 imports 属性数组中。

```
imports: **[**AuthModule, ConfigModule**]**,
```

## **在用户模块中导入配置模块**

注意，在我们的例子中，我们已经在我们的`AppModule`中为全局使用设置了`ConfigModule`(即我们已经将`isGlobal`属性设置为 true)，因此，不需要在 imports 属性数组中导入`ConfigModule`。

然后，我们必须将其注入到该类/repo/服务的构造函数中:

最后，我们可以“正常”使用它:

# 在 main.ts 中使用 ConfigService

由于`main.ts`只包含`bootstrap()`函数，我们可以将`ConfigService`实例/上下文作为一个常量，如下所示:

```
**const** configService = app.get**(**ConfigService**)**;
```

之后，我们可以使用实例的 get 方法，这是正常的方式，例如对于应用程序监听端口:

```
**const** appPort = configService.get**<**number**>(**'APP_PORT', 3000**)**;
**await** app.listen**(**appPort**)**;
```

如您所见(正如官方文档所述)，`get()`方法还采用了一个可选的第二个参数来定义默认值，当键不存在时将返回该值，如上面所示的应用程序监听端口，其默认值为 3000。

我们可以对环境文件中声明的任何其他键进行同样的操作。

一个`main.ts`文件的例子:

## 在动态导入的模块中注入和使用 ConfigService

(它是在模块的导入属性数组中导入的)

例如，当我们在另一个模块中导入`JwtModule`时，例如在`Authorization`模块:`AuthModule`中。下面是我们如何导入`JwtModule`并在`signOptions`中直接注册其参数/属性的值:secret 和`expiresIn`。

这里第一个提示是异步注册`JwtModule`,例如

```
JwtModule.registerAsync**(**. . .**)**
```

这是必要的，因为我们必须读取外部文件，如。env 文件异步。

然后，我们必须在注入属性数组中添加`ConfigModule`。之后，我们可以使用`useFactory()` **动态地(并且异步地)提供/注入`ConfigService`实例。**

一个完整版`AuthModule`的例子:

同样，这里不需要在模块属性数组中声明`ConfigModule`，因为我们已经在`AppModule`中设置了`isGlobal` : true。

就是这样！感谢阅读和快乐编码！