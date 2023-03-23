# ES2016 和 2017 中有用的新功能

> 原文：<https://betterprogramming.pub/useful-new-features-in-es2016-and-2017-3df7b29cc503>

## 我们看看新的字符串和对象方法以及异步和等待

![](img/b4c188acefa6f24865cbe1f9ce70b59c.png)

[Adri Tormo](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ES2015 引入了许多功能，如箭头函数、对象创建和继承的类语法、`let`和`const`以及许多其他新功能。在接下来的几年里，该语言和标准库中将会引入更多的优秀特性。ES2016 是一个具有新功能的小版本，例如将`includes`函数引入数组和指数运算符。ES2017 引入了更多功能，如`Object.values`和`Object.entries`，以及字符串功能，如`padStart`和`padEnd`，以及`async`和`await`。这些便利的特性给 JavaScript 开发人员带来了更多的便利，使得 JavaScript 应用程序的开发更加容易。

# ES2016 特性

## 数组.包含

`Array.includes`检查数组中是否存在项目。它接受一个数字或字符串，函数可以将它作为第一个参数进行比较，并将要搜索的索引作为第二个参数。第二个参数可以是正的，也可以是负的。

```
const array = [1,2,3];
const includesTwo = array.includes(2); // returns true
```

我们也可以从负索引开始搜索数组，然后从计算的索引开始函数搜索，计算的索引是数组的长度加上第二个参数。所以如果我们写:

```
array.includes(2, -2);
```

然后`includes`函数将从索引`3 +(-2).`开始搜索，这意味着`array.includes(2, -2);`将返回`true`。如果传入的绝对数量大于长度，那么就搜索整个数组。

`includes`功能也被添加到 TypedArray 类型中，如`[Int8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Int8Array)`或`[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)`。

## 指数算子

指数运算符是 ES2016 的另一项新功能。指数运算符用`**`表示。这是计算指数值的新语法，是`Math.pow`函数的替代。例如，要计算 2 的 3 次方，我们可以写:

```
2**3
```

也就是 8。注意，在 JavaScript 中，当我们将指数运算符和一元运算符如`-`组合在一起时，不可能编写出模糊的表达式，所以像`-2**2`这样的东西在 JavaScript 中是无效的。然而，`-(2**2)`是有效的，因为它是明确的，因为我们知道值是-4，因为指数在括号内，负号在外面。

我们可以通过编写以下内容来强制幂运算表达式的基数为负数:

```
(-2)**2
```

所以这意味着`base`必须在括号中。

# ES2017 功能

## 对象.值

`Object.values`让我们获得一个数组，数组中有一个对象的值。除了`Object.values`不返回原型链中的值之外，这些值的返回顺序与`for...in`循环提供的顺序相同。

例如，我们可以写:

```
const obj = { a: 1, b: 2 };
console.log(Object.values(obj)); // [1, 2]

const obj = { 0: 'd', 1: 'e', 2: 'f' };
console.log(Object.values(obj)); // ['d', 'e', 'f']

const obj2 = { 100: 'd', 2: 'e', 7: 'f' };
console.log(Object.values(obj2)); // ['e', 'f', 'd']

console.log(Object.values('abc')); // ['a', 'b', 'c']
```

正如我们所见，`Object.values`处理对象和字符串。如果键是数字，那么它们以升序返回，就像上面的`obj2`例子一样。对于字符串，它返回字符串的单个字符的数组。

## **Object.entries()**

`Object.entries`函数返回一个数组，每个条目都是各自数组中的键值对。条目的返回顺序与`for...in`循环提供的顺序相同，只是`Object.values`不返回原型链中的值。与任何对象一样，我们可以使用`sort`函数对数组进行排序，以获得我们想要的条目顺序。

例如，我们可以写:

```
const obj = { foo: 1, bar: 2 };
console.log(Object.entries(obj)); // [ ['foo', 1], ['bar', 2] ]

const obj = { 0: 'x', 1: 'y', 2: 'z' };
console.log(Object.entries(obj)); // [ ['0', 'x'], ['1', 'y'], ['2', 'z'] ]

const obj2 = { 100: 'x', 2: 'y', 7: 'z' };
console.log(Object.entries(obj2)); // [ ['2', 'x'], ['7', 'y'], ['100', 'z'] ]

console.log(Object.entries('abc')); // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]

console.log(Object.entries(100)); // [ ]

const obj = { a: 1, b: 2, c: 3};
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key} ${value}`); // "a 1", "b 2", "c 3"
}

