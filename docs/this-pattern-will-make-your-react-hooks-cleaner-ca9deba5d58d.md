# 这种模式将使你的反应钩更干净

> 原文：<https://betterprogramming.pub/this-pattern-will-make-your-react-hooks-cleaner-ca9deba5d58d>

## 所以你不必重做工作

![](img/b38b4a5721d12903030a9016c0ba7dba.png)

照片由[安妮·尼加德](https://unsplash.com/@polarmermaid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hook?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# 目录

*   钩子是如何制造的
*   我们能把选项结合起来吗
*   为什么会这样
*   结论

# 钩子是如何制造的

当我们在 React 中构建一个带有复杂逻辑的定制钩子时，我们要么返回一个数组，要么返回一个具有状态和逻辑的对象。下面显示了该过程:

## 1.返回一个对象

第一个选项是从自定义挂钩返回一个对象，如下所示:

```
const customHook = (configs) => {
  const isLoading = true //logic
  const reload    = () => {/*logic*/} return **{ isLoading, reload }**
}
```

用法如下所示:

```
const **{ isLoading, reload }** = customHook(configs)
```

其中:

*   它对开发者友好，你不需要记住道具的顺序
*   智能感知将帮助您了解参数

但是当我们使用多个钩子时，就不好看了。它看起来会像这样:

```
const { 
  **isLoading: isLoadingWidget1, 
  reload: reloadWidget1** 
} = customHook(widget1Config)const { 
 **isLoading: isLoadingWidget2, 
  reload: reloadWidget2** 
} = customHook(widget2Config)const { 
  **isLoading: isLoadingWidget3, 
  reload: reloadWidget3** 
} = customHook(widget3Config)
```

这可不太好。我们必须将每个键重命名为另一个键，这样它们就不会冲突。

## 2.返回数组

另一个选项是从自定义挂钩返回一个数组，如下所示:

```
const customHook = (configs) => {
  const isLoading = true //logic
  const reload    = () => {/*logic*/} return **[isLoading, reload]**
}
```

用法如下所示:

```
const [**isLoadingWidget1, reloadWidget1]** = customHook(widget1Config)
const [**isLoadingWidget2, reloadWidget2]** = customHook(widget2Config)
const [**isLoadingWidget3, reloadWidget3]** = customHook(widget3Config)
```

其中:

*   它解决了多用途的问题。您不必重命名每个键。

但是:

*   你要维持道具的顺序
*   不具备与对象相同的智能感知

# 我们能把选项结合起来吗

我们能把这些模式结合起来吗？从某种意义上说，如果你想把它作为一个数组或者作为同一个钩子的对象。

是的，这是可能的。看看这个实现:

```
const customHook = () => {  
  const isLoading = true //logic
  const reload    = () => {/*logic*/} **const result = [isLoading, reload]** **result.isLoading = isLoading
  result.reload    = reload** return result
}
```

`result`是一个数组，但是我们给它添加了`isLoading`和`reload`键。

该实现将使我们能够使用钩子的返回值:

1.  **一个物体**

```
const **{ isLoading, reload }** = customHook(configs)
```

2.**一个数组**

```
const **[isLoading, reload]** = customHook(configs)
```

这两种选择都行得通。但是为什么…

# 为什么会这样

为什么可以同时使用数组和对象返回类型？为什么可以将键附加到数组上？

> 与其他编程语言中的数组一样，`Array`对象使[能够在单个变量名](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/Arrays)下存储多个项目的集合，并且具有用于执行公共数组操作的[的成员—](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#examples) [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)

如前所述，“数组对象”这揭示了一个事实，数组实际上只是一个对象，有一个特殊的构造函数和一组执行常见数组操作的方法。

## 证明数组实际上是一个对象

1.  **证据 1**

```
**typeof []** // 'object'
```

是的，`typeof`将导致`'object'`

2.**证明 2**

```
const **{0: first, 1: second}** = [1, 2, 3, 4] // first: 1, second: 2
```

我们可以使用索引来破坏数组对象，因为任何数组中的键都是对象的索引和值，它们是你在方括号`[]`之间传递的东西。这就是为什么我们能够破坏它，并以这种方式重命名密钥。

3.**证据 3**

```
**const { length }** =[1, 2, 3, 4]
```

数组是一个对象，其中也有`length`键。

4.**证据 4**

```
**Object.keys([1, 2, 3, 4]) **  // ['0', '1', '2', '3']
**Object.values([1, 2, 3, 4])** // [1, 2, 3, 4]
```

`Object.keys`和`Object.values`只作用于对象，数组是对象(特殊对象)。

# 结论

我们刚刚看到了一个模式，它允许我们将钩子的返回值作为数组或对象进行析构。我们已经看到这是多么有帮助。我们已经看到了为什么这是可能的，因为这一切都是关于这样一个事实，数组只是有一个上限的对象。

# 资源

1.  [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)