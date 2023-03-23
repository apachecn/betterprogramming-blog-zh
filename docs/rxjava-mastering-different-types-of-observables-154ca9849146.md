# 掌握 RxJava 中不同类型的可观测量

> 原文：<https://betterprogramming.pub/rxjava-mastering-different-types-of-observables-154ca9849146>

## 为您的需求选择正确的可观察对象

![](img/aad75dfa9c089316d859fa00c1b8eea2.png)

如果你对 RxJava 没有一点基本的了解，请浏览[rx Java:Android 中的多线程](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf?source=your_stories_page---------------------------)这篇帖子。如果你对 RxJava 有一个基本的概念，你就会知道 Observable 是一个向观察者发出值的组件。这篇文章是关于探索 RxJava 中不同类型的可观察对象。

# 不同类型的可观测量

一个可观察对象可以传递任意类型的多个值——文字、消息或事件，这取决于上下文。根据可观察对象发出数据的方式，它们被分类如下:

1.  可观察量
2.  单一的
3.  可完成的
4.  可能
5.  易流动的

对于每个可观察值，我们有以下观察者。

*   观察者
*   单一可观察的
*   CompletableObserver
*   也许可以观察到

在帖子[rx Java:Android 中的多线程](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf?source=your_stories_page---------------------------)中，我们已经探索了 Observable。所以让我们详细了解一下其他类型。

## 单一的

Single 是只发出一个项目或抛出一个错误的可观察对象。换句话说，Single 是一个可以发出单个 onSuccess 或 onError 的反应基类型。Android 中 Single 最简单的用法就是我们进行网络调用消耗一些数据的时候。Single 是使用最频繁的可观察值之一，因为我们在大多数应用程序中都有网络呼叫。它是接口`io.reactivex.SingleObserver<T>`的一个实现，有三个方法。

```
interface SingleObserver<T> {
    void onSubscribe(Disposable d);
    void onSuccess(T value);
    void onError(Throwable error);
}
```

*   订阅:只要订阅了观察器，就会调用
*   onError:每当在流的某个点抛出一个错误时，将被调用
*   onSuccess:如果我们从流中得到响应，将被调用，在这种情况下，我们可以处理响应

让我们假设有一个带有 TvShowsRepo 的 TvShows 应用程序，它是一个带有获取节目细节的方法的改进接口。

```
interface TvShowsRepo {@Get(Constants.BaseUrl +"/show-detaisl/")
fun getShowDetails(showId: Int):Single<SeriesResponse>}
```

这里我们使用 Single，因为它是一次性任务。我们将把回购注入到[视图模型](https://medium.com/better-programming/everything-to-understand-about-viewmodel-400e8e637a58)中，并消费数据，如下所示

这个方法`getShowDetails()`被执行并返回来自服务器的响应或错误。这就是我们通常一次性使用单个可观察值的方式。查看更多关于[单曲](http://reactivex.io/documentation/single.html)。

## 可完成的

Completable 只关心执行完成，而不发出值。Completable observable 不会发出任何数据，而是通知任务的状态是成功还是失败。

```
interface CompletableObserver<T> {
    void onSubscribe(Disposable d);
    void onComplete();    
    void onError(Throwable error);
}
```

*   Completable 将调用一次`onSubscribe`,以在此实例上设置一个 Disposable，以后可以随时使用它来取消订阅
*   如果延迟计算抛出异常，将调用一次`onError`
*   一旦延迟计算正常完成，没有任何错误，就会调用`onComplete`

我们通常使用 Completable 将值存储在 preferences 或 SQLite 本地数据库等不需要响应的地方。

```
@Singleton
class CachedRepoImpl @Inject constructor(private val cacheService: CacheService) : CacheRepo {fun saveUserData(userModel: String): Completable {
    return Completable.fromAction {
        cacheService.saveProfile(userModel)
    }
}}
```

在上面的`cacheService`中是一个类，它有一个共享参数的注入来保存和检索数据。

我们将此作为休闲消费

方法`saveUserDetails()`被执行，返回任务执行成功或失败的回调。这就是我们通常使用 Completable Observable 进行成功或失败回调的方式。查看更多关于 [Completable](http://reactivex.io/RxJava/2.x/javadoc/io/reactivex/Completable.html) 的信息。

## 可能

也许可观察可能会也可能不会发出一个值。这个可观察值可以用在你期望一个项目被可选地发出的情况下。

```
interface MaybeObserver<T> {
    void onSubscribe(Disposable d);
    void onSuccess(T value);
    void onError(Throwable error);
    void onComplete();
}
```

`onSubscribe()`以同步和异步方式为`MaybeObserver`提供取消或处理与`Maybe`连接的手段。

`onSuccess()`用一个项目通知`MaybeObserver`并且已经完成发送基于推送的通知。

`onError`通知经历了错误情况的`MaybeObserver`。

`onComplete`延迟计算正常完成后调用。

让我们看看下面的片段

当它被执行时，输出将是:

```
Hello
```

接下来，让我们看看另一个片段

当执行上面的代码片段时，输出将是:

```
Completed. No items.
```

第二个发出`onComplete` `Completed. No items`因为没有要发出的物品。第一个在`onSuccess`内输出`Hello`，并且在发射后不发射 onComplete。也许在某种程度上是。类似于单个可观察值，除了它是一个可选的东西。探索更多关于[或者](http://reactivex.io/RxJava/javadoc/io/reactivex/Maybe.html)的信息。

## 易流动的

“可流动”是一个可观察对象，当可观察对象生成大量数据，而观察者无法处理这些数据发射时，应使用该可观察对象。可观测源不支持背压。我们使用可流动的主要有两个方面:

1.  UI 无法处理大量数据流的复杂情况
2.  在已经观察到的数据发生变化时得到通知

第二种用法常见于聊天应用中，用于频繁更新用户界面

```
public interface Publisher<T> {public void subscribe(Subscriber<? super T> s);}
```

Subscribe 方法请求开始流数据。这是一个工厂方法，可以多次调用，每次都启动一个新的订阅。每个订阅仅适用于单个订户。订阅者应该只订阅一次单一发行者。如果发布者拒绝订阅尝试或以其他方式失败，它将通过`onError`回调发出错误信号。

```
Flowable.fromArray(1, 2, 3, 4).subscribe(
        **{** i: Int? **->** Timber.v("Entry %d\n", i) **}**,
        **{** e: Throwable? **->**Timber.v("Failed to process: %s\n", e) **}** ) **{** Timber.v("Done") **}**
```

以上是从数组中打印数字的易流动的简单用法。

当使用可流动时，必须使用称为背压的策略来处理溢流排放。如果不是，它抛出一个异常，比如 MissingBackpressureException 或`OutOfMemoryError`。我们将在接下来的文章中详细讨论这个问题。

探索更多关于[的流动](https://www.baeldung.com/rxjava-2-flowable)。

# 奖金

要了解关于 RxJava 的更多信息，请阅读本系列的前几部分:

*   [rx Java:Android 中的多线程](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)
*   [在 RxJava 中创建可观测量的不同方法—第 2 部分](https://medium.com/better-programming/rxjava-different-ways-of-creating-observables-7ec3204f1e23)
*   [用弹球图理解运算符—第 3 部分](https://levelup.gitconnected.com/rxjava-understanding-operators-with-the-marble-diagram-fc96addb0beb)

# 参考

[https://github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)