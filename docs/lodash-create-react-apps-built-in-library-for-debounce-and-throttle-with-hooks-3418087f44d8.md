# Lodash:创建 React 应用程序的内置库，用于反跳和用钩子节流

> 原文：<https://betterprogramming.pub/lodash-create-react-apps-built-in-library-for-debounce-and-throttle-with-hooks-3418087f44d8>

## 使用 useCallback、useMemo、useRef 和自定义挂钩展示去抖和节流

![](img/351605b43e336088fc6b276666c61b06.png)

由 [Octavian Rosca](https://unsplash.com/@tavi004?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](/s/photos/future?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

继我们的[关于 Create React App 的 10 个有趣事实](https://medium.com/better-programming/10-fun-facts-about-create-react-app-eb7124aa3785)之后，今天我们呈现关于 Create React App 的第 11 个有趣事实:它内置了 Lodash，这是一个 JavaScript 库，为数组、数字、对象和字符串提供了实用函数。

虽然许多功能可以被 ES2015+取代，但 Lodash 仍然是一个具有额外便利实用程序的超级套件。我们可以利用内置的 Lodash 用钩子探索`debounce`和`throttle`。

# Lodash、去抖和节流

在我们之前的项目中，Lodash 总是一个需要安装的实用程序包。通过运行`npm i lodash`，在`package.json`中`lodash`包成为`[dependencies](https://medium.com/better-programming/package-jsons-dependencies-in-depth-a1f0637a3129)`的一部分。

这种模式随着 Create React 应用程序而改变。调用`npx create-react-app my-app`后，Lodash 就可以使用了。根本不需要安装。`lodash`不在`package.json`里，而是在`package-lock.json`里，和其他包一起安装。

Lodash 可以导入为:`import _ from “lodash”;`然后和下划线一起使用。以下是`debounce`和`throttle`的定义和用途:

```
/** 
 * Creates a debounced function that delays invoking func until
 * after wait milliseconds have elapsed since the last time the
 * debounced function was invoked.
 */
_.debounce(func, [wait=0], [options={}])/** 
 * Creates a throttled function that only invokes func at most 
 * once per every wait milliseconds.
 */
_.throttle(func, [wait=0], [options={}])
```

Lodash 也可以单独导入，使用时不带下划线。事实上，这是允许 Webpack 的[树摇动](https://webpack.js.org/guides/tree-shaking/)创建更小的包的推荐方式。

```
import debounce from "lodash/debounce";
import throttle from "lodash/throttle";debounce(func, [wait=0], [options={}])
throttle(func, [wait=0], [options={}])
```

为什么我们需要`debounce`和`throttle`？它们是出于性能原因而引入的。以用户输入为例。如果每次击键都调用后台调用来检索信息，我们可能会使整个系统崩溃。相反，我们给出一个等待时间来减少负载。`debounce`等待，直到用户在等待期间停止输入，然后发出更新请求。`throttle`有点不同——它将更新频率控制在等待`throttle`限制之下。

让我们创建一个简单的用户界面来说明这个概念。

![](img/2f7ea20e864d2c81b868d1a3687556cb.png)

在上面的输入字段中，用户输入`123456`。如果用户收听`onChange`并对每个输入值响应`console.log`，这些原始值是`1`、`12`、`123`、`1234`、`12345`和`123456`。如果没有`debounce`或`throttle`，它会在短时间内调用六个后端调用。

事实上，用户可能不太关心中间结果。用户关心的是当输入停止时`123456`的最终结果。`debounce`将是这种情况下的最佳选择。它只在打字停止一个滴答(等待时间)时处理数据。当用户快速键入`123456`时，只有一个去抖动值`123456`。

`throttle`工作方式略有不同。用户可能希望以受控的速率(等待时间)得到响应。默认情况下，它打印出第一次击键，`1`。其他中间节流值取决于等待时间和用户的打字速度。但保证最终结果`123456`会输出。

如果我们将原始值、去抖值和节流值放在一起，下面是一个示例输出。

```
Original Value = 1
Throttled Value = 1
Original Value = 12
Original Value = 123
Original Value = 1234
Original Value = 1234
Original Value = 12345
Original Value = 123456
Throttled Value = 123456
Debounced Value = 123456
```

# 错误的方法

让我们在 Create React App 环境中用`debounce`和`throttle`实现输入示例。`src/App.js`修改如下:

运行`npm start`并在输入栏快速输入`123456`。控制台显示以下结果:

```
Original Value = 1
Throttled Value = 1
Original Value = 12
Throttled Value = 12
Original Value = 123
Throttled Value = 123
Original Value = 1234
Throttled Value = 1234
Original Value = 12345
Throttled Value = 12345
Debounced Value = 1
Original Value = 123456
Throttled Value = 123456
Debounced Value = 12
Debounced Value = 123
Debounced Value = 1234
Debounced Value = 12345
Debounced Value = 123456
```

`debounce`和`throttle`都打印出每一次击键的变化。发生了什么事？

反应重新渲染是由状态或道具的变化引起的。在上面的方法中，当用户击键时，`onChange`触发`handleInputChange`(第 8-18 行)。第 11 行设置了一个新的状态值，这将导致新的呈现显示该值(第 22 行)。

对于每一次击键，都会生成一个新的`debounce`函数(第 12-14 行)和一个新的`throttle`函数(第 15-17 行)。这就是为什么他们简单地用`debounce`和`throttle`表示每一个值。

# 正确的方法

为了使`debounce`和`throttle`行为正确，我们需要创建在渲染之间被记忆的函数。记忆化是一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序。

`useCallback`是个不错的人选。它返回回调的记忆版本。只有当其中一个依赖关系发生变化时，此函数才会发生变化。`useCallback(fn, deps)`有条件地保留功能`fn`。

下面是应用`useCallback`来记忆`debounce`和`throttle`功能的`src/App.js`:

在第 8-13 行，`debounceHandler`是由`useCallback`记忆的`debounce`功能。因为它有一个空的依赖数组，所以它在组件的整个生存期内都被保留。

在第 15-20 行，`throttleHandler`是由`useCallback`记忆的`throttle`功能。因为它有一个空的依赖数组，所以它在组件的整个生命周期内都是保留的。

现在，这种方法奏效了。

当`useCallback`返回一个记忆化的回调时，`useMemo`返回一个记忆化的值。`useCallback(fn, deps)`相当于`useMemo(() => fn, deps)`，其中函数被记忆为一个值。

我们在`src/App.js`中使用`useMemo`:

在第 8-14 行，`debounceHandler`是由`useMemo`记忆的`debounce`函数。因为它有一个空的依赖数组，所以它在组件的整个生命周期内都是保留的。

在第 16-22 行，`throttleHandler`是由`useMemo`记忆的`throttle`功能。因为它有一个空的依赖数组，所以它在组件的整个生存期内都被保留。

这种方法也有效。

在[你想知道的关于 React Refs 的一切](https://medium.com/better-programming/most-things-you-want-to-know-about-react-refs-29901ebf28c6)中，我们对`useRef`进行了详细的描述。它返回一个可变的 ref 对象，该对象的`.current`属性被初始化为传递的参数。返回的对象将在组件的整个生存期内保持不变。

我们也可以使用`useRef`来记忆`src/App.js`中的`debounce`和`throttle`功能:

在第 8-13 行，`debounceHandler`被`debounce`函数初始化。它在组件的整个生命周期内保持在`current`值中，因为它没有被重新分配。第 26 行的调用需要调用`current`值。

在第 13-18 行，`throttleHandler`由`throttle`函数初始化。它在组件的整个生命周期内保持在`current`值中，因为它没有被重新分配。第 27 行的调用需要调用`current`值。

# 定制钩子接近

前面的方法是可行的。我们要为每个用例构建`debounce`或`throttle`处理程序吗？

定制钩子是一种重用编程逻辑的机制。让我们看看如何为`debounce`和`throttle`构建定制钩子。

为了简单起见，我们将定制钩子和用法放在同一个文件中。理想情况下，它们应该被归类为单独的文件。这是修改后的`src/App.js`:

第 5-8 行定义了一个定制钩子，`useDebouncedCallback`。它需要一个回调和等待时间，然后相应地生成一个`debounce`函数。因为第 6 行用`useCallback`和一个空的依赖列表将它包围起来，所以这个`debouncedFunction`在钩子的整个生命周期中不会改变。

第 18-21 行初始化`useDebouncedCallback`，由第 33 行使用。

第 10-13 行定义了一个定制钩子`useThrottledCallback`。这需要一个回调和等待时间，然后相应地生成一个`throttle`函数。因为第 11 行用`useCallback`和一个空的依赖列表将它包围起来，所以这个`throttledFunction`在钩子的整个生命周期中不会改变。

第 23-26 行初始化`useThrottledCallback`，由第 34 行使用。

这种方法适用于可重用的定制钩子。

除了反跳或抑制回调，我们还可以编写自定义钩子来反跳或抑制值。呼叫者可以为各种用途编程这些值。

这是修改后的`src/App.js`:

第 5-9 行定义了一个定制钩子，`useDebouncedValue`。它需要一个初始值和一段等待时间。在内部，它保持原始值，并为去抖值生成一个`debounce`函数。因为第 7 行用`useCallback`和一个空的依赖列表将它包围起来，所以这个`debouncedFunction`在钩子的整个生命周期中不会改变。这个自定义钩子返回一个数组，其中包含去抖动值和去抖动函数来更新该值。

第 19 行初始化`useDebouncedValue`。第 33 行使用`debounceHandler`来更新值。第 33-35 行监听`debouncedValue`变化，并相应打印出去抖值。

第 11-15 行定义了一个定制钩子，`useThrottledValue`。它需要一个初始值和一段等待时间。在内部，它保持原始值，并为调节值生成一个`throttle`函数。因为第 13 行用`useCallback`和一个空的依赖列表将它包围起来，所以这个`throttledFunction`在钩子的整个生命周期中不会改变。这个定制钩子返回一个带有 throttled 值的数组和 throttled 函数来更新该值。

第 20 行初始化`useThrottledValue`。第 33 行使用`throttleHandler`来更新值。第 37-39 行监听`throttledValue`的变化，并相应地打印出节流值。

# 不受控制的方法

在所有以前的方法中，我们使用由 React 团队推荐的[受控组件](https://medium.com/better-programming/to-be-or-not-to-be-2c372198a01c)。

对于去抖和节流的用例，使用不受控制的元件更容易。

下面是`debounce`的改版`src/App.js`:

17 行直接用`debounceHandler`，在 10-13 行定义。

同样，我们可以将`src/App.js`修改为`throttle`:

第 17 行直接使用`throttleHandler`，在第 10-13 行定义。

# 结论

Create React 应用程序中内置的 Lodash 为我们提供了函数式编程和数组、数字、对象和字符串操作的便利。

探索去抖和节流的用法很有趣，还有钩子——试试吧！

感谢阅读，我希望它是有帮助的。你可以在这里看到我的其他媒体出版物。