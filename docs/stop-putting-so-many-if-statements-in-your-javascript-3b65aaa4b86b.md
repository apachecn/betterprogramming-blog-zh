# 不要在 JavaScript 中放那么多 If 语句

> 原文：<https://betterprogramming.pub/stop-putting-so-many-if-statements-in-your-javascript-3b65aaa4b86b>

## 4 在代码中处理条件逻辑的其他方法

![](img/953544a1982a79ac89e8469e4487a9ae.png)

照片由[T R A V E L E E R G E E K](https://unsplash.com/@travelergeek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

> “对一个拿着锤子的人来说，一切看起来都像钉子”——亚伯拉罕·马斯洛

我喜欢把条件逻辑看作软件的面包和黄油。它给了开发人员构建有趣、有用和有趣的东西的能力。

处理条件逻辑最流行的方式是 **if 语句**。if 语句是通用的、灵活的、易于理解的，所以它的流行并不奇怪。

然而，还有其他处理条件逻辑的方法，这些方法经常被开发人员忽略。对于每一项工作来说，使用可靠的 if 语句是很容易的，但是学习其他技术会让你成为一个更有技巧和效率的程序员。

一个熟练的木匠不会对每项工作都使用同样的工具，一个熟练的开发者也不应该这样。

本文探讨了经典 if 语句的四种替代方案。

*   三元运算符
*   Switch 语句
*   逻辑运算符(&&和||)
*   查找地图

# 三元运算符

三元运算符是处理基本 if-else 条件的好方法。在下面的例子中，分配给`message`的值根据`hasError`是真还是假而变化。

用一个经典的 if 语句:

```
let message = "Thanks for contacting us! We'll be in touch shortly!"
if (hasError) {
  message = "Oops, that's an error. Please try again later!"
}
```

该解决方案首先将`message`设置为无错误版本，然后在必要时覆盖它。

现在用一个三元运算符代替:

```
const message = hasError
  ? "Oops, that's an error. Please try again later!"
  : "Thanks for contacting us! We'll be in touch!"
```

三元选项有一些显著的优点:

*   因为`message`只需要赋值一次，所以更加紧凑。
*   由于出现错误时不再需要覆盖`message`，我们可以使用`const`代替`let`。

在这种情况下，三元运算符是明显的赢家，但是不要被它冲昏了头脑。仅在需要相对简单的逻辑的情况下应用它，并且不要在一行中组合多个术语。你的同事会感谢你的！

# Switch 语句

Switch 语句是 if 语句最明显的替代品。它不是判断一个条件是真还是假，而是查看一个特定的值，并执行与之匹配的`case`块。

这使得 switch 语句不如 if 语句灵活，但也使它们更加简洁。让我们看一个例子:

首先，用一个 if 语句:

```
if (status === 200) {
  handleSuccess()
} else if (status === 401) {
  handleUnauthorized()
} else if (status === 404) {
  handleNotFound()
} else {
  handleUnknownError()
}
```

接下来，用一个开关:

```
switch (status) {
  case 200:
    handleSuccess()
    break
  case 401:
    handleUnauthorized()
    break
  case 404:
    handleNotFound()
    break
  default:
    handleUnknownError()
    break
}
```

该开关使用了更多的代码行，但避免了一次又一次地重复相等性检查，并且整体上具有更精简的外观。

编写 switch 语句时要考虑的一个重要细节是使用 breaks。与 if-else 链不同，switch 语句可以“落入”下一个 case。这可能会令人困惑，所以通常建议您在每个案例的结尾添加一个中断。

## 逻辑运算符(&&和||)

与其他编程语言相比，`&&`和`||`(“and”和“or”)操作符在 JavaScript 中的行为有所不同。这种特殊的行为赋予了这些操作符处理条件逻辑的独特能力。

下面是`&&`操作符在 JavaScript 中的工作方式:

1.  首先，它看操作符的左边。如果它的值是`falsy`，那么它甚至不看操作符的右边就返回那个值。
2.  如果发现左边的值是`truthy`，那么返回运算符右边的值。

类似地，这是`||`操作符的工作方式:

1.  首先，它看操作符的左边。如果它的值是`truthy`，那么它甚至不看操作符的右边就返回那个值。
2.  如果发现左边的值是`falsy`，则返回运算符右边的值。

这里的关键是`&&`和`||`操作符不一定需要返回布尔值(真或假)。这可能会令人困惑，但也可能很有用。

## 将&&运算符付诸实施

通常你想访问一个对象内部的属性，但是不能确定这个对象是否预先存在。

例如，也许您想使用用户的`name`属性来构造一条欢迎消息:

```
const message = `Welcome, ${user.name}!`
```

但是如果将`user`设置为`null`、`false`或`undefined`呢？

```
const message = `Welcome, ${user.name}!`
// TypeError: Cannot read property 'name' of null
```

如果`user`不是一个对象，我们试图访问它的`name`属性，JavaScript 将抛出一个错误。

这可以通过在代码中添加 if 语句来避免:

```
let message = null
if (user && user.name) {
  message = `Welcome, ${user.name}!`
}
```

这确实有用，但是`&&`操作者可以使它更简洁一点:

```
const message = user && user.name && `Welcome, ${user.name}!`
```

这种方法允许用`const`而不是`let`来设置`message`，并在一行代码中完成工作。好多了！

## 使用||运算符

`||`操作符非常适合分配后备值。

例如，假设您想要为当前用户创建一个`handle`变量。如果那个用户有一个有效的用户名，那么应该使用它，但是如果用户名被设置为`null`，那么应该使用一个后备值“来宾”。

首先，用一个简单的 if 语句:

```
let handle = 'Guest'
if (username) {
  handle = username
}
```

现在，通过使用`||`操作符:

```
const handle = username || 'Guest'
```

同样——更加简洁，只有一行代码。很大的进步！

# 查找地图

查找映射非常适合于获取一个与另一个相关联的值。

例如，假设我们想要获取与消息框状态相关联的颜色。典型的设置可能如下所示:

**成功是绿色**

**警告为黄色**

**信息为蓝色**

**错误为红色**

让我们写一个这样的函数。首先，用一个 if 语句:

```
function getStatusColor (status) {
  if (status === 'success') {
    return 'green'
  }
  if (status === 'warning') {
    return 'yellow'
  }
  if (status === 'info') {
    return 'blue'
  }
  if (status === 'error') {
    return 'red'
  }
}
```

这很好，但是查找映射可能更合适。对象文字是在 JavaScript 中实现查找映射的一种方式:

```
function getStatusColor (status) {
  return {
    success: 'green',
    warning: 'yellow',
    info: 'blue',
    error: 'red'
  }[status]
}
```

这是更苗条，更少重复。作为一个额外的好处，查找映射不一定需要硬编码——状态和颜色之间的关系可以是动态的，这种模式仍然有效。

# 摘要

If 语句是一个强大的工具，所有 JavaScript 开发人员都应该将它放在手边，但是还有其他处理条件逻辑的方法，这些方法有时更合适。

三元运算符非常适合在单行代码中处理 if-else 逻辑，但是它们应该只用于相当简单的用例。

当您对可能具有多个不同值的特定变量感兴趣时，Switch 语句是理想的。它们没有 if 语句强大，但是看起来更好。

与其他编程语言不同，JavaScript 中的`&&`和`||`操作符并不总是返回布尔值，这种行为很有用。当试图访问一个对象的属性时,`&&`操作符通常用于避免错误，而`||`操作符通常用于在第一选择不可用时给变量分配一个回退值。

查找映射是获取一个与另一个相关联的值的好方法，例如获取与消息状态相关联的颜色(成功的状态可能*将*映射到绿色)。

掌握这四种条件逻辑模式将使您在构建 JavaScript 时更加灵活，并使您成为更好的程序员。通过为工作选择正确的工具，您的代码将变得更加优雅、简洁和可维护。