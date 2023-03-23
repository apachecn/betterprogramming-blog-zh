# 将 Google 地图服务的 Node.js 客户端与 NestJS 应用程序集成

> 原文：<https://betterprogramming.pub/integrate-nodejs-client-for-google-maps-services-with-a-nestjs-application-f1969d47a639>

## 将 Google 地图服务的 NodeJS 客户端包装在 NestJS 应用程序中

![](img/1e8bcf7b195880e37f32b62ec5596783.png)

由[伊利亚·巴甫洛夫](https://unsplash.com/@ilyapavlov?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在构建一个 NestJS 应用程序时，一个常见的疑问是，实现某些功能的适当方式是否尊重框架架构，以充分利用它提供的可伸缩性。

本文的主要目标是提出一种方法，将 Google Maps 服务的 NodeJS 客户端封装在一个 NestJS 模块中，并使用依赖注入使其易于跨应用程序重用。尽管本文特别使用了`[@googlemaps/google-maps-service-js](https://www.npmjs.com/package/@googlemaps/google-maps-services-js)` npm 包，但它也可以作为其他基于客户端的外部服务的灵感。

# NestJS 框架

NestJS 是一个用于创建可伸缩和松散耦合的服务器端 NodeJS 应用程序的框架。它的主要特征之一是实现的架构和目录结构，这加强了应用程序的模块化性质。最重要的是，NestJS 完全支持 TypeScript，并且还可以在幕后与 [Express](https://expressjs.com/) 和 [Fastify](https://github.com/fastify/fastify) 一起工作，这使得它成为一个最新的、可行的替代框架，用于使用众所周知的工具构建可靠的 web 服务器。

# 框架体系结构

正如 [NestJS 文档](https://docs.nestjs.com/#philosophy)中所述，其架构深受 Angular 架构的启发。文件夹结构基本上组织在模块中，这些模块表示绑定应用程序特定上下文的某个实体。每个模块通常由文件`app.module.ts`、`app.controller.ts`和`app.service.ts`组成。模块的常见文件夹结构如下所示:

```
google-maps
| — google-maps.controller.ts
| — google-maps.module.ts
| — google-maps.service.ts
```

# Google 地图服务的 Node.js 客户端

google maps 服务的[客户端是一个库，创建它是为了与 Google](https://github.com/googlemaps/google-maps-services-js) 提供的 [Web API 直接从给定项目的编程语言进行交互，在本例中，是带有 NodeJS 的 TypeScript。](https://developers.google.com/maps/apis-by-platform#web_service_apis)

鉴于`[@googlemaps/google-maps-service-js](https://www.npmjs.com/package/@googlemaps/google-maps-services-js)` npm 包提供了许多重复使用的方法和类，在该服务中使用依赖注入的一种方式是将 google maps 服务客户端封装在模块类的 getter 方法中，以及最常用的方法中，以便更好地服务于应用程序。

# 将库包装在模块中

`google-maps`模块文件夹由一个`module.ts`文件和一个 service.ts 文件组成，其中包含一个客户端 getter 和其他最常用的方法。该服务将如下所示:

上述代码由以下功能组成，使用了 [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming) 方法:

*   `GoogleMapsService`类扩展了默认的`Client`类，并调用 super()方法来实例化它，以便使用它的所有方法和属性；
*   声明一个私有属性来存储访问 google api 服务所需的访问密钥；
*   出于安全原因，使用内置的 NestJS 服务`ConfigService`从环境变量中检索 accessKey
*   在这个例子中，定义了一个名为`getCoordinates`的异步方法，使用。与其相关的 [web 服务 API](https://developers.google.com/maps/documentation/geocoding/overview)通信的地理编码方法；
*   假设应用程序使用的是 TypeScript，包还为库提供了自定义类型，因此可以为创建为 LatLngLiteral 的方法定义返回值的类型。

上述服务的整个结构在 NestJS 文档中的[专用部分有很好的描述。](https://docs.nestjs.com/providers#services)

由于本文中描述的应用程序没有提供任何外部路由来与上面提供的服务进行交互，因此没有 controller.ts 文件来处理外部请求。

至于模块文件，它负责绑定与给定上下文相关的所有文件，并被注入到其他模块中。在 [NestJS 文档](https://docs.nestjs.com/modules)中可以找到更多关于这部分的内容。`google-maps.module.ts`看起来如下所示:

上面的代码将创建的服务定义为提供者，并将其导出，以便可以在其他模块中重用。

# 依赖注入

最后，需要在使用 google-maps 服务的模块内部声明创建的 google-maps 模块，然后在使用 google-maps 服务方法的类上使用依赖注入。其他模块如下所示:

然后，对于服务级别的 DI:

上面的代码使用 NestJS 依赖注入格式来使用创建的 google-maps 服务，还使用 google-maps 类型来定义`getAddressCoords`方法的返回值。

# 结论

如前所述，在具有多个实体范围的可伸缩应用程序中以模块化的方式编写代码是很有趣的。

考虑到所描述的场景，本文中的应用程序展示了一种模块化外部服务的方法，使代码更具可重用性，并将上下文相关的方法和值集中在同一个模块中。