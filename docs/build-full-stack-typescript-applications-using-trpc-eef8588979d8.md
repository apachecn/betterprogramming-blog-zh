# 使用 tRPC 构建全栈类型脚本应用程序

> 原文：<https://betterprogramming.pub/build-full-stack-typescript-applications-using-trpc-eef8588979d8>

## 使用 Next 和 Express 适配器实现前端和后端之间的类型安全集成

![](img/d01264c780fd6b8daf889c03f0d4c674.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Dane Deaner](https://unsplash.com/@danedeaner?utm_source=medium&utm_medium=referral) 拍摄的照片

在构建全栈应用程序时，您可能会使用 REST 或 GraphQL 来为您的前端提供 API 功能。tRPC 试图改变这种情况，或者至少提供一种替代方案，在客户机和服务器之间增加类型共享的好处。

好奇吗？当我看到 tRPC 出现在我的 Twitter feed 中时，我也是，所以我决定仔细看看。在本文中，我们将看看 tRPC 提供了什么，以及它如何简化前端应用程序的 API 开发。

# tRPC 是什么？

简而言之，tRPC 是一个使用 TypeScript 构建类型安全 API 的框架。与 REST 和 GraphQL 等传统 API 相反，tRPC 中没有 API 模式。相反，您在后端 API 中定义的类型可以在客户端访问。

起初这听起来可能有点奇怪，但是如果你考虑到 tRPC 的主要用例是在全栈应用程序中提供 API，这实际上很有意义。

当涉及到服务之间的 HTTP 调用时，为了理解客户机和服务器之间的契约，模式是必不可少的。然而，对于用 tRPC 构建的全栈应用程序的 API，类型提供了契约。

tRPC 本身是非常轻量级的，没有依赖性和框架无关性。大多数主流框架都有 tRPC 的适配器，包括 [Next.js 和 Express](https://trpc.io/docs/example-apps) 。

现在我们了解了 tRPC 是什么，它解决了什么问题？

# tRPC 解决什么问题？

与任何新(ish)技术一样，它的产生很可能是因为有人遇到了以前的技术没有解决的问题。

在这种情况下，问题是为前端 API 创建模式，以及随之而来的所有相关管道。在客户端更是如此，您需要格式化请求并进行 HTTP 调用。tRPC 基本上解决了这个问题，因为它为你处理了这个问题。本质上，它解放了你，工程师，去关注前端显示和后端逻辑，而不必担心两者如何互相交流。

这确实在前端代码和后端代码之间引入了耦合感。然而，因为它是 TypeScript，所以您的前端代码只知道它将接收一个符合特定接口的对象。这意味着我们通过依赖抽象，坚持了[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)。

tRPC 的另一大卖点是速度和安全性。由于我们不必担心模式，因为它们是通过类型隐含的，前端和后端之间的集成是快速的。

至于安全性，您可以确保当您对前端或后端进行更改时，不会破坏正在使用的数据。如果您重命名从您的 API 返回的`User`上的一个属性，任何使用该属性的客户端代码都将被突出显示以进行更新。

# tRPC 的一个简单例子

为了完善这篇文章，我将展示一个简单的例子。为了简单起见，我不打算用 Next 或 Express 创建任何东西。相反，我将展示如何在 tRPC 中创建快速端点，以及如何在客户端使用它。

下面是服务器端，实现一个端点来从列表中检索动物:

```
import { initTRPC } from '@trpc/server';

const t = initTRPC.create();

interface Animals {
    id: number,
    type: string;
}

const listOfAnimals: Animals[] = [
    {
        id: 1,
        type: 'Dog'
    },
    {
        id: 2,
        type: 'Cat'
    },
];

const appRouter = t.router({
    getAnimal: t.procedure
        // The input is unknown at this time.
        // A client could have sent us anything
        // so we won't assume a certain data type.
        .input((req: unknown) => {
            //Here we should do some validation of the input
        })
        .query((req) => {
            const { input } = req;
            const animal = listOfAnimals.find((u) => u.id === input);

            return animal;
        }),
});

export type AppRouter = typeof appRouter;
```

很简单，对吧？我知道它使用的是虚拟数据，但我真的很喜欢用 tRPC 创建端点是多么容易。这是一个简单的查询，本质上映射到一个 GET 请求。它还支持突变，用于创建或修改数据。

如果我们现在想在客户端调用这个端点，代码甚至比服务器还要简单:

```
import { createTRPCProxyClient, httpBatchLink } from '@trpc/client';
// This is our server definition from above, using import type
// is important, as it only imports type, not code, to avoid
// accidentally using server-side code
import type { AppRouter } from './server';

//This configures the client to talk to our server
const trpc = createTRPCProxyClient<AppRouter>({
  links: [
    httpLink({
      url: 'http://localhost:3000/trpc',
    }),
  ],
});

// Call the API
const animal = await trpc.getAnimal.query(1);
```

代码被很好地注释了，所以它应该解释发生了什么。这些链接是 tRPC 的一个有趣的特性，你可以在这里阅读关于[的内容。值得注意的是，在指定的端口上没有运行任何东西，但是如果我们将 tRPC 与 Next.js 一起使用，默认情况下，它将在该端口上本地公开。](https://trpc.io/docs/links)

# 摘要

在这一点上，你可能认为 tRPC 是一个新的框架。不过也有几年历史了，最近发布了 10 版，所以保养的很好。然而，随着 GitHub 上的明星越来越多，它的势头越来越大。

如果我要构建一个全新的全栈应用程序，我当然愿意尝试 tRPC。这看起来是一个非常简单而可靠的产品，与大多数主流框架都有很好的集成。它的[文档](https://trpc.io/docs)也很不错。

你试过 tRPC 吗，还是准备试一试？给我留言，让我知道！

> 我的书《用现代图表技术创建软件》已经出版了！
> 
> [点击此处](https://www.amazon.com/dp/1680509837?maas=maas_adg_265A9C302E256D26C2E10C30DA1AA728_afap_abs&ref_=aa_maas&tag=maas)学习如何创建图表，比文字更直接、更清晰地传达信息。仅使用基于文本的标记，由 Mermaid 提供支持，创建有意义和有吸引力的图表来记录您的领域，可视化用户流，揭示任何所需级别的系统架构，等等！