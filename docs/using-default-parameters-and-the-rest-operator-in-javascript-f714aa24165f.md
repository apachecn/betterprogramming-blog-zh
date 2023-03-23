# 在 JavaScript 中使用默认参数和 Rest 操作符

> 原文：<https://betterprogramming.pub/using-default-parameters-and-the-rest-operator-in-javascript-f714aa24165f>

## 防止 bug 和开发人员错误

![](img/8c58044771ce7d186315fd1dd0d47272.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 2015 版本的 JavaScript 中，JavaScript 函数现在可以接受函数中的默认参数，并且有 rest 运算符来捕获数组中的可选参数。

这对于处理可选参数很方便，这些特性有很多用例。在我们拥有这两个特性之前，我们所拥有的只是带有被调用函数的参数列表的`arguments`对象。

为了防止可选参数出现未定义的错误，我们所能做的就是直接检查每个参数，看它们是否被定义，然后直接设置值。

这对开发人员来说是痛苦的，因为我们希望函数获得一长串可选参数。使用默认参数时，参数的默认值直接在函数的签名中设置。这消除了许多检查代码的代码行。

# 默认参数

JavaScript 的缺省参数很简单。为了在我们的代码中设置它们，我们编写以下代码:

```
const sum = (a,b=1) => a+b
```

在上面的代码中，我们将默认的参数值`b`设置为`1`，所以如果在调用`sum`函数时没有传入第二个参数，那么`b`会自动设置为`1`。

因此，如果我们如下调用`sum`,我们将返回`2`:

```
sum(1)
```

正如我们所看到的，现在我们不必担心可选参数是`undefined`，如果没有为参数设置默认值，这将是另一个结果。这消除了在 JavaScript 中使参数可选时出现的许多错误来源。

不使用默认参数的另一种方法是检查每个参数是否是`undefined`。为此，我们编写了以下代码:

```
const sum = (a,b,c)=>{
  if (typeof b === 'undefined'){
    b = 1;
  } if (typeof c === 'undefined'){
    c = 1;
  }
  return a+b+c;
}
```

如果我们不带参数调用下面的`sum`函数，我们得到:

```
const sum = (a,b = 1,c = 1)=> a+b+c;
sum(1) // returns 3
sum(1, 2) // returns 4
```

正如我们所看到的，我们用默认参数优雅地处理了丢失的参数。

对于默认参数，传入`undefined`等同于跳过参数。例如，如果我们调用下面的函数并传入`undefined`，我们得到:

```
const sum = (a,b = 1,c = 1)=> a+b+c;
sum(1,undefined) // returns 3
sum(1,undefined,undefined) // returns 3
sum(1, 2,undefined) // returns 4
```

注意`undefined`和跳过参数一样。

对于传递给函数的其他 falsy 值来说，情况并非如此。因此，如果我们传入`0`、`null`、`false`或空字符串，它们将被传入，默认的参数值将被覆盖。

例如，如果我们有下面的代码:

```
const test = (num=1) => typeof num;
test(undefined);
test(null);
test('');
test(0);
test(false);
```

我们得到`test(undefined)`的`number`、`test(null)`的`object`、`string`的`test(string)`、`test(0)`的`number`、`boolean`的`test(false)`。

这意味着除了`undefined`之外的任何东西都被传入，但是请注意，如果我们传入 falsy 值，然后对它们运行算术运算，falsy 值将被转换为`0`。

```
const sum = (a,b = 1,c = 1)=> a+b+c;
sum(1,null)
sum(1,null,false)
sum(1, 2,'')
```

所以，`sum(1, null)`返回`2`，因为`b`被转换为`0`，`c`的默认值为`1`。

`sum(1)`返回`1`，因为`b`和`c`转换为`0`。`sum(1, 2,’’)`为`3`如同`a`为`1`，传入`b`使其成为`2`而不是得到`1`的默认值，`c`为空字符串，转换为`0`。

默认参数是在调用时计算的，因此，如果没有参数传递给带有默认值的函数参数，则每次调用时都会设置默认参数。

例如，如果我们有:

```
const append = (val, arr = [])=>{
  arr.push(val);
  return arr;
}

append(1);
append(2);
```

`append(1)`返回`[1]`，而`append(2)`返回`[2]`，因为我们没有向每个函数调用传递一个数组，所以`arr`在每次运行时都被设置为一个空数组。

知道我们可以在默认参数中传递函数返回值也很重要，所以如果我们有一个函数返回一些东西，我们可以在默认参数中调用它并将返回值赋给参数。

例如，我们可以写:

```
const fn = () => 2
const sum(a, b = fn()) => a+b;
```

然后，如果我们调用`sum(1)`，当`fn`函数返回`2`时，我们得到`3`。如果我们想在将值作为参数赋值之前预先处理和组合值，这是非常方便的。

默认参数的另一个重要特性是，给定参数左侧的参数可作为默认值分配给该参数，因此我们可以有一个类似于下面函数的函数:

```
const saySomething = (name, somethingToSay, message = `Hi ${name}. ${somethingToSay}`) => ({
  name,
  somethingToSay,
  message
});
```

注意，我们给`saySomething`函数中的`message`参数分配了一个表达式。这对于操作数据，然后像我们之前通过赋值函数那样进行赋值是非常有用的。

我们还可以看到，默认参数依赖于它左侧的参数。这意味着默认参数不必是静态的。

所以，我们用填充了前两个参数的方式来调用它，比如`saySomething(‘Jane’, ‘How are you doing?’)`。我们得到:

```
{name: "Jane", somethingToSay: "How are you doing?", message: "Hi Jane. How are you doing?"}
```

返回的`message`是我们定义的模板字符串。

我们不能调用嵌套在函数中的函数来获取作为默认参数值的返回值。例如，如果我们写:

```
const fn = (a = innerFn())=>{
  const innerFn = () => { return 'abc'; }
}
```

这将导致抛出一个`ReferenceError`，因为当默认参数被定义时，内部函数还没有被定义。

我们也可以将默认参数值设置在必需参数的左侧。参数仍然从左到右传递给参数，所以如果我们有:

```
const sum = (a=1,b) => a+b
```

如果我们有`sum(1)`，我们有`NaN`返回`1`。它被添加到`undefined`中，因为我们没有为第二个参数传递任何东西，所以`b`是`undefined`。

然而，如果我们写`sum(1,2)`，那么`3`被返回，因为我们已经将`a`设置为`1`并且将`b`设置为`2`。

最后，我们可以使用析构赋值来设置默认参数值。例如，我们可以写:

```
const sum = ([a,b] = [1,2], {c:c} = {c:3}) => a+b+c;
```

然后，我们毫无争议地称之为`sum`。我们得到`6`是因为 JavaScript 提供的析构赋值特性将`a`设置为`1`、`b`设置为`2`、`c`设置为`3`。

# Rest 运算符

rest 操作符是一个 JavaScript 操作符，我们可以在函数中以数组的形式存储不确定数量的参数。

它看起来就像 spread 操作符，让我们将一个数组的条目或一个对象的键值对列表传播到另一个对象中。

例如，如果我们有一个函数，它有一个很长的参数列表，我们可以使用 rest 操作符来缩短函数中的参数列表。

rest 运算符与以下语法一起使用:

```
const fn = (a,b,..restOfTheArgs) => {...}
```

其中`restOfTheArgs`是一个数组，包含除前两个参数之外的参数列表。例如，如果我们想要编写一个`sum`函数，它将一个不定的数字列表作为参数，并对这些数字求和，我们可以编写:

```
const sum = (a,b,...otherNums) => {
  return a + b + otherNums.reduce((x,y)=>x+y, 0);
}
```

正如我们所看到的，这对于有参数列表的函数来说非常方便。在此之前，我们必须使用函数中可用的`arguments`对象来获取参数列表。

这并不理想，因为我们允许他们在参数中传递任何东西。有了 rest 操作符，我们就拥有了两个世界的精华。我们可以有一些固定的参数，而其余的保持灵活。

这使得函数比具有固定数量参数的函数更加灵活，同时对于采用不确定数量参数的函数也具有一定的灵活性。

`arguments`对象拥有函数的所有参数。此外，它不是一个真正的数组，所以数组函数对他们不可用。它只是一个对象，用索引和键来表示参数的位置。

像`[sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)`、`[map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)`、`[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)`或`[pop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)`这样的方法不能在`arguments`对象上运行。它还具有其他属性。这给程序员造成了困惑。

用 rest 操作符转换成数组的参数没有这些问题，因为它是一个真实的数组。

要调用我们编写的`sum`函数，我们可以写:

```
const result = sum(1,2,3,4,5,6,7,8,9,10);
```

`result`将是`55`，因为我们把所有的论点总结在一起。`otherNums`是除 1 和 2 以外的所有数字的数组。

我们还可以使用 rest 操作符将一个参数列表析构为一个变量列表。这意味着我们可以用 spread 操作符将参数列表转换成数组，然后将参数数组分解成变量列表。

这非常有用，因为我们可以获得 rest 操作符所操作的数组条目，并将它们转换为命名变量。

例如，我们可以写:

```
const sum = (a,b,...[c,d,e])=> a+b+c+d+e;
```

这样，我们可以使用 rest 操作符，但要限制函数接受的参数数量。

我们取函数参数`a`和`b`，也取`c`、`d`、`e`为参数。然而，不使用 rest 操作符可能会更清楚，因为所有的参数都是固定的，我们可以直接列出参数。

# 结论

现在我们有了 2015 版的 JavaScript，我们可以用参数做很多方便的事情。

默认值和 rest 操作符对于处理缺失的参数或操作参数非常方便。默认参数让我们为那些在函数调用参数中可能没有传递任何内容的参数设置默认值。

rest 操作符让我们以数组的形式检索参数中列出的部分或全部项目。让我们用数组操作来操纵它们，或者使用析构赋值来将它们分解成单独的变量。