# JavaScript 干净代码:错误处理

> 原文：<https://betterprogramming.pub/javascript-clean-code-error-handling-9cd7e87fdbe3>

## 我们着眼于处理错误的最佳方式

![](img/ddd9c48d7bff428aa3774a6fcf0adaa2.png)

Max van den Oetelaar 在 [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

错误处理是程序的重要组成部分。在很多情况下，我们的程序会遇到意想不到的值，我们必须正确地处理它们。

在本文中，我们将研究如何处理它们，以便容易地发现错误并优雅地处理它们。

# 异常比返回代码更好

抛出异常更好，因为它们让我们知道存在错误，我们必须处理它。

大多数现代编程语言都有内置的异常，所以我们应该抛出它们，而不是返回一个错误代码。

错误代码不明确，可能会被遗漏。异常也更加干净，因为我们不必检查所有可能返回的代码。

例如，如果我们在函数中返回错误代码，那么我们可能会得到如下代码:

我们必须返回我们的`setNumFruit`方法中的所有错误代码。同样，在我们做类定义之后的事情之前，我们必须检查所有的错误代码。

我们可以抛出异常来代替:

我们通过将想要运行的代码包装在一个`try`块中，消除了检查所有错误代码的需要。现在我们可以捕捉错误，而不是检查所有可能返回的错误代码。

这比在做某件事之前检查所有错误代码要好得多——当代码变得更加复杂时，这一点尤其重要。

![](img/5b7f7fcea620d3dcbd62ed4526d126f8.png)

[泰勒](https://unsplash.com/@xoutcastx?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 编写 Try-Catch-Finally

我们应该将我们的`try`包装在抛出我们想要捕捉的异常的代码中。它为块范围的变量创建了自己的范围，所以用`let`或`const`声明的任何东西只能在`try`块中引用。

用`var`声明的变量被提升，这样它们就可以在块外被引用。即使它们在块外被引用，我们也不会得到一个错误。这会让我们得到`1`:

```
try {
  var x = 1;
} catch (ex) {
  console.error(ex);
}
console.log(x);
```

但这会让我们得到`Uncaught ReferenceError: x is not defined`:

```
try {
  let x = 1;
} catch (ex) {
  console.error(ex);
}
console.log(x);
```

# 不要忽略捕获的错误

我们应该在发现错误时报告。他们不应该被抓住，然后被忽视。这是因为我们不想掩盖潜在的问题。

报告异常让我们知道错误，然后相应地处理它。

上面的例子和`console.error`一样，调用了以下内容:

```
try {
  const error = fruitStand.setNumFruit(1);
  console.log(fruitStand.numFruits);
} catch (ex) {
  console.error(ex);
}
```

这是报告错误的方法之一。我们也可以使用其他库在一个中心位置报告错误。

# 不要忽视被拒绝的承诺

像任何其他异常一样，被拒绝的承诺也需要处理。它们可以用我们传递给`catch`方法的回调来处理，或者用`try...catch`块来处理`async`函数——它们是一样的。

例如，我们可以通过编写以下内容来报告错误:

```
Promise.reject('fail')
  .catch(err => {
    console.error(err);
  })
```

或者对于`async`函数，我们可以这样写:

```
(async () => {
  try {
    await Promise.reject('fail')
  } catch (err) {
    console.error(err);
  }
})()
```

# 提供带有异常的上下文

我们应该在异常中提供足够的上下文，以便其他开发人员可以在抛出异常时指出错误。

我们可以在 JavaScript 中获得异常的堆栈跟踪，但它可能不会提供我们需要的所有信息。

# 结论

当我们处理错误时，抛出异常比返回错误代码要好，因为它们让我们使用`try...catch`块来处理错误。这比检查多个错误代码要干净得多。

当我们抛出异常时，我们必须提供足够的细节来指出问题。

我们不应该在发现错误后就忽略它们。我们至少应该报告他们，这样他们才能被关注。

最后，被拒绝的承诺错误应该以与其他异常相同的方式处理。