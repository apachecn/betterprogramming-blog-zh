# 基本的 JavaScript 面试问题

> 原文：<https://betterprogramming.pub/javascript-interview-questions-the-basics-c28f81bafbed>

## JS 面试 101

![](img/d5627cbebcdc2f9b7b5bc5aa068d174c.png)

[毛绒设计工作室](https://unsplash.com/@plushdesignstudio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/question?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

要想得到一份 JavaScript 开发人员的工作，不管是前端还是其他，我们需要搞定编码面试。

在这篇文章中，我们将看看一些基本问题和它们的答案。

# 未定义和 null 有什么区别？

它们都表示一个不存在的值，但是我们必须显式地设置`null`。

另一方面，`undefined`是未声明变量、已设置值的数组位置以及未设置值的已声明变量的值。

同样，`null`拥有类型对象，而`undefined`拥有未定义的类型。他们都是虚伪的，他们都是原始的类型。

下面将为我们带来`undefined`:

```
let x;
let y = undefined;
```

同样，没有赋值的数组位置也将是`undefined`:

```
let arr = [];
arr[1];
```

`arr[1]`是上面的`undefined`。

阵列中的孔也是`undefined`:

```
let arr = [,1];
arr[0]
```

当转换为布尔值时，它们都转换为`false`，如下所示:

```
Boolean(undefined);
Boolean(null);
!!undefined;
!!null;
```

`null`与`==`运算符相比等于`undefined`，因为它们是假的。当我们用`===`运算符比较它们时，它们不相等，因为它们属于不同的类型。

# &&运算符是做什么的？

`&&`操作符是*和*操作符，它找到第一个 falsy 表达式并返回它。如果没有假表达式，则返回最后一个表达式。

例如，如果我们有:

```
let foo = null && undefined && 'foo';
```

由于`null`和`undefined`为假，所以`foo`为`null`。

如果我们有:

```
let foo = true && 'foo';
```

然后我们将`'foo'`分配给`foo`。它进行短路评估，以防止不必要的检查。

这也是一个方便的快捷方式:

```
if (condition){
  doSomething();
}
```

因为上面的代码与:

```
condition && doSomething();
```

因为，如果`condition`是假的，那么`doSomething();`不会因为短路而运行。

# ||运算符是做什么的？

`||`操作者为*或*操作者。它找到第一个 the 表达式并返回它。

它还使用短路来避免不必要的检查。因此，在缺省选择之前的变量为假的情况下，设置变量的缺省值是很方便的。

例如，如果我们有:

```
let foo = null || undefined || 'foo';
```

然后我们用`'foo'`作为`foo`的值。

![](img/e4700c42a56299d755758537a8002a06.png)

Amy Hirschi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# +运算符是做什么的？

`+`操作符有三层含义。

## 1.一元+

如果把它放在一个表达式前面，它会试图把它转换成一个数字。将运算符放在表达式前面会使其成为一元运算符。

例如，如果我们想获得当前日期时间的 UNIX 时间戳，我们可以写:

```
+new Date();
```

`+new Date(2020,1,1);`将回归`1580544000000`。

这也是一种将字符串转换成数字的快速方法。例如:

```
+'1'
```

这将返回`1`。

因此，一元`+`操作符试图转换它后面的表达式。我们可以将表达式用括号括起来，转换成数字，如下所示:

```
+(1 + '1');
```

然后我们得到`11`，因为`1 + ‘1’`返回`'11'`。

如果表达式不能转换成数字，它将返回`NaN`。

## 2.加法算子

如果`+`运算符介于两个数字之间，它会将这两个数字相加。

例如，如果我们有:

```
1 + 2;
```

那么我们得到 3。

如果我们有:

```
let a = 1, b = 2;
a + b;
```

我们也得到了 3。

## 3.并置算符

如果表达式中的一个或多个操作数不是数字，它会尝试用不同的方式转换它。

例如，如果我们有:

```
1 + '1'
```

或者:

```
'1' + 1
```

然后我们得到`'11'`。

当然，如果我们有两个字符串作为操作数，那么`+`操作符将用于字符串连接:

```
'foo' + 'bar';
```

这将返回`“foobar”`。

# `==`和`===`有什么区别？

`==`和`===`的区别在于`==`只检查内容是否相等，而`===`检查内容和数据类型是否相等。

例如:

```
2 == '2'
```

这将返回`true`,因为它们的内容都是`2`。这是因为`==`确实属于强制类型。

用于`==`操作符的类型强制规则的完整列表如下:

1.  如果`x`和`y`具有相同的值，则与`===`操作符进行比较。
2.  如果`x`为`null`且`y`为`undefined`，则返回`true`。
3.  如果`x`为`undefined`且`y`为`null`，则返回`true`。
4.  如果`x`的类型为`number`且`y`为字符串，则返回`x == +y`。
5.  如果`x`是`string`类型且`y`是`number`类型，则返回`+x == y`。
6.  如果`y`为`boolean`类型，则返回`+x == y`。
7.  如果`y`是一个`boolean`，返回`x == +y`。
8.  如果`x`是一个`string`、`symbol`或`number`并且`y`是一个`object`，那么:

*   如果`y`是`String`实例，则返回`x == y.valueOf()`。
*   如果`y`是一个`Number`实例，则返回`x == y.valueOf()`。
*   如果`y`是任何其他类型的对象或值，则返回`x == y[Symbol.toPrimitive]()`。

# 结论

这些都是最基本的问题。为了搞定一场 JavaScript 面试，这些是开始学习的问题。