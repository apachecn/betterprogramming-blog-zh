# 在 JavaScript 中使用 Spread 运算符

> 原文：<https://betterprogramming.pub/using-spread-operator-in-javascript-1b89f0c77ab3>

## spread 语法允许我们将一组对象(如数组)分解成单独的参数

![](img/8fee8d7aa4e634d56853b7e3b6f5c950.png)

由[鞠波·史密斯](https://unsplash.com/@jessysmith?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

spread 语法允许我们将一组对象(如数组)分解成单独的参数，或者将它们插入到不同的可迭代对象(如数组)中。

有了 2018 版本的 JavaScript，我们还可以将一个对象的属性传播到另一个对象中，将键和值传播到另一个对象中。扩展语法由对象前的三个句点表示。

例如，我们可以写:

```
...arr
```

spread 语法的工作方式是复制原始数组的值，然后将它们插入到另一个数组中，或者按照它们在数组中出现的顺序将它们作为函数中的参数列表以相同的顺序放置。

当 spread 运算符用于对象时，键值对的出现顺序与它们在原始对象中的出现顺序相同。

我们可以使用 spread 语法将一组值作为函数的参数展开。例如，我们可以写:

```
const arr = [1,2,3];
const add = (a,b,c) => a+b+c;
add(...arr) // returns 6
```

在上面的示例中，spread 运算符将变量按照它们在数组中出现的顺序展开到参数中。所以 1 传入`a`，2 传入`b`，3 传入`c`。

# 分散阵列

对于数组，我们也可以使用 spread 语法将一个数组的值插入另一个数组。例如，我们可以写:

```
const arr = [1,2,3];
const arr2 = ['a','b','c',...arr,'d']; // arr2 is ['a','b','c',1,2,3,'d']
```

正如我们所看到的，spread 操作符将值准确地插入到我们展开数组的位置，与它们在数组中出现的顺序相同。

所以，1 插在`a`和`d`之间，然后 2 插在 1 和`d`之间，3 插在 2 和`d`之间。结果是，我们用 spread 操作符将一个数组的值复制到另一个数组中，复制的顺序与它们出现的顺序相同，并且正好是放置数组 spread 表达式的位置。

没有 spread 操作符，我们必须编写循环来将它们插入到我们想要的位置。我们`slice`将数组分成两部分，然后对这三部分调用`concat`，然后将结果赋给你插入内容的数组。光是想想就听起来很痛苦。

注意，使用 spread 操作符，只有数组的第一级被展开。如果我们有嵌套或多维数组，它将原样复制引用。它不会对嵌套的项目做任何事情。

借助 ES2018，我们可以对对象做同样的事情，如下所示:

```
const obj = {a: 1, b: 2};
let objClone = { ...obj }; // objClone is {a: 1, b: 2}
```

这将创建对象的浅表副本。这意味着只复制对象的第一级。

对于嵌套对象，它将原样复制引用。它不会对嵌套的项目做任何事情。对象的顶层键和值将被复制到`objClone`。

因此，如果我们有嵌套的对象，我们得到:

```
const obj = {
  a: 1,
  b: {
    c: 2
  }
};
let objClone = {
  ...obj
};
console.log(objClone) 
```

在`objClone`中，我们得到:

```
{
  a: 1,
  b: {
    c: 2
  }
}
```

因此，嵌套对象将引用与原始对象相同的对象。

spread 运算符可用作以前存在的其他函数的替代函数。

例如，我们可以用它来代替`apply`函数来传递函数的参数。`apply`函数将它所调用的函数的参数数组作为第二个参数。

对于`apply`函数，我们调用如下:

```
const arr = [1,2,3]
const sum = (a,b,c)=> a+b+c;
sum.apply(null, arr); // 6
```

使用 spread 语法，我们可以改为编写以下代码:

```
const arr = [1,2,3]
const sum = (a,b,c)=> a+b+c;
sum(...arr)
```

spread 运算符也处理字符串。我们将 spread 操作符应用于字符串，得到一个包含字符串中各个字符的数组。

例如，如果我们写:

```
const str = 'abcd';
const chars = [...str];
```

我们得到`[“a”, “b”, “c”, “d”]`作为`chars`的值。

# 多次使用扩展运算符

我们可以在一个地方多次使用 spread 语法。例如，我们可以有以下内容:

```
const arr = [1,2,3];
const arr2 = [4,5];
const sum = (a,b,c,d,e,f)=> a+b+c+d+e+f;
sum(...arr, ...arr2, 6)
```

像往常一样，spread 语法将数字数组按照它们在中出现的顺序展开到数组的参数中。

所以，`sum(…arr, …arr2, 6)`和`sum(1,2,3,4,5,6)`一样。

1、2、3 是前三个自变量，是`arr`的条目，顺序相同，4、5 是第四、第五个自变量，分散在 1、2、3 之后。

最后，我们把 6 作为最后一个参数。我们还可以看到 spread 语法与普通的函数调用语法一起工作。

# 在构造函数中使用它

我们可以使用 spread 操作符作为对象构造函数的参数。例如，如果我们想创建一个新的`Date`对象，我们可以写:

```
let dateFields = [2001, 0, 1];
let date = new Date(...dateFields);
```

`dateFields`数组中的项目按照它们出现的顺序作为参数传递给构造函数。另一种写法可能会更长，比如:

```
let dateFields = [2001, 0, 1];
const year = dateFields[0];
const month = dateFields[1];
const day = dateFields[2];
let date = new Date(year, month, day);
```

# 复制项目

spread 语法也可用于创建数组或对象的浅层副本，方法是创建数组或对象的键值对的顶级元素的副本，然后将它们插入到使用 spread 运算符的位置。

对于复制数组，我们可以写:

```
const arr = [1, 2, 3];
const arr2 = [...arr, 4, 5];
```

上面的例子，`arr2`是`[1,2,3,4,5]`，而`arr1`还是`[1,2,3]`。

`arr1`没有被`arr2`引用，因为 spread 操作符实际上复制了数组，然后插入了值。请注意，这不适用于多维数组，因为它只复制顶级元素。

我们可以在一个数组或对象中多次应用 spread 语法。数组的一个例子是:

```
let arr = [1, 2, 3];
let arr2 = [4, 5];
let arr3 = [...arr2, ...arr];
```

在上面的例子中，我们得到了`[4,5,1,2,3]`。`arr1`和`arr2`不受影响，因为来自`arr1`和`arr2`的值的副本被插入`arr3`。

# 展开运算符和对象

在 ES2018 中，spread 运算符处理对象文字。然后，可以用 spread 操作符将一个对象的键值对插入到另一个对象中。

如果在同一个对象中有两个对象具有相同的关键点，并且 spread 运算符应用于这两个对象，那么后面插入的对象将覆盖前面插入的对象。

例如，如果我们有以下内容:

```
let obj1 = {foo: 'bar', a: 1};
let obj2 = {foo: 'baz', b: 1};
let obj3 = {...obj1, ...obj2 }
```

然后我们得到`{foo: “baz”, a: 1, b: 1}`作为`obj3`的值，因为`obj1`在`obj2`之前展开。

它们都有`foo`作为对象中的键。首先，`foo: 'bar'`被展开操作符插入到`obj3`。然后，`foo: 'baz'`在`obj2`被合并后覆盖`foo`的值，因为它有相同的键`foo`但后来被插入。

这对于合并对象非常有用，因为我们不必遍历键并输入值，这比一行代码要多得多。

需要注意的一点是，我们不能在常规对象和可迭代对象之间混合 spread 运算符。例如，如果我们写下以下内容，我们将得到`TypeError`:

```
let obj = {foo: 'bar'};
let array = [...obj];
```

# 结论

正如我们所见，spread 语法是 JavaScript 的一个非常方便的特性。它让我们把不同的数组组合成一个。

此外，它让我们只用一行代码就可以将数组作为参数传递给函数。借助 ES2018，我们还可以使用相同的运算符将键值对扩展到其他对象中，从而将一个对象的键值对填充到另一个对象中。

spread 操作符的工作方式是复制顶级项，并在使用 spread 操作符的地方填充它们，因此我们也可以使用它来制作数组和对象的浅层副本。