Object.entries(obj).forEach(([key, value]) => {
  console.log(`${key} ${value}`); // "a 1", "b 2", "c 3"
});
```

在上面的例子中，我们可以看到`Object.entries`返回一个键-值对的数组，每个条目作为一个数组，键是第一个元素，值是第二个元素。如果键是整数，那么它们按数字升序排序。所以`Object.entries(obj2)`返回`[ ['2', 'x'], ['7', 'y'], ['100', 'z'] ]`。它还可以处理字符串，以字符的索引作为键返回单个字符，因此键是字符串的索引，它是每个条目的第一个元素，单个字符是字符串的值，它作为第二个字符返回。对于没有属性的对象，比如数字和布尔值，`Object.entries`返回一个空数组。

`Object.entries`返回的数组可以转换成`Map`对象。它可以像下面的例子一样使用:

```
const obj = { a: 1, b: 2 }; 
const map = new Map(Object.entries(obj));
console.log(map); // Map { a: 1, b: 2 }
```

## String.padStart()

`padStart()`函数将一个字符串添加到一个字符串之前，直到它达到您指定的长度。该函数有两个参数。第一个是字符串的目标长度。如果目标长度小于字符串的长度，则字符串按原样返回。第二个参数是要添加填充的字符串。这是一个可选参数，如果没有指定，它默认为`' '`。

例如，我们可以这样写:

```
'def'.padStart(10);         // "       def"
'def'.padStart(10, "123");  // "1231231def"
'def'.padStart(6,"123465"); // "abcdef"
'def'.padStart(8, "0");     // "00000def"
'def'.padStart(1);          // "def"
```

注意，每个字符串都用第二个参数中的字符串填充到目标长度。第二个参数中的整个字符串可能并不总是包含在内。只包括让函数将字符串填充到目标长度的部分。

## String.padEnd()

`padEnd()`函数在一个字符串之后添加一个字符串，直到它达到您指定的长度。该函数有两个参数。第一个是字符串的目标长度。如果目标长度小于字符串的长度，则字符串按原样返回。第二个参数是要添加填充的字符串。这是一个可选参数，如果没有指定，默认为`' '`。

例如，我们可以这样写:

```
'def'.padEnd(10);         // "def       "
'def'.padEnd(10, "123");  // "def1231231"
'def'.padEnd(6,"123465"); // "defabc"
'def'.padEnd(8, "0");     // "def00000"
'def'.padEnd(1);          // "def"
```

## **object . getownpropertydescriptors()**

函数返回一个对象的所有属性描述符。

例如，我们可以像下面的代码一样使用它:

```
const obj = {
  a: 1
};const descriptors = Object.getOwnPropertyDescriptors(obj);
```

`descriptors`对象应该具有:

```
{
  a: {
    configurable: true,
    enumerable: true,
    value: 1,
    writable: true
  }
}
```

## 异步和等待

通过`async`和`await`，我们可以缩短承诺代码。在`async`和`await`之前，我们必须使用`then`函数，我们把回调函数作为所有`then`函数的参数。这使得代码很长，因为我们有很多承诺。相反，我们可以使用`async`和`await`语法来替换`then`及其相关的回调，如下所示。例如，我们有使用 Fetch API 向后端发出请求的代码:

```
const APIURL = "[http://localhost:3000](http://localhost:3000)";const subscribe = async data => {
  const response = await fetch(`${APIURL}/subscribers`, {
    method: "POST",
    mode: "cors",
    cache: "no-cache",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });
  return response.json();
};window.onload = () => {
  nameForm.method = "post";
  nameForm.target = "_blank";
  nameForm.action = "";
  nameForm.addEventListener("submit", async e => {
    e.preventDefault();
    const firstName = document.getElementById("firstName").value;
    const lastName = document.getElementById("lastName").value;
    const email = document.getElementById("email").value;
    let errors = [];
    if (!firstName) {
      errors.push("First name is required.");
    } if (!lastName) {
      errors.push("Last name is required.");
    } if (!email) {
      errors.push("Email is required.");
    } if (!/[^@]+@[^\.]+\..+/.test(email)) {
      errors.push("Email is invalid.");
    } if (errors.length > 0) {
      alert(errors.join(" "));
      return;
    }
    try {
      const response = await subscribe({
        firstName,
        lastName,
        email
      });
      alert(`${response.firstName} ${response.lastName} has subscribed`);
    } catch (error) {
      alert(error.toString());
    }
  });
};
```

我们用`await`替换了`then`和回调。然后，我们可以将每个承诺的解析值指定为变量。注意，如果我们使用`await`作为我们的承诺代码，那么我们必须把`async`放在函数签名中，就像我们在上面的例子中所做的那样。为了捕捉错误，我们没有在最后链接`catch`函数，而是使用`catch`子句来完成。此外，我们没有在底部链接`finally`函数来在承诺结束时运行代码，而是在`catch`子句后使用`finally`子句来做这件事。

在上面的代码中，我们获得了分配给变量的 promise 的解析值，而不是在`then`函数的回调中获得值，就像上面的`const response = await subscribe({...})`行一样。

函数总是返回承诺，不能像任何使用承诺的函数那样返回任何其他东西。在上面的例子中，我们使用了基于承诺的获取 API 和`async`和`await`语法，我们表明我们可以用比使用回调作为参数传入的`then`函数更短的方式链接承诺。

ES2016 和 2017 为我们提供了方便的功能，增强了 ES2015 中添加的已经很棒的语法糖和功能，这是对其前身的重大改进。这两个版本的 JavaScript 通过新的对象、字符串和数组方法以及链接承诺的简写，使得 JavaScript 开发更加出色。