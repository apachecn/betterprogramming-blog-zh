# ES2018 中的出色新功能

> 原文：<https://betterprogramming.pub/great-new-features-in-es2018-10fd5cc3d91c>

## 包括对象中的扩展操作符、rest 操作符、异步迭代等等

![](img/b5b986907ca872c2f04f68b1c89fa4e3.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

自从 ES2015 发布以来，这本身就是一个巨大的飞跃，JavaScript 一直在快速改进。从那以后，JavaScript 规范中每年都会增加新的特性。

新的语法和新的 JavaScript 构建方法等特性一直在不断增加。在 ES2016 和 2017 中，`Object`对象增加了类似`Object.values`和`Object.entries`的方法。

ES2017 中加入了类似`padStart`和`padEnd`的字符串方法。ES2017 中还增加了`async`和`await`，后者是链接承诺的简写语法。

ES2016 中增加了数组的`includes`方法。ES2018 是另一个具有许多新功能的版本。在 ES2018 中，扩展语法现在可用于对象文字。还添加了休息参数。

还添加了`for await...of`循环，这是一个顺序遍历承诺的循环语法。添加了`SharedArrayBuffer`对象来表示无法分离的原始二进制数据。

一个`finally`函数也被添加到`Promise`对象中。

# 对象中的扩展运算符

spread 语法的工作方式是复制原始数组的值，然后将它们插入到另一个数组中，或者按照它们在数组中出现的顺序将它们作为函数中的参数列表以相同的顺序排列。

当 spread 运算符用于对象时，键值对的出现顺序与它们在原始对象中的出现顺序相同。

在 ES2018 中，spread 运算符处理对象文字。然后，可以使用 spread 操作符将一个对象的键值对插入到另一个对象中。

如果在同一个对象中有两个对象具有相同的关键点，并且 spread 运算符应用于这两个对象，那么后面插入的对象将覆盖前面插入的对象。

例如，如果我们有以下内容:

```
let obj1 = {foo: 'bar', a: 1};
let obj2 = {foo: 'baz', b: 1};
let obj3 = {...obj1, ...obj2 }
```

然后我们得到`{foo: “baz”, a: 1, b: 1}`作为`obj3`的值，因为`obj1`在`obj2`之前展开。它们都有`foo`作为对象中的键。

第一个`foo: 'bar'`由展开操作员插入到`obj3`。然后`foo: 'baz'`在`obj2`被合并后覆盖`foo`的值，因为它有相同的键`foo`，但是后来被插入。

这对于合并对象非常有用，因为我们不必遍历键并输入值，这比一行代码要多得多。

需要注意的一点是，我们不能在常规对象和可迭代对象之间混合 spread 运算符。例如，如果我们写下以下内容，我们将得到`TypeError`:

```
let obj = {foo: 'bar'};
let array = [...obj];
```

# Rest 运算符

rest 操作符是一个 JavaScript 操作符，我们可以在函数中以数组的形式存储不确定数量的参数。它看起来就像 spread 操作符，让我们将一个数组的条目或一个对象的键值对列表传播到另一个对象中。

例如，如果我们有一个函数，它有一个很长的参数列表，我们可以使用 rest 操作符来缩短函数中的参数列表。

rest 运算符与以下语法一起使用:

```
const fn = (a,b,..restOfTheArgs) => {...}
```

其中`restOfTheArgs`是一个数组，包含除前两个参数之外的参数列表。

例如，如果我们想要编写一个`sum`函数，它将一个不定的数字列表作为参数，并对这些数字求和，我们可以编写:

```
const sum = (a,b,...otherNums) => {
  return a + b + otherNums.reduce((x,y)=>x+y, 0);
}
```

正如我们所看到的，这对于有参数列表的函数来说非常方便。在此之前，我们必须使用函数中可用的`arguments`对象来获取参数列表。这并不理想，因为我们允许他们在参数中传递任何东西。

有了 rest 操作符，我们就拥有了两个世界的精华。我们可以有一些固定的参数，而其余的保持灵活。这使得函数比具有固定数量参数的函数更灵活，同时具有采用不确定数量参数的函数的一些灵活性。

`arguments`对象拥有函数的所有参数。此外，它不是一个真正的数组，所以数组函数对他们不可用。它只是一个对象，用索引和键来表示参数的位置。

像`[sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)`、`[map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`、`[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)`或`[pop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)`这样的方法不能在`argument`的对象上运行。它还具有其他属性。这给程序员造成了困惑。用 rest 操作符转换成数组的参数没有这些问题，因为它们是真实的数组。

要调用我们编写的`sum`函数，我们可以写:

```
const result = sum(1,2,3,4,5,6,7,8,9,10);
```

`result`将是 55，因为我们将所有的参数加在一起。`otherNums`是除 1 和 2 以外的所有数字的数组。

我们还可以使用 rest 操作符将一个参数列表析构为一个变量列表。这意味着我们可以用 spread 操作符将参数列表转换成数组，然后将参数数组分解成变量列表。

这非常有用，因为我们可以获得 rest 操作符所操作的数组条目，并将它们转换为命名变量。例如，我们可以写:

```
const sum = (a,b,...[c,d,e])=> a+b+c+d+e;
```

这样，我们可以使用 rest 操作符，但是要限制函数接受的参数数量。我们取函数参数`a`和`b`，也取`c`、`d`、`e`为参数。

然而，不使用 rest 操作符可能会更清楚，因为所有的参数都是固定的，我们可以直接列出参数。

# `for await...of`

`for await...of`循环允许我们创建一个循环，它迭代一个承诺列表以及一个普通可迭代的条目。

它适用于可迭代对象，如数组、字符串、`argument`对象、`NodeList`对象、`TypedArray`、`Map`、`Set`以及用户定义的同步和异步可迭代对象。

为了使用它，我们写:

```
for await (let variable of iterable) {
  // run code on variable}
```

可以用`const`、`let`或`var`来声明`variable`，可迭代对象是我们正在迭代的可迭代对象。我们可以迭代异步可迭代对象，如下所示:

```
const asynNums = {
  [Symbol.asyncIterator]() {
    return {
      i: 6,
      next() {
        if (this.i < 20) {
          return Promise.resolve({
            value: this.i++,
            done: false
          });
        }return Promise.resolve({
          done: true
        });
      }
    };
  }
};(async () => {
  for await (let num of asynNums) {
    console.log(num);
  }
})();
```

我们应该会看到 6 到 19 个日志。它也适用于异步生成器:

```
async function* asynNumGenerator() {
  var i = 6;
  while (i < 20) {
    yield i++;
  }
}(async () => {
  for await (let num of asynNumGenerator()) {
    console.log(num);
  }
})();
```

我们应该看到同样的事情被记录下来。它也适用于承诺:

```
const arr = Array.from({
  length: 20
}, (v, i) => i)let promises = [];for (let num of arr) {
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(num);
    }, 100)
  })
  promises.push(promise);
}(async () => {
  for await (let num of promises) {
    console.log(num);
  }
})();
```

正如我们所看到的，如果我们运行上面的代码，我们应该看到从 0 到 19 的顺序记录，这意味着承诺是按顺序迭代的。这非常方便，因为在引入循环语法之前，我们从来没有任何东西可以遍历异步代码。

# 无极. finally()

`finally`函数被添加到`Promise`对象中，该对象在承诺完成时运行。这意味着无论承诺是被实现还是被拒绝，它都会运行。

它需要一个回调函数，这个函数在承诺完成时运行。这让我们可以运行代码，而不管承诺如何结束。同样，这意味着我们不再需要在`then`和`catch`函数的回调中复制代码。

要使用`finally`功能，我们按如下方式使用:

```
promise
.finally(()=>{
  // run code that when promise is settled
})
```

`finally`很有用，因为我们希望在承诺结束时运行代码，比如清理代码或在代码结束后进行处理。

和叫`.then(onFinally, onFinally)`很像。然而，我们不必声明它两次来为它创建一个变量并传递它两次。对`finally`函数的回调没有任何参数，因为没有办法预先确定承诺的状态。

它用于我们不知道承诺会发生什么的情况，所以不需要提供任何论据。如果履行了承诺，那么在调用`finally`函数后，解析的值将保持不变。

例如，如果我们有:

```
Promise.resolve(3).then(() => {}, () => {})
```

这将解析为`undefined`，但是如果我们:

```
Promise.resolve(3).finally(() => {})
```

上面的代码将解析为 3。同样，如果承诺被拒绝，我们会得到:

```
Promise.reject(5).finally(() => {})
```

这将被值 5 拒绝。

# SharedArrayBuffer

`SharedArrayBuffer`是一个用来表示固定长度的原始二进制数据缓冲区的对象。

它类似于`ArrayBuffer`对象，但是我们可以用它在共享内存上创建视图。它不能超脱，不像一个`ArrayBuffer`。它的构造函数将缓冲区的长度作为参数。

长度是要创建的数组缓冲区的字节大小。它返回一个指定大小的`SharedArrayBuffer`,内容初始化为 0。

主线程上的多个工作线程可以创建和更新共享内存。但是，这可能需要一段时间，直到更改传播到所有上下文。

# 结论

有了 ES2018，我们有了更多方便的功能来帮助开发人员开发 JavaScript 应用。

亮点包括对象的 spread 操作符，它允许我们将键值对复制到其他对象，以及 rest 操作符，它允许我们传递可选参数。

`for await...of`循环让我们遍历异步代码的集合，这在以前是不容易做到的。