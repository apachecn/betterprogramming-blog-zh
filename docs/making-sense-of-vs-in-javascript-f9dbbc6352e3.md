# JavaScript 中== vs. ===的意义

> 原文：<https://betterprogramming.pub/making-sense-of-vs-in-javascript-f9dbbc6352e3>

## 双倍等于(==)测试标准相等，三倍等于(===)测试严格相等

![](img/6deef681e44927816b7921b426d7d99e.png)

照片由[查尔斯“鸭子”尤纳斯](https://unsplash.com/@unitasphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

平等应该是平等的吧？JavaScript 的三重等于比较总是让我困惑。大多数语言都有`==`而没有`===`。

在 JavaScript 中，两倍等于和三倍等于的区别如下:

> "如果操作数必须具有特定的类型和值，或者如果操作数的确切类型很重要，请使用[= = = =]严格相等运算符。
> 
> 否则，使用标准的等式操作符 *[==]* ，它允许你比较两个操作数的同一性，即使它们不是同一类型。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators)

我理解的关键是了解“类型强制”——这是普通 JavaScript 中一个奇怪的概念，普通 JavaScript 是一种动态类型语言。

发生类型强制是因为 JavaScript 试图抵抗编程错误，例如比较数字和字符串。

[严格相等比较算法](https://medium.com/u/e9031892baf5#sec-11.9.6)来比较两个操作数:

> [11.9.6](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.6) 严格相等比较算法
> 
> 比较 x === y，其中 x 和 y 是值，产生**真**或**假**。这种比较按如下方式进行:
> 
> 1.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )与[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )不同，则返回**假**。
> 
> 2.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )未定义，则返回**真**。
> 
> 3.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为空，则返回**真**。
> 
> 4.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是数字，则
> 
> → a .如果 *x* 为 **NaN** ，则返回 **false** 。
> 
> → b .如果 *y* 为 **NaN** ，则返回 **false** 。
> 
> → c .如果 *x* 与 *y* 的数值相同，则返回 **true** 。
> 
> → d .如果 *x* 为 **+0** 且 *y* 为**-0**，则返回**真**。
> 
> → e .如果 *x* 为**-0**且 *y* 为 **+0** ，则返回**真**。
> 
> → f .返回**假**。
> 
> 5.如果 [Type](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是字符串，那么如果 *x* 和 *y* 是完全相同的字符序列(长度相同，对应位置的字符相同)，则返回**true**；否则，返回**假**。
> 
> 6.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为布尔值，如果 *x* 和 *y* 都为**真**或都为**假**，则返回**真**；否则，返回**假**。
> 
> 7.如果 *x* 和 *y* 引用同一个对象，则返回 **true** 。否则，返回**假**。
> 
> 注意——该算法不同于[同值算法(9.12)](https://www.ecma-international.org/ecma-262/5.1/#sec-9.12) 对有符号零和 nan 的处理。

## 标准等式运算符

标准相等运算符(`==`和`!=`)使用[抽象相等比较算法](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)来比较两个操作数:

> [11.9.3](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3) 抽象等式比较算法
> 
> 比较 x == y，其中 x 和 y 是值，产生真值**或假值**。这种比较按如下方式进行:****
> 
> **1.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )与[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )相同，则**
> 
> **→ a .如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )未定义，则返回**真**。**
> 
> **→ b .如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为空，则返回**真**。**
> 
> **→ c .如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是数字，则**
> 
> **→ →一、如果 *x* 为 **NaN** ，则返回 **false** 。**
> 
> **→ →二。如果 *y* 为 **NaN** ，则返回 **false** 。**
> 
> **→ →三。如果 *x* 与 *y* 的数值相同，则返回 **true** 。**
> 
> **→ →四。如果 *x* 为 **+0** 且 *y* 为**-0**，则返回**真**。**
> 
> **→ → v .如果 *x* 为**-0**且 *y* 为 **+0** ，则返回**真**。**
> 
> **→ →六。返回**假**。**
> 
> **→ d .如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是字符串，那么如果 *x* 和 *y* 是完全相同的字符序列(长度相同，对应位置的字符相同)，则返回**真**。否则，返回**假**。**
> 
> **→ e .如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为布尔值，如果 *x* 和 *y* 都为**真**或都为**假**，则返回**真**。否则，返回**假**。**
> 
> **→ f .如果 *x* 和 *y* 引用同一个对象，则返回 **true** 。否则，返回**假**。**
> 
> **2.如果 *x* 为**空**且 *y* 为**未定义**，则返回**真**。**
> 
> **3.如果 *x* 为**未定义**且 *y* 为**空**，则返回**真**。**
> 
> **4.如果 [Type](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是 Number， [Type](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )是 String，则返回比较结果*x*= =[to Number](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)(*y*)。**
> 
> **5.如果 [Type](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是 String， [Type](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )是 Number，返回比较结果[到 Number](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)(*x*)= =*y*。**
> 
> **6.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为布尔值，则将比较结果[返回给 number](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)(*x*)=*y*。**
> 
> **7.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )为布尔值，则返回比较结果*x*= =[to number](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)(*y*)。**
> 
> **8.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )为字符串或数字，且[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )为对象，则返回比较结果*x*= =[top primitive](https://www.ecma-international.org/ecma-262/5.1/#sec-9.1)(*y*)。**
> 
> **9.如果[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *x* )是对象，[类型](https://www.ecma-international.org/ecma-262/5.1/#sec-8) ( *y* )是字符串或数字，则返回比较结果[to primitive](https://www.ecma-international.org/ecma-262/5.1/#sec-9.1)(*x*)=*y*。**
> 
> **10.返回**假**。**
> 
> **注 1——根据上述平等定义:**
> 
> **可以通过`"" + a == "" + b`强制进行字符串比较。**
> 
> **可以通过`+a == +b`强制进行数值比较。**
> 
> **布尔比较可以由:`!a == !b`强制。**
> 
> **注释 2-等式运算符保持以下不变量:**
> 
> **`A != B`相当于`!(A == B)`。**
> 
> **`A == B`等同于`B == A`，除了`A`和`B`的求值顺序不同。**
> 
> **注 3:等式运算符并不总是可传递的。例如，可能有两个不同的 String 对象，每个表示相同的字符串值；每个字符串对象都被`==`操作符视为等于字符串值，但是两个字符串对象并不相等。例如:**
> 
> **`new String("a") == "a"`和`"a" == new String("a")`都是**真**。**
> 
> **`new String("a") == new String("a")`为**假**。**
> 
> **注 4:字符串比较对代码单元值序列使用简单的相等测试。没有尝试使用 Unicode 规范中定义的更复杂、面向语义的字符或字符串相等性和排序顺序的定义。因此，根据 Unicode 标准在规范上相等的字符串值可能会被测试为不相等。实际上，该算法假设两个字符串都已经是规范化的形式。**

# **结论**

**一般来说，我更喜欢使用`===`和`!==`，除非我有充分的理由使用`==` 或`!=`，比如在空支票的情况下。**

**在检查 null 时，知道 null 和 undefined 与`==`相等是很有用的。**

# **附加阅读**

*   **[Panu pitkmki](https://bytearcher.com/contact/)对比[bytearcher.com](https://bytearcher.com/articles/equality-comparison-operator-javascript/)本文中的`==`和`===`:**

**[](https://bytearcher.com/articles/equality-comparison-operator-javascript/) [## JavaScript 中应该使用===还是==相等比较运算符？

### 您知道在 JavaScript 中有两种不同的等式比较操作符:===和==操作符，或者三元……

bytearcher.com](https://bytearcher.com/articles/equality-comparison-operator-javascript/) 

*   [MDN web 文档](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)解释[真实](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)、[虚假](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)、[类型强制](https://developer.mozilla.org/en-US/docs/Glossary/Type_Conversion)和[布尔](https://developer.mozilla.org/en-US/docs/Glossary/Boolean):

[](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) [## 真理

### 在 JavaScript 中，真值是在布尔上下文中遇到时被认为是真的值。所有值都是…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Truthy) 

*   根据 Basarat Ali Syed 的优秀免费书籍[*TypeScript Deep Dive*](https://basarat.gitbooks.io/typescript/docs/javascript/equality.html)，`==`和`===`在 JavaScript 和 TypeScript 中的工作方式相同:

 [## 平等

### JavaScript 中需要注意的一点是==和===之间的区别。随着 JavaScript 试图变得有弹性…

basarat.gitbooks.i](https://basarat.gitbooks.io/typescript/docs/javascript/equality.html) 

德里克·奥斯汀博士是《职业编程:如何在 6 个月内成为一名成功的 6 位数程序员 》一书的作者，该书现已在亚马逊上架。**