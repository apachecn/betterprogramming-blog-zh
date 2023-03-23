# JavaScript 三元运算符

> 原文：<https://betterprogramming.pub/the-javascript-ternary-operator-1589238d929b>

## 它的作用以及为什么您应该停止使用它

![](img/7006aba919f0771c046780fbe01be07d.png)

乔恩·泰森在 [Unsplash](https://unsplash.com/s/photos/question-mark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# JavaScript 运算符

JavaScript 有几种不同类型的操作符。这些包括比较、算术、逻辑等等。

这些运算符可以进一步定义为一元或二元。随着我们对三进制的理解，现在让我们来分析一下这两个术语之间的区别。

## 一元的

一元运算符只接受一个操作数，位于运算符之前或之后:

```
*operator* *operand || operand* *operator*for example:x++ || ++x
```

## 二进制的

二元运算符需要两个操作数，最常见的例子是基本的加法、减法、乘法和除法:

```
*operand operator* *operand*x + y; || x - y; || x * y; || x / y;
```

关系运算符、按位运算符、等式运算符和逻辑运算符也是二元的:

```
*operand operator* *operand*a few examples (non-exhaustive):x < y; *// the less-than relational operator*
x << y; *// the left shift bitwise operator*
x || y; *// binary logical operator*
x == y; *// the equals operator*
```

这就把我们带到了三元组。

# 三元运算符

> “条件(三元)运算符是唯一一个接受三个操作数的 JavaScript 运算符:条件后跟一个问号(`?`)，如果条件为[真值](https://developer.mozilla.org/en-US/docs/Glossary/truthy)则执行一个表达式，后跟一个冒号(`:`)，最后，如果条件为[假值](https://developer.mozilla.org/en-US/docs/Glossary/falsy)则执行一个表达式。— MDN

语法如下所示:

```
*condition* ? *value1* : *value2*
```

如果`condition`评估为真，三元运算符将具有`value1`的值。如果`condition`评估为假(或 falsy)，则它的值为`value2`。

```
let bouncerReply = (age >= 21) ? 'Access allowed' : 'Access denied';
```

您还可以链接条件运算符:

```
let message = (tempCelsius > 95) ? 'Too hot!' :
  (tempCelsius < 95) ? 'Too cold!' :
  (tempCelsius === 95) ? 'Just right!' :
  'This isn't porridge!';
```

1.  第一个问号检查条件`tempCelsius > 95`。
2.  如果`true`，返回`'Too hot!'`。如果不是，检查条件`tempCelsius < 95`。
3.  如果`true`，返回`'Too cold!'`。如果不是，检查`tempCelsius === 95`。
4.  如果那是`true` —它返回`'Just right!'`。否则，它继续执行最后一个冒号`:`后的表达式，返回`'This isn't porridge!'`。

# 该不该用？

事实上，我觉得有必要对上述三元表达式序列进行解释，这给出了我对这个问题的答案。下面是上面的代码使用`if..else`后的样子:

就个人而言，我建议默认使用`if..else`语句，而不是使用三元/条件操作符。只是我自己的看法。

链接或嵌套 ternaries 使他们被吹捧为微不足道的缩小。当然，`if..else`的例子要长几行代码，但是它的可读性也更好。把缩小留给缩小者。

# 有什么区别？

三元运算符不仅仅是对`if..else`结构的替换。

区别在于三元运算符是一个*表达式*，而`if..else`构造是一个*语句*。表达式返回值，而语句不返回值。

归根结底，三元运算符的使用是一种语法上的好处，正如我在其他地方写的一样，更少的代码并不总是等于更简洁的代码。

# 摘要

完全公开，我确实在自己的代码中使用了三元运算符。几乎每个实例都是单行变量赋值或简单的条件语句。亲爱的读者，一旦一种情况需要嵌套或链式 ternaries，我就转向`if..else`，这也是我推荐给你的。

我认为在编写代码时，可读性应该总是优先考虑的。bug 通常源于对以前编写的代码的误解。你未来的自己会感谢你，任何使用你代码的开发人员也会感谢你。

尤其是当代码长度或性能的差异可以忽略不计时。

感谢阅读！