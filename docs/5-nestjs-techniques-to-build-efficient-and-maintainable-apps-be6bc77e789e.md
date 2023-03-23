# 构建高效且可维护的应用程序的 5 种 NestJS 技术

> 原文：<https://betterprogramming.pub/5-nestjs-techniques-to-build-efficient-and-maintainable-apps-be6bc77e789e>

## 在 5 分钟内学会 5 个隐藏的宝石

![](img/0406c9a5112c27577188d218bd731222.png)

由[在](https://unsplash.com/@whatisdelirium?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/cat-gem?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的

NestJS 是一个复杂的框架，并且在不断发展。这是一个很棒的框架，有许多现成的特性。有些功能并不广为人知，也没有得到充分利用，但如果用在正确的地方，它们会非常强大。

在这篇文章中，我将走过 5 个隐藏的宝石。

*向前跳转:*

*   [版本控制](#f150)
*   [使用带有自定义装饰器的内存缓存](#7497)
*   [NestJS Cron Job](#017e)
*   [用管道验证输入](#5dfc)
*   [通过切换底层平台提高性能](#d54a)

# 版本控制

版本控制对于 web API 开发至关重要。[我们经常需要为重大变更创建新的 API 版本](https://medium.com/p/5e826ef832b6)，同时保持旧版本的工作。

在 NestJS 8 之前，没有现成的 API 版本支持。因此，您需要手动实现版本控制。

在 NestJS 8 中引入了 3 种不同类型的版本控制。

*   URI 版本控制——版本将在请求的 URI 内传递(默认)
*   标题版本——自定义请求标题将指定版本
*   媒体类型版本控制-请求的接受报头将指定版本

让我们以默认的 URI 版本为例。

要启用 URI 版本控制，请执行以下操作:

```
app.enableVersioning({
  type: VersioningType.URI,
});
```

要对控制器应用 URL 版本控制:

```
@Controller({
  version: '1',
})
export class CatsControllerV1 { ....
```

或者您可以将它应用到单独的路线

```
@Version('2')
  @Get('cats')
  findAllV2(): string {
    return 'This action returns all cats for version 2';
  }
```

另一个有用的功能是版本“[中性](https://docs.nestjs.com/techniques/versioning)”。您可以将控制器设置为版本中立的:

```
@Controller({
  version: VERSION_NEUTRAL,
})
export class CatsController {
```

如果传入的请求根本不包含版本，它将被映射到`VERSION_NEUTRAL` 控制器。

NestJS 中新的版本控制特性简单而灵活。这为使用 NestJS 提供了另一个很好的理由。

# 使用带有自定义装饰器的内存缓存

缓存是计算机科学中的难题之一。NestJs 提供了一个现成的 c [ache manager](https://docs.nestjs.com/techniques/caching) ，默认情况下有一个内存中的数据存储。

使用起来很简单。首先导入`CacheModule.`

```
imports: [CacheModule.register()]
```

然后我们可以使用`CACHE_MANAGER`令牌将其注入到一个类中，并开始使用它。

```
constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}// To retrieve items from the cache
await this.cacheManager.get('key');// To add an item to the cache
await this.cacheManager.set('key', 'value');
```

要在 NestJS API 中实现缓存，代码通常如下所示。

当应用程序增长时，具有上述模式的代码将到处重复。

如何减少重复？答案是一个定制装饰器。

装饰器是一个可以扩展另一个函数的行为而不用修改它的函数。为了创建一个可以与缓存管理器交互的装饰器，我们需要将缓存管理器注入到装饰器中。强大的 NestJS DI 使它变得相对容易。

下面是使用内存缓存管理器的装饰器的实现。

解决方案的要点是:

*   `target`是包含装饰器的类
*   `descriptor` 是对被修饰的类方法的引用
*   在第 12 行，我们提取目标构造函数名和方法名以形成一个`cacheKey`，所以它是唯一的
*   `ttl`是使缓存失效的持续时间，我们将默认值设置为 10 秒
*   第 4 行和第 10 行相当于在构造函数中调用@Inject decorator，如下所示:

```
constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}
```

装饰器的使用非常简单:

它现在干净多了，我们可以将它应用于任何需要缓存的服务。

# NestJS Cron Job

尽管 NestJS 主要是为 REST API 设计的，但它也附带了一个调度包。这个包公开了一个 API，允许我们创建 cron 作业。

要开始使用 NestJS cron 作业，我们需要首先安装这个包

```
$ npm install --save @nestjs/schedule 
$ npm install --save-dev @types/cron
```

您可以通过两种不同的方式创建 cron 作业:

*   以声明的方式创建 cron 作业。

`@Cron`装饰器支持 [cron 模式](http://crontab.org/)。

```
@Cron('45 * * * * *')
  handleCron() {
    this.logger.debug('Called when the current second is 45');
  }
```

在第 45 秒时，将每分钟调用一次`handleCron` 方法。您还可以使用预定义的 cron 表达式来设置作业。

```
@Cron(CronExpression.EVERY_MINUTE)
```

*   动态创建一个新的 cron 作业。

使用来自`cron`包的`CronJob`对象，您可以创建 cron 作业。然后，您可以使用`SchedulerRegistry.addCronJob()`方法将作业添加到调度注册表中。

```
addCronJob(name: string, seconds: string) {
  const job = new CronJob(`${seconds} * * * * *`, () => {
    this.logger.warn(`time (${seconds}) for job ${name} to run!`);
  });

this.schedulerRegistry.addCronJob(name, job);
job.start();
```

要了解 NestJS 任务调度包中可用的其他选项和特性，请查看官方文档[。](https://docs.nestjs.com/techniques/task-scheduling)

# 用管道验证输入

数据验证对任何 Web API 都很重要。在 NestJS 中，我们可以使用[管道](https://docs.nestjs.com/pipes)在运行时执行验证。当管道用于验证时，如果验证成功，它将返回未更改的数据，如果数据不正确，将引发错误。

NestJS 自带开箱管道，其中一个就是`ValidationPipe`。内置的验证管道基于流行的[类验证器](https://github.com/typestack/class-validator)包。下面显示了一个用法示例:

*   `@IsString()`:传入值是否为字符串，
*   `@Length(10)`:验证值的最小长度是否为 10 个字符。如果值不是字符串，它也会返回一个错误。

当收到的请求体`CreateCatDto`中的属性无效时，应用程序将自动响应一个`400 Bad Request`。

在上面的例子中，验证是用 decorators 进行的。同一个装饰器可以在整个 NestJS 应用程序的不同 DTO 类中使用。这使得验证逻辑更容易维护。

内置的验证管道提供了更多的装饰器。您还可以在 [class-transformer](https://github.com/typestack/class-transformer) 和 class-validator 包之上创建自己的定制验证管道。

NestJS 中的管道灵活而强大。它可以是同步的，也可以是异步的。它还可以应用于不同级别的范围:参数、方法、控制器或全局。

关于如何使用 NestJS 管道进行验证的更多细节，您可以查看官方文档[。](https://docs.nestjs.com/pipes)

# 通过切换底层平台来提高性能

默认情况下，NestJS 运行在 Express 之上。与其他 API 框架相比，它的性能并不是最好的。对于一个正常的 app 来说，性能上的差异是不会被注意到的。

但是如果你正在开发一个性能非常高的应用程序，你可以迁移你的 NestJS 应用程序来使用 F [astify](https://docs.nestjs.com/techniques/performance) 。Fastify 比 Express 快得多，大约快两倍。

从 Express 到 Fastify 的迁移相对容易，因为 NestJS 提供了基于适配器模式的框架独立性。换句话说，NestJS 的设计使得 Express 和 Fastify 可以互换。

要使用 Fastify，我们需要安装软件包

```
npm i --save @nestjs/platform-fastify
```

然后我们可以在`main.ts.`中配置 Fastify 平台

```
async function bootstrap() {
  const app = await NestFactory.create<NestFastifyApplication>(
    AppModule,
    new FastifyAdapter()
  );
  await app.listen(3000);
}
```

现在 Fastify 已经准备好了！

请注意，在 NestJS 中，Fastify 被用作 HTTP 提供者。因此，那些依赖于 Express 的包将需要由 Fastify 等价包来替换。

想了解更多关于如何构建高性能 NestJS 应用程序的信息吗？

[](https://levelup.gitconnected.com/build-a-high-performance-nestjs-app-5b807ff452e4) [## 构建高性能 NestJS 应用程序

### 优化 NestJS 应用性能的 4 个最佳实践

levelup.gitconnected.com](https://levelup.gitconnected.com/build-a-high-performance-nestjs-app-5b807ff452e4) 

在本文中，我们介绍了 5 个鲜为人知的特性及其用例。我希望你学到了一些东西。