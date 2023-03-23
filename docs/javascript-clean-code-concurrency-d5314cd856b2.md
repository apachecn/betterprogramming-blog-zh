# JavaScript 干净代码:并发性

> 原文：<https://betterprogramming.pub/javascript-clean-code-concurrency-d5314cd856b2>

## 如何编写干净的异步代码

![](img/d5acbbdecd9ab9d43a6592b34bb4abe1.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

并发是大多数现代程序的重要组成部分。为了在 JavaScript 中实现这一点，我们必须使用非阻塞的异步代码。

在本文中，我们将研究如何以一种简洁、易于阅读和修改的方式编写异步代码。

# 用承诺代替回电

承诺从 ES6 开始就是一个标准对象，所以以前的异步回调应该全部被承诺取代。

如果我们有任何顺序代码，使用回调是一件非常痛苦的事情，因为我们必须在多个层次上嵌套它们。

例如，如果我们想要运行多个没有承诺的`setTimeout`回调，那么我们必须如下嵌套它们:

```
setTimeout(() => {
  console.log('foo');
  setTimeout(() => {
    console.log('bar');
    setTimeout(() => {
      console.log('baz');
    }, 200)
  }, 200)
}, 200)
```

正如我们所看到的，我们只有三个回调，嵌套已经很难看了。我们必须把它清理干净，这样看起来更舒服，也更容易理解。

我们可以通过以下承诺做到这一点:

```
const timeOutPromise = (str) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(str);
    }, 200)
  })
}
timeOutPromise('foo')
  .then((val) => {
    console.log(val);
    return timeOutPromise('bar');
  })
  .then((val) => {
    console.log(val);
    return timeOutPromise('baz');
  })
  .then((val) => {
    console.log(val);
  })
```

正如我们所看到的，对于承诺，我们可以用传入回调的`then`方法将它们链接起来。除了在`timeoutPromise`函数中，我们不需要嵌套回调，并且它只是两层嵌套，而不是三层或更多层。

我们在传递给`then`方法的回调参数中获得了一个承诺的解析值。

为了捕捉错误，我们可以使用带有回调函数的`catch`方法，如下所示:

```
const timeOutPromise = (str) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(str);
    }, 200)
  })
}
timeOutPromise('foo')
  .then((val) => {
    console.log(val);
    return timeOutPromise('bar');
  })
  .then((val) => {
    console.log(val);
    return timeOutPromise('baz');
  })
  .then((val) => {
    console.log(val);
  })
  .catch((err) => console.error(err))
```

![](img/51b4e12d1ff43be0517ceb7dbc0f3f2f.png)

照片由 [Tran Mau Tri Tam](https://unsplash.com/@tranmautritam?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# Async/Await 是链接承诺的更干净的语法

ES2017 引入了`async`和`await`语法，这是一种更干净的链接承诺的方式。

我们可以将上面的内容改写为:

```
const timeOutPromise = (str) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(str);
    }, 200)
  })
}(async () => {
  let val;
  val = await timeOutPromise('foo');
  console.log(val);
  val = await timeOutPromise('bar');
  console.log(val);
  val = await timeOutPromise('baz');
  console.log(val);
})();
```

它与以下内容完全相同:

```
const timeOutPromise = (str) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(str);
    }, 200)
  })
}timeOutPromise('foo')
  .then((val) => {
    console.log(val);
    return timeOutPromise('bar');
  })
  .then((val) => {
    console.log(val);
    return timeOutPromise('baz');
  })
  .then((val) => {
    console.log(val);
  })
```

一个不同之处是解析后的值通过赋值操作符被赋值给`val`。只要我们在承诺之前有`await`，这个任务就有效。

为了处理被拒绝的承诺，我们可以像处理同步代码一样使用`try...catch`子句:

```
const timeOutPromise = (str) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(str);
    }, 200)
  })
}(async () => {
  try {
    let val;
    val = await timeOutPromise('foo');
    console.log(val);
    val = await timeOutPromise('bar');
    console.log(val);
    val = await timeOutPromise('baz');
    console.log(val);
  } catch (err) {
    console.error(err);
  }
})();
```

`async`函数只返回承诺，所以不能作为通用函数使用。它们是承诺的语法糖，而不是它的替代品。

# 结论

要编写异步代码，承诺是必经之路。它们允许我们将多个链接在一起，而不需要嵌套回调。

如果异步代码没有作为承诺返回，我们应该将它们转换成承诺。为了链接它们，我们可以使用`then`方法。

为了从被拒绝的承诺中发现错误，我们可以使用`catch`方法。

`async`和`await`是承诺的句法糖。它们是一样的，但是`async`和`await`更短。

我们可以使用`try...catch`通过`async`和`await`从被拒绝的承诺中捕捉错误。