# 您现在可以避免的 7 个常见 JavaScript 错误

> 原文：<https://betterprogramming.pub/7-common-javascript-bugs-you-can-now-avoid-27d41c8d81c2>

## 同名函数、不精确的十进制算术、在循环中创建回调、丢失上下文等等

![](img/6a5c3aa241cb91d4bb48b910e44287ed.png)

尼古拉斯·皮卡德在 [Unsplash](https://unsplash.com/s/photos/vintage-tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在本文中，我们将看看 JavaScript 中可能遇到的一些常见错误，理解它们为什么会发生，并学习如何避免它们。

# 1.定义两个同名的函数

如果你来自 Java 或。在. NET 中，方法可以被重载，你可以试着创建两个名字相同但签名不同的函数[并期望语言根据提供的参数选择正确的函数。](/10-fundamentals-you-need-to-know-about-functions-in-javascript-8e74579b7a9a)

考虑下面的例子，我们有两个同名但参数数量不同的函数:

调用`sum(1, 2)`的结果是`NaN`。我们来了解一下原因。

首先，JavaScript 中的函数不一定是方法。在这个例子中，它们只是独立的函数。第二，语言中不支持重载函数。

当我们用相同的名字定义几个函数时，最后一个覆盖了所有其他的定义。调用函数时，会调用最后定义的函数。在我们的例子中，这意味着调用`sum`函数，期望三个参数中只有两个。在这种情况下，最后一个参数被设置为`undefined`。与`undefined`的算术运算产生`NaN`。

# 2.做连接而不是加法

您可能希望从 UI 中读取一些数字，然后将它们相加。

考虑下面的 HTML 表单，它有两个输入，代码从这些输入中读取数据并将它们的值相加:

为了简化，示例可能如下所示:

```
const no1 = '1';
const no2 = '2';
const sum = no1 + no2;
//'12'
```

在许多情况下，从 UI 中读取的值是字符串，加号(`+`)操作符既是加法操作符，也是连接操作符。当其中一个操作数是字符串时，另一个操作数被转换为字符串并执行连接:

```
const no1 = 1;
const no2 = '2';
const sum = no1 + no2;
//'12'
```

为了避免这个问题，我们需要确保所有的值都是数字。内置的`Number`函数可以用来将字符串转换成数字:

```
const no1 = Number('1');
const no2 = Number('2');
const sum = no1 + no2;
//3
```

# 3.忘记了十进制算术是不精确的

有时我们可能会忘记十进制算术在 JavaScript 中是不精确的。你需要意识到这一点，并避免相关的问题。

考虑下面的等式，即`false`:

```
console.log(0.1 + 0.2 === 0.3);
//false
```

那是因为`0.1 + 0.2`制造类似`0.30000000000000004`的东西。再比如，`0.3 — 0.1`造就`0.19999999999999998`。

整数运算是精确的，所以我们可以用它来避免前面的问题。在我们的例子中，我们乘以然后除以`10`:

```
console.log( (0.1*10 + 0.2*10)/10  === (0.3*10)/10 );
//true
```

# 4.允许在返回后自动插入分号

JavaScript 有一个自动分号插入机制，试图纠正程序。请注意在`return`语句末尾添加分号的后果。

考虑这个例子:

```
function getGameObject(){
  return 
    { name: 'Fornite'};
}
```

你可能认为这个函数返回的是一个[对象](https://medium.com/programming-essentials/7-things-you-should-know-about-objects-in-javascript-c0b2f251e160)，其实不是。它返回`undefined`:

```
console.log(getGameObject());
```

自动分号机制试图将分号放在`return`行的末尾，发现这是一个有效的代码，并将其放在。前面的代码相当于:

```
function getGameObject(){
  return;
    { name: 'Fornite'};
}
```

为了避免这个问题，将`{`放在与`return`语句相同的行上:

# 5.读取空的或未定义的属性

访问`null`和`undefined`上的属性是一个常见的场景，这会导致一个错误:

```
const book = null;console.log(book.title);
//Uncaught TypeError: Cannot read property 'title' of null
```

为了避免这样的异常，我们可以使用可选的链接操作符`?.`。

如果一个引用为 null([null 或未定义](https://medium.com/programming-essentials/things-on-null-and-undefined-that-you-should-know-6fda81f24745))，则`?.`操作符不会导致错误。相反，它返回`undefined`:

```
const book = null;console.log(book?.title);
//undefined
```

当标题存在且不为空时，以下代码返回`true`。当书是`null`或`undefined`时，它不会抛出错误:

```
if(book?.title){
  return true;
}
```

下面是我们如何检查书和书名的值是否不是虚假值(`null`、`undefined`、`''`、`0`或`NaN`):

# 6.在循环中创建回调

在循环中使用同一个变量并创建回调时，您可能会遇到以下问题。考虑下面的代码:

在循环内部，我们创建了三个回调函数，它们都引用同一个变量:`i`。当稍后调用这些回调函数时，它们都显示变量 `i`的最后一个值，即`3`。

我们可能期望看到`0 1 2`，但事实并非如此，因为所有的回调都引用同一个变量，这个变量会随着时间的推移而改变，它的最后一个值在循环执行 100 毫秒后显示。

我们可以通过使用`for`循环并用`let`声明循环变量来避免这个问题。它将用不同的变量为每次迭代创建一个块范围。所以这一次，每个回调指向不同的变量:

更好的选择是检查是否可以使用数组方法编写代码。在这种情况下，每次迭代都将有新的功能范围:

# 7.失去这个背景

函数可以用作方法。在这种情况下，函数中的关键字`this`引用相关的对象。

这个伪参数可能会变得令人困惑，因为它取决于函数是如何被调用的，而不是函数是在哪里定义的。对于来自其他语言的开发人员来说，这尤其令人沮丧，因为`this`总是指向当前对象。在 JavaScript 中，它反映了如何调用函数。

考虑下面的例子。`setTimeout`函数的回调中的`this`关键字没有引用与`showMessage`函数相同的对象:

解决这个问题的简单方法是使用 arrow 函数在方法中定义回调。这将确保回调中的`this`参数指向与`showMessage`方法中相同的对象:

这将解决大多数情况下的问题，但不是所有问题。当`this`可以指向一些其他对象时，仍然会有边缘情况，因为正如已经说过的，这取决于函数是如何被调用的。

考虑下面的例子。在第一个例子中，`showMessage`作为一个函数被调用，所以`this`在函数内部是`window` / `undefined`:

```
setTimeout(obj.showMessage, 100);
//undefined
```

解决方案是调用`showMessage`作为对象上的方法:

```
setTimeout(() => obj.showMessage(), 100);
//'Hi'
```

例如，如果我们使用析构语法从对象中提取方法，然后调用它，也会发生同样的问题。同样，它作为一个函数被调用，`this`关键字是`window` / `undefined`:

```
const { showMessage } = obj;
showMessage();
//undefined
```

我认为避免`this`参数问题的最佳选择是根本不使用`this`。下面，使用[闭合](https://medium.com/programming-essentials/you-will-finally-understand-what-closure-is-13ba11825319)完成相同的示例:

这一次，`obj`对象是使用定义后自动执行的`create`函数创建的。

`create`用一个方法`showMessage`返回一个对象。

`showMessage`是访问`msg`变量的闭包。返回的闭包函数可以访问这个变量，即使在它的父变量`create`已经执行之后。

关于如何在构建对象时避免使用`this`关键字的更多信息，你可以阅读不带“this”的 [JavaScript，它看起来像是一种更好的函数式编程语言](https://medium.com/programming-essentials/removing-javascripts-this-keyword-makes-it-a-better-language-here-s-why-db28060cc086)。

感谢阅读。