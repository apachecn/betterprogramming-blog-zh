# 通过减少 API 调用的数量来提高 React 应用程序的性能

> 原文：<https://betterprogramming.pub/request-batching-in-react-b8fd0656b28b>

## 在 React 中请求批处理

![](img/1a25c3942fe7cd2af6298f0e98986990.png)

照片由[乔纳森·法伯](https://unsplash.com/@farber?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我最近在开发一个 React 应用程序，它需要调用一个 API 来检查用户是否有权限在应用程序中执行不同的操作。组件将通过调用这个 API 来检查不同的权限，如果用户缺少所需的权限，则由它们来呈现错误或替代状态。

我注意到这种方法会导致快速连续地进行许多调用(尽管针对不同的权限)，我想通过减少调用这个 API 的次数来改善这种情况。然后，我开始考虑如何实现一种机制来消除查询的抖动，在等待期结束时(比如说 10ms ),它会折叠所有的查询，进行一次批处理调用，并在组件不知道的情况下返回响应。

API 已经支持批处理版本(获取许可列表),所以这只是前端工作。

几年前，我开发了一个使用 [Hystrix](https://github.com/Netflix/Hystrix) 的 API，它是一个 Java 库，提供了很多东西，包括[请求折叠](https://github.com/Netflix/Hystrix/wiki/How-it-Works#request-collapsing)，所以我继续寻找一个 Javascript 的等价物，如果可能的话，我真的不想重新发明轮子。

我找到了！是[数据加载器](https://github.com/graphql/dataloader):

> 一个通用实用程序，作为应用程序数据提取层的一部分，为各种后端提供一致的 API，并通过批处理和缓存减少对这些后端的请求。

# 目录

```
· [Dataloader usage](#6c50)
· [React-query integration](#eca2)
```

# 数据加载器用法

基本上，你定义你的`Dataloader`:

```
const fetcher = async (requests) => {
 // this function takes an array of collapsed requests makes a batch call and return an array of responses
};const loader = new Dataloader(fetcher);
```

然后使用其基于 promise 的 API 加载一个(或多个)项目:

```
const data = await loader.load(request);
```

默认情况下，单个执行帧中出现的单个请求将被折叠。但是，这是可配置的，可以很容易地更改为折叠某个时间窗口内发生的所有请求。

```
const loader = new DataLoader(fetcher, {
  batchScheduleFn: callback => setTimeout(callback, 100)
})
```

此外，可以通过使用`maxBatchSize`选项来限制折叠在一起的请求的数量。

# 反应-查询集成

该应用程序还使用了 [react-query](https://react-query.tanstack.com/) 来管理 API 调用，因此挑战在于集成这两者。幸运的是他们在一起玩得很好，正如你在这个[演示](https://codesandbox.io/s/react-query-dataloader-lgosp)中看到的。

所有需要做的就是使用加载器作为 react-query 获取函数:

```
const { data } = useQuery(
    getKey(request),
    () => loader.load(request)
  );
```

就是这样！加载器将完成所有压缩调用的繁重工作，使其对消费者完全透明。

感谢阅读。我希望你今天学到了新东西。保重，下次再见。