# JavaScript 循环的 5 种不同迭代方式

> 原文：<https://betterprogramming.pub/5-different-ways-of-iterating-with-javascript-loops-fcb223b0f21f>

## JavaScript 循环可以让我们更有效率

![](img/245b7beaaca70d79975f82a6e61e5b5d.png)

[凯文泽尔](https://unsplash.com/@kai_wenzel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

有时我们希望在程序中连续运行某些任务。我们可以轻松地在 JavaScript 中使用循环来重复运行这些任务，而不是每次都为相同的任务重写代码。

# 循环意味着什么

循环是一种编程特性，只要满足特定条件，它就会迭代一组任务或指令。

循环检查条件。如果条件满足(即，如果它返回`true`，一组代码将运行。再次检查条件，如果仍然返回`true`，则再次运行该组代码。这个循环一直持续到条件返回`false`。一旦条件不再满足(即返回`false`，循环关闭。

## 循环的一个简单例子

假设我们想改变一个四项列表中三项的`background-color`,我们可以这样写。

首先，我们创建项目列表:

然后我们使用 CSS 对其进行样式化。首先将`background-color`设置为`aqua`:

要仅更改前三项，我们必须遍历列表:

```
var loop = document.querySelectorAll("li");
for (let i = 0; i < loop.length - 1; i++) {
     loop[i].style.backgroundColor = "blue";
}
```

`background-color`继续变化，直到达到第三项，这是我们设定的条件。

只要满足要求的条件，循环使得重复特定的任务变得非常容易。

# 循环语句的类型

有几种类型的循环语句。然而，它们本质上都执行相同的任务:它们重复一个动作一定的次数。

在许多情况下，一种类型的循环比其他类型的循环更合适。

不同类型的循环语句包括:

*   `for`循环语句
*   一个`for …` in 循环语句
*   一个`for …of`循环语句
*   一个`while`循环语句
*   一个`do … while`循环语句

## For 循环语句

一个`for`循环语句继续迭代，直到指定的条件不再返回`true`。

```
**syntax:** for (initialization statement; *condition statement* ; *increment statement*) {
  // *code block to be executed*
}
```

如果我们想写出保存在一个数组中的某个家庭的所有成员的年龄，我们可以使用下面的语句:

一个`for`循环由三条语句组成。

初始化语句(`i = 0;`)只执行一次。变量`i`也被称为*指数。它起着计数器的作用。它可以初始化为任何数字。*

`i`首先被初始化为`0`，这样循环就可以从第一个年龄项`48`开始写出年龄。

假设我们只想从索引`2`中写出年龄。我们可以将`i`变量初始化为`2`。`i = 2`跳转`48`和`16`。它从`18`开始写出年龄。

条件语句(`i < ageLen;`)设置循环的条件。只要该条件返回`true`，循环就会继续运行。

这里是这样的条件:只要`i`小于`ageLen`，等于`6`，循环就要写出所有的年龄项。`ageLen`变量存储年龄数组中的项目数。

我们也可以将循环设置为仅在`i`小于`5`(即`i < 5`)时运行。这里只会写出年龄`48`到`14`，省略了最后一个年龄(`11`)。

增量语句( `i++; or i + 1;`)在每次循环运行时将计数器加 1。循环写出第一个年龄，然后跳到下一个年龄，直到所有年龄都被写出。只有当条件返回`true`时，这种情况才会继续。

*注意:你也可以使用减量语句(* `*i — 1*` *)。*

## For … in 循环语句

一个`for…in`循环语句连续运行通过一个对象的属性。对于每个属性，执行一个特定的代码块。

```
**syntax:** for (properties in object) {
  // *code block to be executed*
}
```

以下示例遍历 company 对象的属性:

*提示:* *遍历数组时使用* `*for*` *循环或* `*while*` *循环，因为* `*for...in*` *语句遍历用户定义的属性和数组元素。*

## For … of 循环语句

`for…of` loop 语句连续遍历可迭代数据类型的值，如数组、映射、集合、字符串等。

```
**syntax:**
for (values of iterable) {
 // *code block to be executed*
}
```

下面的例子演示了一个简单的`for…of`循环语句:

```
var arr = [2, 4, 6];
let val;
for (val of cars) {
  console.log(val);
}
/* console logs 2, 4, 6... */
```

## While 循环语句

只要指定的条件评估为`true`，循环语句就会执行一个代码块。`while`循环与`for`循环非常相似。

```
**syntax:**
while (*condition*) {
 *// code block to be executed*
}
```

在下面的例子中，只要变量`i`小于`5`，循环中的代码就会继续运行:

## Do … while 循环语句

`do...while`循环语句是不同种类的`while`循环。该循环将在检查条件是否为`true`之前执行一次代码块。那么只要条件为`true`，就会重复循环。

```
**syntax:**
do {
 *// code block to be executed* }
while (*condition*);
```

我们可以像这样重写上面的`while`循环示例:

# 中断和继续语句

## break 语句

`break`语句用于关闭一个循环或停止任何迭代。

在下面的例子中，`break`语句结束循环一次`i === 2`:

## 连续语句

`continue`语句跳过了循环中的一次迭代。如果某个条件出现，那么它就进入循环中的下一次迭代。

在下面的例子中，`continue`语句跳过了`6`:

## 标签

一个`label`给了一个语句一个标识符，你可以用它在你代码的其他地方引用它。正如在 [MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/label)中提到的，“你可以使用一个标签来标识一个循环，然后使用`break`或`continue`语句来指示一个程序是应该中断循环还是继续执行。”

`continue`语句(引用或不引用标签)只能用于跳过一次循环迭代。

如果不引用标签，`break`语句只能用于结束循环或迭代。通过标签引用，break 语句可用于关闭指定的代码块:

# 结论

在本文中，我们回顾了所有的循环语句，以及如何使用它们来迭代程序中的某些任务。

感谢阅读。我希望你觉得这篇文章很有见地。

# **资源**

[W3schools](https://www.w3schools.com/js/js_loop_for.asp) [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#labeled_statement)
[口才 JavaScript](https://eloquentjavascript.net/02_program_structure.html)