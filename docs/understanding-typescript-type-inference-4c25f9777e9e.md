# 理解 TypeScript 类型推理

> 原文：<https://betterprogramming.pub/understanding-typescript-type-inference-4c25f9777e9e>

## 通过嵌套泛型和接口的恐怖的意想不到的旅程

![](img/ea561030b9a13035dadd2d09b3bbcff1.png)

由[埃文·丹尼斯](https://unsplash.com/@evan__bray?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/horror?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

最近，Redux 团队决定将项目转换成 TypeScript。在 Unlock 的职位上使用了大量的打字稿后，我决定一头扎进去。

由于项目已经在它的`index.d.ts`中有了重要的类型，大部分工作是配置和寻找聪明的方法来处理足够短的可审查的拉请求。

进入野兽。[问题#3482](https://github.com/reduxjs/redux/issues/3482) 是一个开放的问题，有点像带有 Redux 存储增强器和`replaceReducer`的 edge-casey 情况。

长话短说，没有人看过它，所以我决定看看我是否能找到答案。很快，这让我掉进了兔子洞。

# 基本类型推理

TypeScript 文档很好地介绍了基本类型推理，并提供了一些简单的边缘案例。阅读这篇文章，简单了解一下类型推断的基础知识。

在本文中，我将着眼于推理，因为它与函数参数和返回值有关。

这里有两个函数推理的基本例子。第一个推理出函数的返回值，第二个推理出接口定义的类型。

希望这非常简单明了并且有意义。让我们更进一步！

# 泛型推理

同样，[TypeScript 文档](https://www.typescriptlang.org/docs/handbook/generics.html)提供了对泛型的不错的介绍。他们提供了许多泛型的定义，并展示了它们的强大功能和灵活性。

然而，即使在多次读取之后，我还是错过了最强大的用法，那就是它们在不显式传入类型的情况下推断类型的能力。在通用约束中使用类型参数的[一节中有一个这种用法的明确示例。](https://www.typescriptlang.org/docs/handbook/generics.html#using-type-parameters-in-generic-constraints)

让我们看一下这个例子:

这种设计的绝妙之处在于，TypeScript 编译器允许我们定义`getProperty`,然后用任何类型的变量调用`getProperty`,而无需显式传入类型。

在我理解这一点之前，我一直认为只有一种方法可以使用泛型，那就是要么在调用时显式地传递类型，要么在定义中提供默认值。

# 现实世界的例子

那么，我们如何在现实世界的例子中使用它呢？

再说说 [Redux](https://redux.js.org/) 。Redux 定义了一个同步事件循环，用于以单向方式传播状态变化。这是一种很奇特的说法，当我们想要改变什么的时候，只有一个管道可以让东西进入。

因为它是同步的，所以我们需要一种方法来挂接像从远程 API 加载信息这样的事情。通常，那些使用 Redux 的人使用一种叫做`middleware`的东西来做这件事，但是中间件是如何被修补到事件循环中的呢？

为此，Redux 提供了一种称为`store enhancer`的机制。存储增强器非常强大，因为它可以操纵存储的任何方面并对其进行修改。

从类型脚本的角度来看，我们需要考虑两种状态形状。存储本身的形状和由 Redux 事件循环修改的状态的形状。

Redux 存储有两个控制事件循环的函数:`getState`和`dispatch`。

状态的实际修改由终端用户提供的`reducer`函数处理。任何商店都允许用`replaceReducer`在运行时改变缩减器，它只接受新的缩减器作为参数。

`dispatch`接受一个参数，称为`action`，它是某种具有`type`成员的对象，由`reducer`用来决定如何路由动作和更新状态。

以下是上面所有以打字稿表示的散文:

再来分解一下:

*   一个`Action`有一个`type`和可能任意数量的其他属性。
*   一个`Reducer`接受预先存在的状态或`undefined`和一个动作，并总是返回该状态。
*   `Store`(在我们的简化视图中)有三个函数，`getState`、`dispatch`和`replaceReducer`，它们作用于通用接口`Store`中定义的状态和动作`S`和`A`。
*   `StoreEnhancer`是接受存储创建函数作为参数并返回存储创建函数的函数。
*   `StoreEnhancerStoreCreator`函数接受一个`reducer`和任何预定义的状态，并传递给该缩减器，然后创建一个`Store`。

跟我到目前为止？如果没有，不要烦恼，让我们继续，你可以随时回到上面的描述。此外…

# 我刚才告诉你的那些东西里面都有虫子

Magnus Helmersson 指出的问题在你的商店增强器扩展它创建的商店时开始起作用。

事实证明，如果您创建一个修改存储或修改状态的存储增强器，这种变化不会传播到新的存储。

雪上加霜的是，打字稿给我们的错误是这首抽象的现代主义诗歌:

```
Type '<S, A extends Action<any> = AnyAction>(reducer: Reducer<S, A>, preloadedState?: DeepPartial<S> | undefined) => { replaceReducer: (nextReducer: Reducer<S, A>) => void; dispatch: Dispatch<A>; getState(): S & ExtraState; subscribe(listener: () => void): Unsubscribe; [Symbol.observable](): Observable<...>; }' is not assignable to type 'StoreEnhancerStoreCreator<{}, ExtraState>'.
      Type '{ replaceReducer: (nextReducer: Reducer<S, A>) => void; dispatch: Dispatch<A>; getState(): S & ExtraState; subscribe(listener: () => void): Unsubscribe; [Symbol.observable](): Observable<S & ExtraState>; }' is not assignable to type 'Store<S & ExtraState, A>'.
        Types of property 'replaceReducer' are incompatible.
          Type '(nextReducer: Reducer<S, A>) => void' is not assignable to type '(nextReducer: Reducer<S & ExtraState, A>) => void'.
            Types of parameters 'nextReducer' and 'nextReducer' are incompatible.
              Types of parameters 'state' and 'state' are incompatible.
                Type 'S | undefined' is not assignable to type '(S & ExtraState) | undefined'.
                  Type 'S' is not assignable to type '(S & ExtraState) | undefined'.
                    Type 'S' is not assignable to type 'S & ExtraState'.
                      Type 'S' is not assignable to type 'ExtraState'.
```

谢谢打字稿！

还好我是写诗的，所以知道怎么解析上面的艺术品，关键时刻是`Type '(nextReducer: Reducer<S, A>) => void' is not assignable to type '(nextReducer: Reducer<S & ExtraState, A>) => void'`。

这是怎么回事？

当我们使用存储增强器时，它增加了状态(`S`)的定义，以包括存储增强器添加到状态(`ExtraState`)的额外状态。

这工作很好，直到我们更换减速器。`replaceReducer`的定义有两个问题。

首先，返回的`Store`不知道任何关于从`getState()`返回的状态的扩展或者存储增强器添加的存储的扩展。

这很容易解决，我们只需要将这些值传递给所有创建的`Store`类型，这样我们就可以在`replaceReducer`中使用它们。另一个错误更微妙，但同样重要。

你看到了吗？

`replaceReducer`通用模板类型`NewState`和`NewActions`的定义具有现有类型的默认值。

在热重装的正常情况下，我们用来替换现有减速器的减速器很可能与现有减速器具有相同的类型。然而，传递默认值有另一个副作用:它不再从传入的 reducer 中推断值。

这个问题很微妙，因为在您实际编写存储增强器之前，不会出现任何类型错误，正如这个问题的注释所示:

 [## StoreEnhancer 的 TypeScript 定义不允许使用 replaceReducer 问题进行状态扩展…

### 你想请求一个特性还是报告一个错误？StoreEnhancer 类型脚本接口似乎没有…

github.com](https://github.com/reduxjs/redux/issues/3482#issuecomment-525302027) 

这一次，TypeScript 给我们的错误是:

```
src/redux/store-enhancers/ext-enhancer.ts:25:66 - error TS2322: Type '<S, A extends Action<any> = AnyAction>(reducer: Reducer<S, A>, preloadedState?: PreloadedState<S>) => { replaceReducer: (nextReducer: Reducer<S, A>) => Store<S & ExtraState, A, ExtraState, {}>; dispatch: Dispatch<...>; getState(): S & ExtraState; subscribe(listener: () => void): Unsubscribe; [Symbol.observable](): O...' is not assignable to type 'StoreEnhancerStoreCreator<{}, ExtraState>'.
  Type '{ replaceReducer: (nextReducer: Reducer<S, A>) => Store<S & ExtraState, A, ExtraState, {}>; dispatch: Dispatch<A>; getState(): S & ExtraState; subscribe(listener: () => void): Unsubscribe; [Symbol.observable](): Observable<...>; }' is not assignable to type 'Store<S & ExtraState, A, ExtraState, {}>'.
    Types of property 'replaceReducer' are incompatible.
      Type '(nextReducer: Reducer<S, A>) => Store<S & ExtraState, A, ExtraState, {}>' is not assignable to type '<NewState = S & ExtraState, NewActions extends A = A>(nextReducer: Reducer<NewState, NewActions>) => Store<NewState & ExtraState, NewActions, ExtraState, {}>'.
        Types of parameters 'nextReducer' and 'nextReducer' are incompatible.
          Types of parameters 'state' and 'state' are incompatible.
            Type 'S' is not assignable to type 'NewState'.
              'S' is assignable to the constraint of type 'NewState', but 'NewState' could be instantiated with a different subtype of constraint '{}'.
```

说`Type 'S' is not assignable to type 'NewState'`是什么意思？

在这种情况下，这意味着`NewState`有可能与`S`有完全不同的形状，尽管它可能是好的。

这是因为对这两种类型的唯一约束是它们扩展了`{}`。

换句话说，`S`可能是一个接口`{ thing: 'this' }`，而`NewState`可能是一个接口`{ other: number }`，两者都满足`{}`定义的收紧，但是任何一个`S`的东西显然与`NewState`不是同一个类型，就像`const a: S = { thing: 'this' }`不同于`const b: NewState = { other: 5 }`一样。

这里的问题是假设`NewState`应该有一个默认值。相反，我们需要删除缺省值，这样类型推理就开始起作用了，我们从传入的实际缩减器中提取出正确的值`NewState`。

通过这些变化，我们得到:

现在，存储增强器的扩展被传播到存储中，并且可以在传入新的缩减器时正确地重新应用。

# 包扎

TypeScript 的类型推断是最强大的功能，也是最难理解的功能之一。

我们甚至还没有开始讨论关键字，你可以在官方文件中读到。然而，在大多数情况下，我发现基本的推理是我们所需要的。

以下是我在心理上使用的小备忘单:

1.  常量直接推断它们的类型(`const a = 1`有一个`number`的类型)。
2.  如果使用接口，函数的声明不需要声明它的类型，大概也不应该(允许类型推断生效)。
3.  可调用的东西(函数)可以通过多层泛型来推断类型。
4.  接口不能进行类型推断，所有泛型模板变量必须有显式传入的值，或者设置默认值。

编码快乐！

# 参考

*   [https://www . typescriptlang . org/docs/handbook/type-inference . html](https://www.typescriptlang.org/docs/handbook/type-inference.html)
*   [https://www . typescriptlang . org/docs/handbook/generics . html # using-type-parameters-in-generic-constraint](https://www.typescriptlang.org/docs/handbook/generics.html#using-type-parameters-in-generic-constraints)
*   【https://github.com/reduxjs/redux/issues/3482 
*   [https://redux.js.org/](https://redux.js.org/)