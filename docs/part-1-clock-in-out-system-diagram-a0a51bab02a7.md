# 上下班打卡系统第 1 部分:图表

> 原文：<https://betterprogramming.pub/part-1-clock-in-out-system-diagram-a0a51bab02a7>

## NestJS + Angular 教程

![](img/27028d592125269574905771e8218205.png)

图一。上下班打卡系统:图表

这篇文章是我描述上下班打卡系统的系列文章的一部分。在这个系列的第一部分，我将描述系统的架构。

# 系统图

描述我们的系统将解决的问题的最好方法是使用图表。

图一。上图显示了用于构建上下班打卡系统的图表。在此图中，您可以看到不同的组件，它们可以在不同的服务器上，也可以在同一台服务器上。

# 系统组件

我们系统的组件如下:

## 1.身份证

所有用户都有一张卡，用一个 UID **来标识。**这种方式的工作原理是，用户在一个有 NFC 读卡器的 [Arduino](https://www.arduino.cc/) 系统附近传递他们的卡**。**

## **2。Arduino**

系统里有两个 Arduino。

第一个用于上班打卡，第二个用于下班打卡。因此，每个 Arduino 通过 HTTPS 使用`POST`动词将 UID 卡发送到后端。

Arduino 系统有一个 NFC 和一个 Wi-Fi 芯片。NFC 用于读取卡，Wi-Fi 芯片用于连接到 LAN 以将 UID 发送到服务器。

## **3。后端**

后端使用 [Nest](https://www.nestjs.com/) 开发，Nest 是一个基于 [Express](https://expressjs.com/) 的框架(虽然你也可以使用其他库，比如 [fastify](https://www.fastify.io/) )。

它可以使用[实体](https://en.wikipedia.org/wiki/SOLID)原理和语法[角度](https://angular.io/)来开发软件。这个后端将连接到一个关系数据库 [PostgreSQL](https://www.postgresql.org/) ，使用[类型的 ORM](http://typeorm.io/) 作为 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 。

## **4。前端**

前端使用 [Angular](https://angular.io/) 开发，是一个基于 JavaScript 的框架。当您想要扩展大型 web 应用程序(客户端)时，这是一个很好的选择。

在本例中，它用来举例说明如何使用的最新有角版本和良好做法。

前端使用 [RxJS](https://github.com/ReactiveX/rxjs) 开发。RxJS 是一个反应式编程的库，使用 Observables 使编写异步或基于回调的代码变得更加容易。

前端和后端之间的连接可以通过使用**和**套接字来获得实时性。然而，使用轮询，您获得了一个套接字结果(接近实时)，但是更容易。

有几个组件可以在未来开发，例如:

*   一个管理面板来管理关于用户和卡的信息
*   我在系统中登记/注销的个人信息
*   用户可以搜索以了解用户是否在建筑物中的前端

将来，这些功能可能会被开发出来，但是今天，我将展示一个基本的上下班打卡系统。

GitHub 项目可以在 https://github.com/Caballerog/clock-in-out[找到](https://github.com/Caballerog/clock-in-out)

# 资源

如果你想了解更多，你可以阅读以下内容:

*   [上下班打卡系统第 1 部分:图表](https://medium.com/@ccaballero/part-1-clock-in-out-system-diagram-a0a51bab02a7?source=post_page---------------------------)
*   [上下班打卡系统第 2 部分:基础后端— AuthModule](https://medium.com/better-programming/part-2-clock-in-out-system-basic-backend-i-authmodule-66d4a5c56122?source=post_page---------------------------)
*   [上下班打卡系统第 3 部分:基本后端—用户模块](https://medium.com/better-programming/part-3-clock-in-out-system-basic-backend-ii-usersmodule-a56f42b20f62?source=post_page---------------------------)
*   [上下班打卡系统第 4 部分:基础后端— AppModule](https://medium.com/better-programming/part-4-clock-in-out-system-basic-backend-iii-appmodule-850dd17883e?source=post_page---------------------------)
*   [上下班打卡系统第 5 部分:种子数据库和迁移数据](https://medium.com/better-programming/part-5-clock-in-out-system-seed-database-and-migration-data-cf037be21aac?source=post_page---------------------------)
*   [上下班打卡系统第 6 部分:基本前端](https://medium.com/@ccaballero/part-6-clock-in-out-system-basic-frontend-an-7e5f9ed08c3f?source=post_page---------------------------)
*   [上下班打卡系统第 7 部分:使用 Docker/Docker-Compose 部署后端(NestJS)](https://medium.com/@ccaballero/part-7-deploy-backend-nestjs-docker-docker-compose-2429c0b6aa9c?source=post_page---------------------------)
*   [上下班打卡系统第 8 部分:使用环境部署前端(角度 6+)](https://medium.com/@ccaballero/part-8-clock-in-out-system-deploy-frontend-angular-6-using-environments-ad267325d3b6?source=post_page---------------------------)
*   [上下班打卡系统第 9 部分:后端测试——服务的单元测试](https://medium.com/@ccaballero/part-9-testing-backend-testing-2d021f48403b?source=post_page---------------------------)
*   [上下班打卡系统第 10 部分:后端测试——控制器单元测试](https://medium.com/@ccaballero/part-10-testing-backend-testing-unit-testing-controllers-4177370ef581?source=post_page---------------------------)
*   上下班打卡系统第 11 部分:后端测试-e2e 测试
*   上下班打卡系统第 12 部分:前端测试单元测试
*   上下班打卡系统第 13 部分:前端测试集成测试