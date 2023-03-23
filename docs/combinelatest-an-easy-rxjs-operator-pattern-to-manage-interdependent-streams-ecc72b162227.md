# CombineLatest:管理相互依赖的流的简单 RxJS 操作符模式

> 原文：<https://betterprogramming.pub/combinelatest-an-easy-rxjs-operator-pattern-to-manage-interdependent-streams-ecc72b162227>

## 轻松管理必须关联多个数据流的角度分量

![](img/b52f4752743e93adfa91fb0bfe33b501.png)

作者照片

`CombineLatest`言出必行:它会记住任何订阅流发出的最后一个值，然后将它们组合起来。这是非常有用的，因为它使您不必在每次一个流发出时检查所有其他流的值。

如果您有一个包含许多相互依赖的数据流的角度分量，那么您可以使用本文中的`combineLatest`模式来轻松地关联这些可观测量的数据。

为什么有必要同时检查来自不同流的最新值？因为通常情况下，只有当来自不同流的数据相关时，才允许某些场景。例如，在下面的代码中，只有当来自另一个流的用户文档表明用户已经支付了他们的 sub 时，来自一个流的私人帖子才能显示。

下面我们来看看角形组件类。每次我细化代码的时候，我都会展示整个类，这样你就不用想象新代码是什么样子了。

NgRx 存储中的观察值`userDoc$`和`latestPost$`在`combinedObservable$$`中合并，并且在执行任何操作之前，在`if`语句中访问任何相互依赖的数据。`combinedObservable$$`将组合的数据流作为一个数组发出。在这种情况下，输入到`tap`操作符中的`dataArray`的格式是:`[{…}, {…}]`。

我使用了`tap`操作符来托管一系列`if`语句，这些语句可以用来同时访问值并对它们进行操作。如您所见，`combineLatest`操作符就像一个漏斗，让您可以访问来自任何订阅流的最新值:

# 添加类范围变量的可观察值

经常需要的下一个需求是对类范围变量中值的变化做出反应。这种变化可能是由于用户在模板中的选择，也可能是由于某些操作改变了变量值。在执行操作之前，可能需要将新的变量值与来自其他数据流的最新值进行比较或组合。

为此，有必要将类范围内的变量`this.classScopedVar`转换为可观察变量，并将其输入`combinedObservable$$`。

为什么要创造一个`this.classScopedVar`的可观察对象？为什么不在`combinedObservable$$`里直接用`this.classScopedVar`？原因很简单，只有当它的一个可观测量发出时，`combinedObservable$$`才会发出一个组合流。因此，有必要将`this.classScopedVar`转换成那些可观测量之一，以便它的值可以与来自其他流的最新值一起使用。

我已经在类中添加了变量`classScopedVar: string;`和
`classScopedVar$: BehaviorSubject<string>;`。然后我定义了`this.classScopedVar$ = new BehaviorSubject(this.classScopedVar);`并将`this.classScopedVar$`添加到`combinedObservable$$`流中。在第三个`if`语句中，您可以看到两个流的值是如何一起使用的(即`postObject[classVar]`):

*警告:在* `*combineLatest*` *流发出之前，所有输入流必须至少发出一次。确保您的所有流至少发出一次！*

# 合并流的问题是

解析为`true`的所有`if`语句将在`combinedObservable$$`的每次发射时被触发。这会导致问题，因为值不变的`if`语句会被不必要地触发。例如，随着`combinedObservable$$`的每次发射，第二个`if`语句将总是被触发，因为数值不变，从而连续触发`askUserToPay()`。

有一些方法可以确保这种情况不会发生:

*   根据需要，使用一个手动的类范围变量来覆盖单个的`if`语句。您可以根据需要打开和关闭该变量。作为一个类变量的替代，你可以使用另一个可观察的流，比如`BehaviorSubject()`，并把它输入到组合流中，但是要记住它会导致组合流的另一次发射，这必须由任何其他`if`语句来处理。
*   使用不同的`combineLatest`流将可观察性捆绑到不同的用例中。不要形成一个巨大的`combineLatest`流，这会让管理你的`if`报表成为一场噩梦。
*   使用`distinctUntilChanged`过滤掉与上次发射相同的发射。
*   使用`filter`运算符排除不相关的值。
*   如果你确定你已经完成，用`takeUntil()`终止合并流，或者如果你知道你需要多少排放，使用`take(#)`操作符。

我在下面的代码中实现了这些更改:

# 结论

感谢阅读。如果你喜欢这篇文章，可以考虑在这里[订阅](https://craftedwebpages.medium.com/membership)中级会员，以获得数以千计的其他文章。

我将在未来发布更多 Angular、RxJS、NgRx 和通用编程技巧。

# 学习资源

*   [组合测试](https://rxjs-dev.firebaseapp.com/api/index/function/combineLatest)操作员
*   [直到泄漏](https://medium.com/angular-in-depth/rxjs-avoiding-takeuntil-leaks-fb5182d047ef)
*   [RxJs 完成和退订的区别在可观察到吗？](https://stackoverflow.com/questions/52198240/rxjs-difference-between-complete-and-unsubscribe-in-observable/52198997)
*   [RxJS 订户退订与完成](https://stackoverflow.com/questions/48771350/rxjs-subscriber-unsubscribe-vs-complete)
*   [distinctUntilChanged](https://www.learnrxjs.io/learn-rxjs/operators/filtering/distinctuntilchanged) 运算符
*   [过滤](https://rxjs-dev.firebaseapp.com/api/operators/filter)操作符，还有这里的
*   [抽头](https://www.learnrxjs.io/learn-rxjs/operators/utility/do)操作符
*   [带](https://www.learnrxjs.io/learn-rxjs/operators/filtering/take)符