# 在 JavaScript 中使用承诺进行异步编程

> 原文：<https://betterprogramming.pub/using-promises-for-async-programming-in-javascript-b544f1c4c4ca>

## 用 promise 对象保持代码运行

![](img/90250a1e73e05bc751fdaa2a28b8ab10.png)

[Max 陈](https://unsplash.com/@maxchen2k?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/promise?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

因为 JavaScript 是单线程语言，同步代码一次只能执行一行。这意味着，如果同步代码运行的时间超过了瞬时时间，那么它将停止其余代码的运行，直到正在运行的代码结束。为了防止运行时间不确定的代码阻碍其他代码的运行，我们需要异步代码。

# 承诺

要在 JavaScript 中做到这一点，我们可以在代码中使用承诺。承诺是代表一个过程的对象，该过程运行的时间不确定，并且可能导致成功或失败。为了在 JavaScript 中创建承诺，我们使用了一个`Promise`对象，它是创建承诺的构造函数。`Promise`构造函数采用一个名为 executor 函数的函数，该函数有`resolve`和`reject`参数。这两个参数都是我们调用的函数，要么履行承诺(这意味着用某个值成功地结束它)，要么拒绝承诺(这意味着返回某个错误值并将承诺标记为失败)。函数的返回值被忽略。所以，承诺不能回报承诺之外的任何东西。

例如，我们可以在 JavaScript 中定义一个承诺，如下所示:

```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('abc'), 1000);
});
```

上面的代码创建了一个承诺，该承诺在一秒钟后用值`abc`实现。因为我们在 executor 函数中运行`setTimeout`来在一秒钟内解析带有值`abc`的承诺，所以这是异步代码。我们不能在`setTimeout`的回调函数内返回值`abc`，所以我们必须调用`resolve('abc')`来取回解析后的值。我们可以通过使用`then`函数来访问已履行承诺的解析值。`then`函数接受一个回调函数，该函数将已履行承诺的解析值作为参数。你可以在那里得到价值，然后用它做你想做的事情。例如，我们可以这样做:

```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('abc'), 1000);
});promise.then((val) => {
  console.log(val);
})
```

当我们运行上面的代码时，我们应该看到`abc`被记录。正如我们所看到的，当通过调用`resolve`函数实现承诺时，承诺将提供值。

承诺有三种状态。它可以是待定的，这意味着承诺已经实现或被拒绝。它可以实现，这意味着操作成功完成。或者可以拒绝，说明承诺操作失败。

一个待定的承诺既可以有值地履行，也可以因某种错误而被拒绝。当一个承诺被履行时，相应的解析值被`then`函数获取，传递给`then`函数的回调函数被调用。如果一个承诺被拒绝，那么我们可以有选择地用`catch`函数捕捉错误，这个函数也接受一个带有错误的回调函数。`then`和`catch`函数都返回承诺，所以它们可以链接在一起。

例如，我们可以这样写:

```
const promise = (num) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (num === 1) {
        resolve('resolved')
      } else {
        reject('rejected')
      }
    }, 1000);
  });
}promise(1)
  .then((val) => {
    console.log(val);
  })
  .catch((error) => {
    console.log(error);
  })promise(2)
  .then((val) => {
    console.log(val);
  })
  .catch((error) => {
    console.log(error);
  })
```

在上面的代码中，我们有一个函数`promise`,当`num`为 1 时，它返回一个 JavaScript promise，该函数使用值`resolved`来实现该承诺，否则拒绝该承诺并返回错误`rejected`。所以我们跑:

```
promise(1)
  .then((val) => {
    console.log(val);
  })
  .catch((error) => {
    console.log(error);
  })
```

然后`then`函数运行，由于`num`为 1，所以`promise(1)`函数调用返回的承诺得到履行，解析后的值在`val`中设置。所以当我们运行`console.log(val)`时，我们得到`resolved`日志。当我们运行下面的代码时:

```
promise(2)
  .then((val) => {
    console.log(val);
  })
  .catch((error) => {
    console.log(error);
  })
```

然后`catch` 函数运行，因为`promise(2)`函数调用返回的承诺被拒绝，被拒绝的错误值可在 error 中设置。所以当我们运行`console.log(error)`时，我们得到`rejected`日志。

一个 JavaScript promise 对象有以下属性:`length`和`prototype`。对于构造函数参数的数量，`length`总是将其值设置为 1，也就是 1。`prototype`属性表示承诺对象的原型。

promise 还有一个`finally`方法来运行代码，不管 promise 是被履行还是被拒绝，代码都会运行。`finally`方法将回调函数作为参数，无论承诺的结果如何，您想要运行的任何代码都会在这里运行。例如，如果我们运行:

```
Promise.reject('error')
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.log(error);
  })
  .finally(() => {
    console.log('finally runs');
  })
```

然后我们记录了`error`和`finally runs`，因为最初的承诺被拒绝，原因是`error`。然后运行`finally`方法回调中的任何代码。

使用承诺编写异步代码的主要好处是，我们可以使用它们来顺序运行异步代码。为此，我们可以用`then`函数链接承诺。`then`函数接受一个回调函数，该函数在承诺兑现时运行。当承诺被拒绝时，还需要第二次辩论。为了链接承诺，我们必须返回另一个承诺作为`then`函数的第一个回调函数的返回值。它可以返回其他值，比如 nothing，如果我们不想将另一个承诺与现有的承诺联系起来的话。我们可以返回一个值，这个值将在下一个`then`函数中被解析和检索。它还会引发错误。然后由`then`返回的承诺被拒绝，抛出的错误作为值。它还可以返回一个已经履行或被拒绝的承诺，当一个`then`函数在它之后被链接时，它将获得履行的值，或者在`catch`函数的回调中获得一个错误原因。

例如，我们可以这样写:

```
Promise.resolve(1)
  .then(val => {
    console.log(val);
    return Promise.resolve(2)
  })
  .then(val => {
    console.log(val);
  })Promise.resolve(1)
  .then(val => {
    console.log(val);
    return Promise.reject('error')
  })
  .then(val => {
    console.log(val);
  })
  .catch(error => console.log(error));Promise.resolve(1)
  .then(val => {
    console.log(val);
    throw new Error('error');
  })
  .then(val => {
    console.log(val);
  })
  .catch(error => console.log(error));
```

在第一个例子中，我们将承诺链接起来，它们都解析为一个值。所有的承诺都已经分解成了价值观。在第二个和最后一个例子中，我们拒绝第二个承诺或者抛出一个错误。他们都做同样的事情。第二个承诺被拒绝，错误原因会被记录在`catch`函数的回调中。我们还可以链接待定的承诺，如下面的代码所示:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 1000);
});const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(2), 1000);
});promise1
  .then(val => {
    console.log(val);
    return promise2;
  })
  .then(val => {
    console.log(val);
  })
  .catch(error => console.log(error));
