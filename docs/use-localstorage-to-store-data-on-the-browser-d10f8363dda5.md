# 如何使用 LocalStorage 在浏览器中存储数据

> 原文：<https://betterprogramming.pub/use-localstorage-to-store-data-on-the-browser-d10f8363dda5>

## 在浏览器中存储数据变得更加容易

![](img/59f025b11a166192f44fa3918b158f3f.png)

[丘特尔斯纳普](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在浏览器中存储数据是我们经常为 web 应用程序做的事情。通常，我们需要在浏览器上存储一些特定于用户的临时数据。我们可以利用浏览器的本地存储功能做到这一点。

对于本地存储，我们在浏览器上将数据存储为字符串。这是一个基于键值的存储介质，允许我们通过键获取数据，也可以用键作为标识符来设置值。所有数据都存储为字符串。如果一段数据不是字符串，那么在存储之前它会被转换成字符串。

一旦它们被保存，只要我们不删除数据，它们就在那里。我们可以通过数据的键删除数据，也可以同时删除所有数据。在同一个域上托管的每个应用程序都可以访问数据，因此我们可以在同一个域下托管多个 web 应用程序，并且仍然可以在所有应用程序中获得相同的数据。

这意味着我们可以轻松地将应用模块化为更小的应用，只要所有应用都托管在同一个域中，我们就不会有浏览器数据访问的问题。

与 cookies 不同，本地存储数据没有过期时间，所以如果我们不删除它，它仍然会在那里。

我们用`localStorage`对象访问浏览器的本地存储。如果我们不使用`http`协议访问`localStorage`，它将抛出一个`SecurityError`。这意味着任何以像`file:`或`data:`这样的协议开头的 URL 都会失败，并显示这个错误。

# 保存数据

我们可以使用`setItem`方法将数据保存到浏览器的本地存储器中。这需要两个参数。第一个参数是带有数据键的字符串，第二个参数是带有我们传递给第一个参数的相应键的值的字符串。

如果存储空间已满，它将引发异常。Safari 在私人模式下将存储配额设置为零字节，但它允许在私人模式下使用单独的数据容器进行存储。这意味着我们应该从`setItem`中捕捉异常。

例如，我们可以写:

```
localStorage.setItem('foo', 'bar');
```

然后一旦我们运行它，我们会在 Chrome 的本地存储部分的应用程序标签中看到这个条目。

我们也可以写…

```
localStorage.foo = 'bar';
```

…保存数据。

括号符号也适用于为本地存储赋值。例如，我们可以写…

```
localStorage['foo'] = 'bar';
```

…用键`'foo'`和值`'bar'`设置本地存储项目。

然而，我们不应该使用点或括号符号来代替`setItem`。我们不想无意中通过设置一个字符串来覆盖像`setItem`方法这样的东西，比如试图通过使用`setItem`作为键来保存数据。

## 保存对象数据

如果我们试图保存对象，我们将得到`[object Object]`作为值。为了实际保存内容，我们必须使用`JSON.stringify`方法。例如，不写…

```
localStorage.setItem('foo', {foo: 'bar'});
```

…我们写道:

```
localStorage.setItem('foo', JSON.stringify({foo: 'bar'}));
```

然后，我们用`foo`键得到`{“foo”:”bar”}`作为条目的值。

## 重复呼叫

如果我们用同一个键重复调用`setItem`方法，那么这个键的现有值将被覆盖。例如，如果我们写…

```
localStorage.setItem('foo', 1);
localStorage.setItem('foo', 2);
```

…然后我们得到`2`作为键为`foo`的条目的值，因为它是保存的最后一个值。

![](img/c161f4aa54837026ab1f61e9323254d8.png)

[Erda Estremera](https://unsplash.com/@erdaest?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 获取数据

我们可以用`getItem`方法获取数据，或者像其他对象一样使用点符号获取数据。`getItem`接受一个参数——我们想要从中获取值的键的字符串。如果为给定的键设置了值，它将返回一个字符串，如果没有设置，则返回`null`。

例如，我们可以写:

```
localStorage.getItem('foo');
```

然后，如果我们运行上一节中的`setItem`代码，就会得到值`'bar'`。

我们也可以写:

```
localStorage.foo;
```

或者，我们可以写:

```
localStorage['foo'];
```

括号符号对于访问带有相应键的值很方便，这些键不是有效的对象属性名称。

还有一个`key`方法来获取本地存储中给定位置号的键的名称。它有一个参数，是一个零或更大的整数。第一个位置编号为`0`。例如，如果我们有以下代码…

```
localStorage.key(1)
```

…然后我们获得本地存储中第二个条目的键名。

# 删除本地存储中的单个条目

我们可以用`removeItem`方法从本地存储器中删除一个给定的键。它接受一个参数，这个参数是一个带有条目键名的字符串。

例如，如果我们想删除带有键`'foo'`的条目，那么我们可以写:

```
loocalStorage.removeItem('foo');
```

上面的代码将删除键名为`'foo'`的本地存储条目。

或者，我们可以使用`delete`操作符删除给定键名的项目。例如，我们可以通过运行以下命令删除带有关键字`'foo'`的本地存储条目:

```
delete localStorage.foo;
```

我们也可以使用括号符号来做同样的事情，所以我们可以写…

```
delete localStorage['foo'];
```

…删除相同的条目。

然而，我们不应该使用点或括号符号来代替`setItem`。我们不想无意中通过设置一个字符串来覆盖像`setItem`方法这样的东西，比如试图通过使用`setItem`作为键来保存数据。

# 清除本地存储

我们可以使用`clear()`方法清除本地存储器中的所有条目。

我们可以写…

```
localStorage.clear()
```

…清除所有条目。一旦我们运行这个方法，我们不应该在浏览器的应用程序选项卡的本地存储部分看到任何东西。

# 浏览器兼容性

几乎所有的现代浏览器都有本地存储，所以在任何地方使用都是安全的。甚至 Internet Explorer 从版本 8 开始就支持它，所以它不是一项新技术。对于持久数据来说，它比浏览器端的 cookies 好得多，因为它不会过期，并且有操作数据的方法。

有了浏览器上的本地存储，在客户端存储数据比以往任何时候都更容易。我们可以通过调用上面概述的方法做很多事情。保存的内容将被保存为字符串。如果传入`setItem`的第二个参数的数据不是字符串，那么它会自动转换成字符串。