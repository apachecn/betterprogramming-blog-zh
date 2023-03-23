# 在并发模式下用暂停进行数据提取的反应实验

> 原文：<https://betterprogramming.pub/react-experiments-with-suspense-for-data-fetching-in-concurrent-mode-da1bc6f62ea5>

## 了解如何探索这一期待已久的功能

![](img/c4c2e7ff4cf111081401ee14fa55122f.png)

# 介绍

我们已经等待 [React](https://reactjs.org/) 中的数据获取悬念很久了。它现在作为一个实验性的特性在[实验频道](https://reactjs.org/blog/2019/10/22/react-release-channels.html)中提供。

详情请查阅官方文件。

1.  [引入并发模式](https://reactjs.org/docs/concurrent-mode-intro.html)
2.  [数据提取暂记](https://reactjs.org/docs/concurrent-mode-suspense.html)
3.  并发用户界面模式
4.  [采用并发模式](https://reactjs.org/docs/concurrent-mode-adoption.html)
5.  [并发模式 API 引用](https://reactjs.org/docs/concurrent-mode-reference.html)

它完全不同于传统的 React，非常有前途。

这篇文章探讨了使用悬念获取数据。请注意，所有功能都是实验性的，将来可能会有变化。

为了在并发模式下获得数据获取的暂挂优势，我们应该使用“即取即渲染”模式。这将要求我们在渲染之前开始提取；一个新的心智模型，而不是我们所习惯的(获取`useEffect`或`componentDidMount`)。)

# createFetcher

让我们创建一个封装了获取函数的“`fetcher`”。这可以是一个任意的返回承诺的`async`函数。

```
const fetcher = createFetcher(async url => (await fetch(url)).json());
```

这是一个普通的 GET fetcher，它接受一个 URL 作为输入，并假设一个 JSON 响应。通常情况下，我们会想创造更多的专业 fetchers。

一个取数器提供了两种方法:`prefetch`和`lazyFetch`。

如果你调用`prefetch`，它将启动`fetch`函数，你将得到一个*可挂起的*。suspendable 是一个具有两个属性的对象:`data`和`refetch`。`data`是获取承诺结果，但是如果承诺没有解决，它会抛出一个承诺。`refetch`将再次运行 fetch 函数并返回一个新的 suspendable。

如果您调用`lazyFetch`，您将得到一个类似 suspendable 的结果，带有回退数据和一个`lazy`标志。它永远不会真正挂起，但是你可以把它当作可挂起的，就像`prefetch`返回的一样。

`createFetcher`的打字稿如下:

```
type Suspendable<Data, Input> = {
  data: Data;
  refetch: (input: Input) => Suspendable<Data, Input>;
  lazy?: boolean;
};type Fetcher<Data, Input> = {
  prefetch: (input: Input) => Suspendable<Data, Input>;
  lazyFetch: (fallbackData: Data) => Suspendable<Data, Input>;
};export const createFetcher: <Data, Input>(
  fetchFunc: (input: Input) => Promise<Data>,
) => Fetcher<Data, Input>;
```

这个实现有点长:

```
export const createFetcher = (fetchFunc) => {
  const refetch = (input) => {
    const state = { pending: true };
    state.promise = (async () => {
      try {
        state.data = await fetchFunc(input);
      } catch (e) {
        state.error = e;
      } finally {
        state.pending = false;
      }
    })();
    return {
      get data() {
        if (state.pending) throw state.promise;
        if (state.error) throw state.error;
        return state.data;
      },
      get refetch() {
        return refetch;
      },
    };
  };
  return {
    prefetch: input => refetch(input),
    lazyFetch: (fallbackData) => {
      let suspendable = null;
      const fetchOnce = (input) => {
        if (!suspendable) {
          suspendable = refetch(input);
        }
        return suspendable;
      };
      return {
        get data() {
          return suspendable ? suspendable.data : fallbackData;
        },
        get refetch() {
          return suspendable ? suspendable.refetch : fetchOnce;
        },
        get lazy() {
          return !suspendable;
        },
      };
    },
  };
};
```

使用`prefetch`几乎总是首选。`lazyFetch`仅作为*渲染时提取*模式的变通方法。

一旦你得到一个 suspendable，你可以在渲染中使用它，React 会处理剩下的事情。

因为我们需要在创建 React 元素之前调用`prefetch`,所以我们只能在 render 函数之外进行。在编写时，它是在组件文件中全局完成的，假设我们知道我们想要什么作为初始的 suspendable。这可能会使测试变得困难。

# 使用可暂停

由`createFetcher`创建的 fetcher 在功能上是完整的，但是最好有方便的钩子来使用 suspendables。

最简单的就是`useSuspendable`。它存储本地状态中的单个 suspendable。

`useSuspendable`的实现如下:

```
export const useSuspendable = (suspendable) => {
  const [result, setResult] = useState(suspendable);
  const origFetch = suspendable.refetch;
  return {
    get data() {
      return result.data;
    },
    refetch: useCallback((nextInput) => {
      const nextResult = origFetch(nextInput);
      setResult(nextResult);
    }, [origFetch]),
    lazy: result.lazy,
  };
};
```

由`useSuspendable`钩子返回的结果几乎和普通的 suspendable 一样，但是有一点不同:如果你调用`refetch`，不是返回一个新的 suspendable，而是用新的 suspendable 替换状态值。

# 结束语

我犹豫要不要写这篇文章，因为它是高度实验性的，甚至可能在写完几天后就改变。然而，对于社区来说，尝试这个新的数据获取悬念世界并给出一些反馈是很重要的。