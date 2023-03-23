# 在 JavaScript 中使用析构可以做的 8 件事

> 原文：<https://betterprogramming.pub/8-things-you-can-do-with-destructuring-in-javascript-3da9975fcec1>

## 探索析构赋值语法

![](img/63c76e9ec347bbdbd0fd62fd88bfe981.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上用 [Veit Hammer](https://unsplash.com/@derveit?utm_source=medium&utm_medium=referral) 拍照

在这篇文章中，我将解释 JavaScript 中的析构赋值是什么。但是首先，让我们看看什么是对象属性值速记。

# 对象属性值速记

在 ES6 中，如果有一个对象，它的键与作为属性传入的变量同名，那么可以使用对象属性值简写来简化代码。

```
const data = { item: '123' };
const dummy = { data: data };
```

我们可以这样简化上面的代码:

```
const data = { item: '123' };
const dummy = { data };
```

这是一个让我们的代码更干净的小特性！

# 析构赋值语法

析构赋值语法是一个将数组和对象属性中的值解包到变量中的表达式。听起来很奇怪，其实不然！本质上，我们将一个结构(destructure)分解成单个的元素，例如，一个数组到数组项。

## 从数组中获取数据

假设我们有一个包含两个元素的数组:

```
const array = [1, 2];
```

我们希望第一个元素的值存储在名为“a”的变量中，第二个元素的值存储在名为“b”的变量中

```
const a = array[0];
const b = array[1];
```

通过析构，我们可以这样做:

```
const [a, b] = array;
```

在析构中，表达式的左边部分称为**目标**，右边部分称为**源**。

请记住，要使析构起作用，目标和源必须具有相同的数据结构。

通过析构，我们可以很容易地跳过数组中的项目。代码如下:

```
const array = [1, 2, 3];
const [a, , b] = array;
```

现在 **a** 的值为 1，而 **b** 的值为 3。我们可以添加任意多的逗号，但是在一行中的两个逗号之后，就不可读了。

假设我们有一个包含九项的数组:

```
const array = [1, 2, 3, 4, 5, 6, 7, 8, 9];
```

我们希望将第一个元素的值存储到名为“a”的变量中，将第二个元素的值存储到名为“b”的变量中，并将数组的其余部分存储到名为“c”的变量中。

借助重置参数，我们可以轻松做到这一点。如果你不知道什么是复位参数，不要着急，读读[这个](/exploring-the-spread-operator-and-rest-parameter-in-javascript-8f425b4031e2)。

```
const [a, b, ...c] = array;
console.log(a); // 1
console.log(b); // 2
console.log(c); // [3, 4, 5, 6, 7, 8, 9]
```

## 交换变量值

假设我们有两个变量，我们想交换它们的值。代码如下:

```
let a = 1;
let b = 2;
const temp = a;
let a = b;
let b = temp;
```

有了析构，我们不用 temp 变量也能轻松做到:

```
let a = 1;
let b = 2;
let [b, a] = [a, b];
```

我们在源和目标中创建了相同的数据结构(数组)。这是一个简单的交换技巧。

## 毁灭函数返回值(数组)

让我们假设我们有一个返回两个项目的数组的函数:

```
const data = () => [1, 2];
```

我们希望将第一个元素的值存储到名为“a”的变量中，将第二个元素的值存储到名为“b”的变量中。

```
const value = data();
const a = value[0];
const b = value[1];
```

通过析构，我们可以简化上述操作并删除 value 变量:

```
const [a, b] = data();
```

## 添加默认值

假设我们有一个数组，它有时有两个值，有时只有一个值。

```
const array = [1];
```

我们希望将第一个元素的值存储到名为“a”的变量中，将第二个元素的值存储到名为“b”的变量中。同样，当数组只有一个值时，变量“b”的值为“1”。

```
const a = array[0];
const b = array[1] || 2;
```

旧的方法是使用逻辑运算符。如果你不知道什么是逻辑运算符，不要着急，读读[这个](/exploring-the-spread-operator-and-rest-parameter-in-javascript-8f425b4031e2)。

通过析构，我们可以这样做:

```
const [a, b=2] = array;
```

请记住，这仅在解包后的值为`undefined`或根本不存在的情况下有效。因此，如果值是假的，例如 null、false 等，它将不会使用默认值。

## 从对象获取数据

让我们假设我们有对象:

```
const user = { name: 'George', email: 'george.roubie@gmail.com' };
```

我们需要两个变量“name”和“email”以及用户对象的值。

```
const name = user.name;
const email = user.email;
```

通过析构，我们可以这样做:

```
const { name: name, email: email } = user;
```

上面的工作非常完美，因为源和目标具有相同的数据结构。但是我们可以使用对象属性值简写来以一种更简单的方式来写它。

```
const { name, email } = user;
```

当我们希望变量具有不同于属性的名称时，扩展方法很有帮助，例如，“用户名”、“用户电子邮件”:

```
const { name: userName, email: userEmail } = user;
```

## 用 rest 参数从对象获取数据

让我们假设你有这个对象:

```
const user = { 
  name: 'George',
  role: 'SA',
  killingZombies: true, 
  dancing: true
};
```

我们需要一个包含用户名的变量“name ”,一个包含用户角色的变量“role ”,以及一个包含所有用户爱好的对象的变量“favorites”。

使用析构和 rest 参数，我们可以这样做:

```
const { name, role, ...hobbies } = user;
```

## 破坏函数返回值(对象)

假设我们有一个返回对象的函数:

```
const data = () => ({ name: 'George', role: 'SA' });
```

我们需要一个包含用户名的变量“name”和一个包含用户角色的变量“role”。

```
const value = data();
const name = value.name;
const role = value.role;
```

通过析构，我们可以简化上述操作并删除 value 变量:

```
const { name, role } = data();
```

## 破坏功能参数

让我们假设我们有这个函数:

```
function (currentUser) {
  const name = currentUser.name;
  const email = currentUser.email;
  const roles = currentUser.roles;
  console.log('Name', name);
  console.log('Email', email);
  console.log('Roles', roles);
}
```

我们可以通过析构函数参数来优化以上内容:

```
function ({ name, email, roles }) {
  console.log('Name', name);
  console.log('Email', email);
  console.log('Roles', roles);
}
```

我们在参数部分创建了所有变量，现在代码可读性更好了。

如你所见，JavaScript 中的析构赋值语法非常强大，可以优化/简化我们的代码。