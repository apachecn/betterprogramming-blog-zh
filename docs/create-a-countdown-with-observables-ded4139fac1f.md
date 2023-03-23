# 创建一个可观察的倒计时

> 原文：<https://betterprogramming.pub/create-a-countdown-with-observables-ded4139fac1f>

## 并了解 RxJS 的基础知识

![](img/864e9d05f09d3d1a8cb8e20f5ea51fe0.png)

来自[像素](https://www.pexels.com/photo/brown-hourglass-on-brown-wooden-table-1178684/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[迈克](https://www.pexels.com/@mike-468229?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的照片

对于许多初级或中级前端开发人员来说，处理可观测量和订阅仍然是一场噩梦。我也必须承认，我很难理解反应式开发的原则。

然而，当我不得不编码倒计时时，一切对我来说都变得更加清晰，我决定在 [RxJS](https://rxjs-dev.firebaseapp.com/) Observables 的帮助下做这件事！

# 每个问题都有它的操作者

我们希望显示一个持续 30 秒的倒计时，并在完成时通知用户。为了做到这一点，我们需要以设定的间隔发出一串数值，这个间隔将是 1s。

RxJS 提供了广泛的运算符来帮助我们处理数据流。在这些之间，还有`interval` 和`timer`运算符。

`[interval](https://www.learnrxjs.io/operators/creation/interval.html)` 运算符返回每隔 *n* 秒发出一个值的可观察值。但是什么是可观察的呢？

可观察值是不断更新的数据流。

打个比喻，这就像在一个特定的频道上有一台收音机。如果我们订阅了《可观察》，也就是说，如果我们打开收音机，我们就可以随心所欲地实时收听直播。

在`interval` 操作者*、*的情况下，收音机被调到一个定期发出声音的频道。输出如下所示:

这个`[timer](https://www.learnrxjs.io/operators/creation/timer.html)`操作者是不同的。它返回一个可观测值，在我们作为参数输入的时间帧结束时，它将只发射一次。

基本上，广播会一直沉默，直到节目结束时，扬声器说:“结束”。像这样:

我们可以使用这两个操作符的组合能力来实现我们的目标并显示倒计时。

但是仍然缺少一些东西。其实只要我们订阅了`interval$`，`interval`，*，*运营商就会一直为我们“数数”。我们只需要记录相当于 30 秒的前 30 个值。

这就是`[take](https://www.learnrxjs.io/operators/filtering/take.html)`操作符的用武之地！它将允许我们陈述我们想听的发射次数，有点像记录我们喜爱的节目一定时间的设备。例如:

现在我们有了所有的工具，我们可以开始创建我们的倒计时了！

# 解决方案

因此，为了创建一个 30 秒的倒计时，我们将创建一个可观察对象，它将以 1 秒的间隔发送 30 次值，并在 30 秒后发送一个可观察对象，以通知时间到了。

```
const timerInterval$ = interval(1000); //1sconst timer$ = timer(30000); //30sconst times = 30;const countDown$ = timerInterval$.pipe(take(times));
```

最后，我们将订阅这两个观察值来记录发出的值。

但是由于`interval`可观测值将发出递增的值，而我们想要显示递减的值，我们将记录我们倒计时的总长度和发出的值之间的差异:

```
const sub = countDown$.subscribe(val =>console.log(time - val));const sub1 = timer$.subscribe(val => console.log('time is up!'));
```

您可以尝试代码并在 [StackBlitz](https://stackblitz.com/edit/rxjs-timer-ts?file=index.ts) 上查看输出！

[](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6) [## 如何在 Angular 中创建一个简单的商店

### 用单一的事实来源增强你的应用

medium.com](https://medium.com/better-programming/how-to-create-a-simple-store-in-angular-577a8f21a3d6) [](https://medium.com/better-programming/how-i-got-rid-of-state-observables-in-angular-2c69cbbbbd0e) [## 我是如何在 Angular 中消除状态可观测量的

### 我不会回去的

medium.com](https://medium.com/better-programming/how-i-got-rid-of-state-observables-in-angular-2c69cbbbbd0e)