# 在 RxJava 中创建观察值的不同方法

> 原文：<https://betterprogramming.pub/rxjava-different-ways-of-creating-observables-7ec3204f1e23>

## 有多少种方法可以创造一个可观察的？

![](img/db8456ab95e7e2d813e442b8b517b170.png)

照片由 [Mika Korhonen](https://unsplash.com/@mikakor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/observation?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Rx 代表无功扩展。是一个非常棒的反应库，我们可以很容易地将它集成到我们的应用程序中。

我们可以将`RxJava`理解为由一个称为`Observable`的组件发出的数据，由 Rx 库提供的底层结构将把更改传播到另一个组件`Observer`。简单地说，它是一个使用可观察流进行异步编程的 API。

[Rx Java—Android 中的多线程](https://medium.com/@pavan.careers5208/rxjava-multi-threading-in-android-302a7419edbf)有助于了解 Rx 的基础知识，关于`[Observable](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)`[s](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)`[Observer](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)`[s](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)`[Scheduler](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)`[s](https://medium.com/better-programming/rxjava-multi-threading-in-android-302a7419edbf)等的一切。所以，希望你已经了解了`RxJava`的基础知识，让我们从讨论`Observable`开始。

## 什么是可观测的？

在`RxJava`中，`Observable`是向`Observers`发送数据的源。我们可以将`observable` s 理解为供应商——他们处理数据并向其他组件提供数据。它做一些工作，并发出一些值。

以下是 RxJava 中不同类型的`Observable`

*   `Observable`
*   `Flowable`
*   `Single`
*   `Maybe`
*   `Completable`

我们将在下一篇文章中详细讨论每种类型，但请记住，不同类型的`Observable`有不同的用途。

`RxJava`库为`Observabl` e 创作提供了很多方法。让我们看看这些方法，并了解何时使用每种方法:

*   `create()`
*   `just()`
*   `defer()`
*   `empty()`
*   `never()`
*   `error()`
*   `range()`
*   `interval()`
*   `timer()`
*   `from()`

# **创建()**

通过函数从头开始创建一个`Observable`:

```
val createObserver = Observable.**create**(*ObservableOnSubscribe*<String> **{** emitter **->** emitter.onNext("Hello World")
    emitter.onComplete()
**}**)
```

create factory 方法是*首选的*实现自定义可观察序列的方式。本质上，此方法允许您指定一个委托，该委托将在每次进行订阅时执行。

![](img/86ac42207d532f3207612aae723b0d36.png)

*注意:*`*Flowable.create()*`**还必须指定* ***背压*** *当用户提供的函数生成的项目多于下游消费者请求的项目时，将应用的行为。**

*`ObservableOnSubscribe`是一个函数接口，它有一个`subscribe()`方法，该方法接收一个`ObservableEmitter`实例的实例，该实例允许以取消安全的方式推送事件。看一下界面:*

```
**/** ***@param*** <*T*> *the value type pushed
 */* public interface **ObservableOnSubscribe**<T> {

    */**
     * Called for each Observer that subscribes.
     ** ***@param*** *emitter the safe emitter instance, never null
     ** ***@throws*** *Exception on error
     */* void **subscribe**(@NonNull ObservableEmitter<T> emitter) throws Exception;
}*
```

# *就()*

*这是创建可观察的最简单方便的方法之一。`just()`通过获取一个预先存在的对象并在*订阅*时将该特定对象发送给*下游*消费者来构建一个反应类型。just 操作符将一个项目转换成一个发出该项目的`Observable`。*

*![](img/7f53e1141e87776c4a7a68564700ba67.png)*

```
*val justObservable = Observable.**just**(4, 5, 6, null)*
```

*记住，如果你将`null`传递给`Just`，它将返回一个`Observable`，这个`Observable`将发出`null`作为一个项。不要错误地认为这将返回一个空的`Observable`到`Just` ——它将返回一个发出`null`的`Observable`作为一个条目。*

*`*Observable.just()*` *放出 just 函数里面存在的东西。它可以接受 2 到 9 个参数。如果你在* `*just()*` *中传递一个列表或数组，它将只发出这个列表或数组。**

# ***延期**()*

*在观察者订阅并为每个`observer`创建一个新的`Observable`之前，`defer()`不会创建`Observable`。`Defer`操作符一直等到观察者订阅它，然后它生成一个`Observable`，通常带有一个`Observable`工厂函数。它为每个`subscriber`进行这种创建——尽管每个`subscriber`可能认为它订阅了同一个`Observable`，但事实上，每个订阅者都获得了自己单独的序列。*

*![](img/86ac42207d532f3207612aae723b0d36.png)*

```
*val observable = Observable.**defer** **{** val time = System.currentTimeMillis()
    Observable.just(time)
**}***
```

*在某些情况下，等到最后一分钟(即订阅时间)生成`Observable`可以确保它包含最新的数据。*

# *空()*

*`empty()`创建一个`Observable`使*不发射任何物品，但正常终止*。这种类型的源在订阅时立即发出完成信号。它返回一个没有向`Observer`发出任何条目的`Observable`，并立即调用它的`onComplete()`方法。*

*![](img/8beb1d4812bc32179d096bfab08772d3.png)*

```
*val empty = Observable.**empty**()

empty.subscribe(
        **{** v **->** *println*("This should never be printed!") **}**,
        **{** error **->** *println*("Or this!") **}**,
        **{** *println*("Done will be printed.") **}**)*
```

# *从不()*

*`never()`创建一个*不发射任何项目且不终止的`Observable`。*这种类型的信号源不发出任何`onNext`、`onSuccess`、`onError`或`onComplete`信号。这种类型的无功源对于测试或禁用组合运算符中的某些源非常有用。*

*![](img/1ef2bf3c83ab62db56e32a1b69531d03.png)*

```
*val never = Observable.**never**()

never.subscribe(
        **{** v **->** *println*("This should never be printed!") **}**,
        **{** error **->** *println*("Or this!") **}**,
        **{** *println*("This neither!") **}**)*
```

# ***错误()***

*`error()`向用户发出一个错误信号，该信号可以是预先存在的，也可以是通过`java.util.concurrent.Callable`产生的。*

```
*val error = Observable.**error**(IOException())
error.subscribe(
        **{** v **->** *println*("This should never be printed!") **}**,
        **{** e **->** e.*printStackTrace*() **}**,
        **{** *println*("This neither!") **}**)*
```

*![](img/86ac42207d532f3207612aae723b0d36.png)*

*`onErrorResumeNext()` 指示一个`ObservableSource`将控制传递给另一个`ObservableSource`，而不是调用`Observer.onError()`，如果它在序列链中遇到错误。*

*如果您将另一个`ObservableSource`恢复序列传递给一个`ObservableSource`的`onErrorResumeNext()`方法，如果原来的`ObservableSource`遇到错误，它不会调用其`Observer`的`onError()`方法，而是将控制权让给**恢复序列**，后者将调用`Observer`的`onNext()`方法，如果它能够这样做的话。在这种情况下，`Observer`可能永远不会知道发生了错误。您可以使用它来防止错误传播，或者在遇到错误时提供回退数据。*

```
*val observable = Observable.**fromCallable** **{** if (Math.random() < 0.5) {
        throw IOException()
    }
    throw IllegalArgumentException()
**}** val result = observable.**onErrorResumeNext** **{** error **->** if (error is IllegalArgumentException) {
        return@onErrorResumeNext Observable.empty()
    }
    Observable.error(error)
**}**for (i in 0..9) {
    result.subscribe(
            **{** v **->** *println*("This should never be printed!") **}**,
            **{** error **->** error.*printStackTrace*() **}**,
            **{** *println*("Done") **}**)
}*
```

# *范围()*

*`range()`创建一个发出特定范围的连续整数的`Observable`。`Range`操作符按顺序发出一系列连续的整数，您可以从中选择范围的开始及其长度。它为每个消费者生成一个价值序列。`range()`方法生成`Integer` s，`rangeLong()`生成`Long` s。*

*![](img/b04987745983a2d74e06972e09f526b0.png)*

```
*val greeting = "Hello World!"

val indexes = Observable.**range**(0, greeting.length)

val characters = indexes
        .map **{** index **->** greeting[index] **}** characters.subscribe(**{** character **->** *print*(character) **}**, **{** error **->** error.printStackTrace() **}**,
        **{** *println*() **}**)*
```

# *间隔()*

*`interval()`创建一个`Observable`，它发出一个由给定时间间隔分隔的整数序列。`Interval`操作符返回一个`Observable`，它发出一个无限的升序整数序列，在两次发射之间有一个您选择的固定时间间隔。*

*![](img/b04987745983a2d74e06972e09f526b0.png)*

```
*val clock = Observable.**interval**(1, TimeUnit.SECONDS)

clock.subscribe **{** time **->** if (time!! % 2 == 0L) {
        *println*("Tick")
    } else {
        *println*("Tock")
    }
**}***
```

# *计时器()*

*`timer()`创建一个`Observable`，它在我们指定的给定延迟后发出一个特定的项目。*

```
*val eggTimer = Observable.**timer**(5, TimeUnit.MINUTES)

eggTimer.blockingSubscribe **{** v **->** *println*("Egg is ready!") **}***
```

# *从*

*`from`用于将各种其他对象和数据类型转换为`Observable`。**当我们处理`Observable`时，如果您想要处理的所有数据都可以表示为`Observable` s，而不是表示为`Observable` s 和其他类型的混合，会更加方便。这允许您使用一组操作符来管理数据流的整个生命周期。***

# ***fromIterable()***

***`fromIterable()`向来自`java.lang.Iterable`源(如`List` s、`Set` s 或`Collection` s 或`custom Iterable` s)的项目发送信号，然后完成序列。将`Iterable`序列转换为发出序列中项目的`ObservableSource`。***

***![](img/b04987745983a2d74e06972e09f526b0.png)***

```
***val numbers = ArrayList<Int>()
numbers.add(1)
numbers.add(2)
numbers.add(3)
numbers.add(4)
val fromObservable = Observable.**fromIterable**(numbers)***
```

## ***fromArray()***

***`fromArray()`将`Array`转换为`ObservableSource`，发出`Array`中的项目。***

***![](img/b04987745983a2d74e06972e09f526b0.png)***

```
***val observable = Observable.**fromArray**(array)

observable.subscribe(
        **{** item **->** *println*(item) **}**, 
        **{** error **->** error.printStackTrace() **}**,
        **{** *println*("Done") **}**)***
```

******注意*** *: RxJava 不支持原语数组，只支持(泛型)引用数组。****

# ***fromCallable()***

***当消费者订阅时，调用给定的`java.util.concurrent.Callable`,其返回值(或抛出的异常)被传递给该消费者。***

***![](img/1ef2bf3c83ab62db56e32a1b69531d03.png)***

***换句话说，它返回一个`Observable`,当观察者订阅它时，它调用您指定的函数，然后发出从该函数返回的值。这允许您推迟执行您指定的函数，直到观察者订阅了`ObservableSource`。也就是说，它让函数变得“懒惰”***

```
***val callable = *Callable* **{** *println*("Hello World!")
    return@Callable ("Hello World!")
**}** val observable = Observable.**fromCallable**(callable)

observable.subscribe(
        **{** item **->** *println*(item) **}**, 
        **{** error **->** error.printStackTrace() **}**,
        **{** *println*("Done") **}**)***
```

## ***fromAction()***

***`fromAction()`返回一个`Completable`实例，该实例为每个订阅者运行给定的`Action`，并发出一个未检查的异常或简单地完成。***

***![](img/d796c5d1f9e3d0fdab6bc347ffe45c6b.png)***

```
***val action = *Action***{** *println*("Hello World!") **}** val completable = Completable.**fromAction**(action)

completable.subscribe(
        **{** *println*("Done") **}**, 
        **{** error **->** error.printStackTrace() **}** )***
```

# ***fromRunnable()***

***`fromRunnable()`返回一个`Completable`实例，该实例订阅给定的可观察对象，忽略所有值，只发出终端事件。***

***![](img/d796c5d1f9e3d0fdab6bc347ffe45c6b.png)***

```
***val runnable = **{** *println*("Hello World!") **}** val completable1 = Completable.**fromRunnable**(runnable)

completable.subscribe(
        **{** *println*("Done") **}**,
        **{** error **->** error.printStackTrace() **}** )***
```

******注意****:*`fromAction`*和* `fromRunnable` *的区别在于* `Action` *接口允许抛出被检查的异常，而* `java.lang.Runnable` *不允许。****

## ***来自未来()***

***`fromFuture()`将一个`java.util.concurrent.Future` 转换成一个`ObservableSource`。通过将对象传递到`from()`方法，我们可以将任何支持`Future`接口的对象转换成发出该对象的`Future.get()`方法的返回值的`ObservableSource`。***

***![](img/1ef2bf3c83ab62db56e32a1b69531d03.png)***

```
***val executor = Executors.newSingleThreadScheduledExecutor()

val future = executor.schedule(**{** "Hello world!" **}**, 1, TimeUnit.SECONDS)

val observable = Observable.**fromFuture**<String>(future)

observable.subscribe(
        **{** item **->** *println*(item) **}**,
        **{** error **->** error.printStackTrace() **}**,
        **{** *println*("Done") **}**)***
```

***这还不是关于可观测量的所有知识——还有更多。***

# ***奖金***

***渴望了解更多关于 Rx 的信息，请继续阅读 RxJava 上的系列[完整指南。](https://medium.com/@pavan.careers5208/complete-guide-on-rxjava-d997235e4eec)***

# *****参考文献*****

***[创建可观测量](http://reactivex.io/documentation/operators.html)***

***请让我知道你的建议和意见。感谢您的阅读。***