```

第一个`then`函数的回调返回了`promise2`，这是一个待定承诺。

# 方法

JavaScript 承诺有以下方法。

## Promise.all(可重复)

接受一个 iterable 对象，让我们在一些计算机上并行运行多个承诺，在另一些计算机上串行运行。这对于运行多个不依赖于彼此价值观的承诺来说非常方便。它接受一个带有承诺列表的 iterable，通常是一个数组，然后返回一个`Promise`,当 iterable 中的承诺被解析时，这个值被解析。

例如，我们可以编写如下代码来运行带有`Promise.all`的多个承诺:

```
const promise1 = Promise.resolve(1);
const promise2 = 2;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(3), 1000);
});
Promise.all([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  });
```

如果我们运行上面的代码，那么`console.log`应该记录`[1,2,3]`。正如我们所看到的，只有在所有承诺都实现后，它才返回一个解析的值。如果它们中的一些被拒绝，那么我们将得不到任何已解析的值。相反，我们将得到被拒绝的承诺返回的任何错误值。它将在第一个被拒绝的承诺处停止，并将该值发送给`catch`函数的回调。例如，如果我们有:

```
const promise1 = Promise.resolve(1);
const promise2 = Promise.reject(2);
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => reject(3), 1000);
});
Promise.all([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
  .catch(error => {
    console.log(error);
  });
```

然后我们从`catch`函数的回调中的`console.log`得到 2。

## `Promise.allSettled`

`Promise.allSettled`返回一个承诺，该承诺在所有给定承诺被解决或拒绝后解决。它接受一个带有承诺集合的 iterable 对象，例如，一个承诺数组。返回承诺的解析值是每个承诺最终状态的数组。例如，假设我们有:

```
const promise1 = Promise.resolve(1);
const promise2 = Promise.reject(2);
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => reject(3), 1000);
});
Promise.allSettled([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
```

如果我们运行上面的代码，那么我们会得到一个包含三个条目的数组，每个条目都是一个对象，它具有已履行承诺的`status`和`value`属性，以及一个对象，它具有被拒绝承诺的`status`和`reason`属性。比如上面的代码会记录`{status: “fulfilled”, value: 1}`、`{status: “rejected”, reason: 2}`、`{status: “rejected”, reason: 3}`。成功承诺记录`fulfilled`状态，拒绝承诺记录`rejected`状态。

## `Promise.race`

`Promise.race`方法返回一个承诺，该承诺解析为首先实现的承诺的解析值。它接受一个带有承诺集合的 iterable 对象，例如，一个承诺数组。如果传入的 iterable 为空，那么返回的承诺将永远处于待定状态。如果 iterable 对象包含一个或多个非承诺值或已经完成的承诺，那么`Promise.race`将返回到这些条目中的第一个。例如，如果我们有:

```
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(3), 1000);
});
Promise.race([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
```

然后我们看到`1`被记录。这是因为`promise1`是第一个被解决的问题，因为它在下一行运行之前就被解决了。同样，如果我们在数组中有一个非承诺值作为参数传入，如下面的代码所示:

```
const promise1 = 1;
const promise2 = Promise.resolve(2);
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(3), 1000);
});
Promise.race([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
```

然后我们得到同样的记录，因为我们传递给`Promise.race`方法的是数组中的非承诺值。同步代码总是在异步代码之前运行，所以同步代码在哪里并不重要。如果我们有:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 2000);
});const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(2), 1000);
});const promise3 = 3;
Promise.race([promise1, promise2, promise3])
  .then((values) => {
    console.log(values);
  })
```

然后我们记录了`3`,因为`setTimeout`将回调函数放在队列中稍后运行，所以它将比同步代码晚运行。

最后，如果我们有:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 2000);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(2), 1000);
});Promise.race([promise1, promise2])
  .then((values) => {
    console.log(values);
  })
