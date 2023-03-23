# JavaScript 访谈:空的、未定义的或未声明的变量之间有什么区别？

> 原文：<https://betterprogramming.pub/javascript-interviews-whats-the-difference-between-a-variable-that-s-null-undefined-or-cb1c8f41e6c3>

## 您将如何着手检查这些状态呢？

![](img/0a2a565b4280a5f9d2925a966c118fde.png)

[Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/empty?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 让我们从未申报的开始

未声明不是 JavaScript 保留字。这意味着我们在编写代码时不使用或赋值`undeclared`，相反，当我们试图使用一个未声明的变量时，我们会遇到产生的错误消息。

```
aVariable;
console.log(aVariable) // ReferenceError: aVariable is not defined
```

当我们试图使用一个没有使用关键字`let`或`const`声明的变量时，这种用法将导致上面的错误消息。

具有讽刺意味的是，这条错误消息揭示了我们今天看到的第一个有点令人困惑的场景:未声明变量的数据类型是`undefined`。

# 这就给我们带来了未定义

`undefined`是 JavaScript 中七种原始数据类型之一。

根据 ECMAScript [文档](https://www.ecma-international.org/ecma-262/7.0/#sec-undefined-value):

> "任何没有赋值的变量都有未定义的值."

我们在使用一个声明了但没有定义的变量时会遇到这种情况。在这些情况下，JavaScript 为我们赋值。该值为`undefined`:

```
let x; // *here we are declaring x without defining it*
console.log(x) // undefined
```

语句或方法中使用的未定义变量将返回`undefined`:

```
let x;
addTwo = (x) => {2 + x} // undefined
```

而一个不返回值的函数也会返回`undefined`。

当试图访问一个不存在的数组元素和/或对象属性时，我们也会遇到`undefined`:

```
let arr = [1,2,3];
console.log(arr[6]) 
// undefinedvar database = {};
console.log(database.names);
// undefined
```

在这些变量场景中，`undefined`是值*，而*是数据类型。

# null 呢？

再次引用 ECMAScript [文档](https://www.ecma-international.org/ecma-262/7.0/#sec-undefined-value)，`null`是:

> "一个原始值，它代表任何对象值的有意缺失."

这里的关键词是*有意*。如果我们忘记定义一个变量，那么`undefined`可能会被无意中赋值，而 JavaScript 永远不会赋值`null`。

与`undefined`一样，`null`也是原语，尽管如果您检查它的类型:

```
typeof(null) // "object"
```

你得到`“object”`。这是一个 JavaScript 遗留错误，与本文的讨论无关，但是请注意`null`是一个基本类型，而不是一个对象。

`null`是一个值，开发者可以将它赋给变量，以区分故意为空的值和返回`undefined`的错误。

为什么要用？MDN 的一个例子:

> 在 API 中，`null`经常在一个可以预期有对象但没有相关对象的地方被检索

# 测试空值和未定义值

`null`和`undefined`都是`falsy`值。因此，一个简单的方法来建立一个可以捕捉任一值的检查，如下所示(通过[堆栈溢出](https://stackoverflow.com/questions/5515310/is-there-a-standard-function-to-check-for-null-undefined-or-blank-variables-in)):

```
if( variable ) {
// *do execute*
} else {
 *// don't execute*
}
```

如果变量不是`undefined`或`null`，这将评估为`true`。

但是，要注意，`null`和`undefined`是松散相等的，但不是严格相等的。这是因为检查宽松等式会将两者都强制到`falsy`。

```
undefined == null // trueundefined === null // falsenull !== undefined // true
```

要检查变量是否未定义，您可以执行以下操作:

```
typeof variable !== 'undefined' ? *execute* : *don't execute*
```

`undefined`是类型和价值的缺失，`null`是价值的缺失。

# 总结

## 空

*   `null`被分配。
*   `null`永远是故意的。
*   `null`表示缺乏价值。

## 不明确的

*   `undefined`可以分配。
*   `undefined`往往是无意的。
*   `undefined`表示变量已声明但未定义。

## 两者

*   `null`和`undefined`都是原语。
*   `null`和`undefined`都是`falsy`值。
*   `null`和`undefined`大致相等。

感谢阅读！