```

然后我们在控制台日志中得到`2`，因为一秒钟内解决的承诺将比两秒钟内解决的承诺更早解决。

## `Promise.reject`

返回一个被拒绝的承诺。用一个作为`Error`实例的对象来拒绝一个承诺是很有用的。例如，如果我们有以下代码:

```
Promise.reject(new Error('rejected'))
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.log(error);
  })
```

然后我们得到`rejected`日志。

## `Promise.resolve`

`Promise.resolve`返回一个承诺，该承诺被解析为传递给`resolve`函数的参数的值。我们也可以传入一个带有`then`属性的对象，它的值是一个承诺的回调函数。如果值有一个`then`方法，那么承诺将由`then`函数实现的值来实现。也就是说，`then`函数的值的函数的第一个参数与`resolve`相同，第二个参数与`reject.`相同。例如，我们可以这样写:

```
Promise.resolve(1)
  .then((value) => {
    console.log(value);
  })
```

然后我们记录了`1`,因为`1`是我们传递给`resolve`函数的值，用来返回带有解析值`1`的承诺。

如果我们传入一个带有`then`方法的对象，如下面的代码所示:

```
Promise.resolve({
    then(resolve, reject) {
      resolve(1);
    }
  })
  .then((value) => {
    console.log(value);
  })
```

然后我们记录值`1`。这是因为`Promise.resolve`函数将运行`then`函数，并且设置为`then`属性的函数的`resolve`参数将被假定为一个像 promise 中的`resolve`函数那样被调用的函数。如果我们改为调用传入对象中的`then`函数内的`reject`函数，那么我们会得到一个被拒绝的承诺，如下面的代码所示:

```
Promise.resolve({
    then(resolve, reject) {
      reject('error');
    }
  })
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.log(error);
  })
```

在上面的代码中，由于承诺被拒绝，我们记录了`error`。

# 异步和等待

通过`async`和`await`，我们可以缩短承诺代码。在`async`和`await`之前，我们必须使用`then`函数，我们必须将回调函数作为所有`then`函数的参数。这使得代码很长，因为我们有很多承诺。相反，我们可以使用`async`和`await`语法来替换`then`函数及其相关的回调函数。例如，我们可以缩短以下代码:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 2000);
});const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(2), 1000);
});promise1
  .then((val1) => {
    console.log(val1);
    return promise2;
  })
  .then((val2) => {
    console.log(val2);
  })
```

收件人:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 2000);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(2), 1000);
});(async () => {
  const val1 = await promise1;
  console.log(val1)
  const val2 = await promise2;
  console.log(val2)
})()
```

我们用`await`替换了`then`和回调。然后，我们可以将每个承诺的解析值指定为变量。注意，如果我们使用`await`作为我们的承诺代码，那么我们必须把`async`放在函数签名中，就像我们在上面的例子中所做的那样。为了捕捉错误，我们使用了`catch`子句，而不是在最后链接`catch`函数。此外，我们在`catch`子句之后使用`finally`子句，而不是在底部链接`finally`函数来运行代码。

例如，我们可以写:

```
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 2000);
});
const promise2 = new Promise((resolve, reject) => {
  setTimeout(() => reject('error'), 1000);
});(async () => {
  try {
    const val1 = await promise1;
    console.log(val1)
    const val2 = await promise2;
    console.log(val2)
  } catch (error) {
    console.log(error)
  } finally {
    console.log('finally runs');
  }})()
```

在上面的代码中，我们获得了分配给变量的承诺的解析值，而不是在`then`函数的回调中获得值，就像上面的`const response = await promise1`行一样。此外，我们使用了`try...catch...finally`块来捕捉被拒绝承诺的错误，并使用了`finally`子句而不是传入回调的`finally`函数来创建无论承诺发生什么情况都运行的代码。

像其他任何使用承诺的函数一样，`async`函数总是返回承诺，不能返回其他任何东西。在上面的例子中，我们展示了我们可以用比使用回调作为参数传入的`then`函数更短的方式来链接承诺。

# 概述

有了承诺，我们可以轻松地编写异步代码。承诺是代表一个过程的对象，该过程运行的时间不确定，并且可能导致成功或失败。为了在 JavaScript 中创建承诺，我们使用了一个`Promise`对象，它是创建承诺的构造函数。

`Promise`构造函数采用一个名为 executor 函数的函数，该函数有`resolve`和`reject`参数。这两个参数都是我们调用的函数，要么履行承诺(这意味着用某个值成功地结束它)，要么拒绝承诺(这意味着返回某个错误值并将承诺标记为失败)。函数的返回值被忽略。所以，承诺不能回报承诺之外的任何东西。

承诺是有约束力的，因为承诺会回报承诺。promises 的`then`函数可以抛出一个错误，返回已解析的值，或者返回其他待定、已履行或被拒绝的承诺。