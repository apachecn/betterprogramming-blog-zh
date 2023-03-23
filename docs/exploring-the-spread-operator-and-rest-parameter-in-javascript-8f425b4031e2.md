# 探究 JavaScript 中的 Spread 运算符和 Rest 参数

> 原文：<https://betterprogramming.pub/exploring-the-spread-operator-and-rest-parameter-in-javascript-8f425b4031e2>

## spread 操作符有许多用例。让我们来看看其中的几个

![](img/9aca882682f6618e228ad6120b2364b5.png)

照片由[在](https://unsplash.com/@drew_saurus?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上画出 Saurus 。

在本文中，我将解释 JavaScript 中的*扩展操作符*和*剩余参数*是什么。但是首先，让我们看看 JavaScript 中的逻辑运算符是什么。

# 逻辑运算符

`&&`和`||` 称为逻辑运算符。JavaScript 中的逻辑运算符不像其他编程语言那样只返回布尔值。JavaScript 中的逻辑运算符返回值。这就是为什么许多开发人员称它们为选择操作符。

规则:

*   真理`&&`无论如何
*   falsy `&&`随便= falsy
*   真实`||`无论如何=真实
*   无论如何

但是什么是真实和虚假呢？如果您将一个值作为参数传递给`Boolean`构造函数，并且它返回`true`，则该值为 the。如果返回`false`，则为 falsy。
更简单的规则是，值`false`、`null`、`undefined`、`0`、`-0`、`‘’`、`NaN`、`0n`、`-0n`为假，其他都为真。

```
const user = null;
const currentUser = user || {};
```

上面的例子是一个添加默认值的简单方法。因为`user`是`null`(falsy)`currentUser`将是`{}`。

# 传播算子

展开运算符是类似于`+`、`-`、`/`等的运算符。这需要一个 iterable(我们可以循环遍历的东西)并将其扩展为单个元素。语法是三个点(`...`)，有很多用例。

## 浅拷贝一个对象

对象是通过引用保存的非基元类型。如果一个对象等于另一个对象，当您更改第一个对象时，第二个对象也会自动更改。

```
const user1 = { name: 'George', surname: 'ruby' };
const user2 = user1;
user2.surname = 'Roubie';
```

在上面的例子中，`user1`中的姓也被改变了。我们可以使用 spread 运算符来修复它。

```
const user1 = { name: 'George', surname: 'ruby' };
const user2 = { ...user1 };
user2.surname = 'Roubie';
```

我们可以这样优化上面的代码:

```
const user1 = { name: 'George', surname: 'ruby' };
const user2 = { ...user1, surname: 'Roubie' };
```

这段代码之所以有效，是因为如果同一个键在一个对象中出现了两次，那么最后一次出现的键会胜出。这个特性在状态管理技术中非常有用。

浅复制意味着如果复制的对象中有嵌套对象，引用将会保留。

```
const roles = { isAdmin: true };
const user1 = { name: 'George', surname: 'Roubie', role: roles };
const user2 = { ...user1 };
user2.role.isAdmin = false;
```

上例中的角色在`user1`中也有变化，所以要小心。

## 组合对象

使用 spread 操作符，我们可以将任意多的对象组合成一个新的对象。

```
const userInfo = { name: 'George', surname: 'Roubie' };
const userRoles = { roles: ['admin', 'it'] };
const token = { accessToken: '123', refreshToken: '456' };
const currentUser = { ...userInfo, ...userRoles, ...token };
```

## 有条件地向对象添加属性

在逻辑运算符`&&`和扩展运算符的帮助下，你可以很容易地给一个对象添加属性。

```
const getRole = () => 'admin';
const user = { 
  name: 'George', 
  surname: 'Roubie',
  ...(getRole() === 'admin' && { admin: true })
};
```

因为`getRole() === 'admin'`返回`true`，`&&`返回`{ admin: true }`。这就是为什么在用户对象中添加`admin: true`的原因。

```
const getRole = () => 'dev';
const user = {
  name: 'George',
  surname: 'Roubie',
  ...(getRole() === 'admin' && { admin: true })
};
```

现在`getRole() === 'admin'`返回`false`**`&&`返回`false`。但是如果展开`false`(例如`{ ...(false) }`)，展开操作符将返回 nothing ( `{}`)。这就是为什么用户对象中没有添加任何内容。**

## **复制数组(浅层)**

**数组是像对象一样的非基本类型。如果一个数组等于另一个数组，当您更改第一个数组时，第二个数组也会自动更改。**

```
const a = [1, 2, 3];
const b = [...a];
b.push(4);
```

**上面的代码也可以用 slice 方法实现。**

```
const a = [1, 2, 3];
const b = [...a, 4];
```

**但是使用 slice 方法，当我们复制一个数组时，我们不能添加一个值，就像我们在上面的例子中做的那样。**

**这个副本是浅层的，所以如果数组中有对象或数组，引用将会保留。**

```
const data = { test: 1 };
const arr1 = [1, 2, 3, data];
const arr2 = [...arr1];
arr2[3].test = 3;
```

**在这个例子中，`arr1`和`arr2`将在数组的第三个索引中具有值`{ test: 3 }`，因为副本是浅的。**

## **串联数组**

**使用 spread 操作符，我们可以将任意多的数组连接成一个新数组。**

```
const a = [1, 2, 3];
const b = [4, 5, 6];
const c = [7, 8, 9];
const d = [...a, ...b, ...c];
```

**如果我们想连接两个数组，我们也可以使用`concat`方法，但是`concat`方法只能连接两个数组。使用 spread 操作符，我们可以连接任意多的数组，还可以在数组之间添加值，这是用`concat`方法做不到的。**

```
const a = [1, 2, 3];
const b = [5, 6, 7];
const c = [9, 10, 11];
const d = [0, ...a, 4, ...b, 8, ...c, 12];
```

## **使用数组作为参数**

**因为 spread 操作符将数组扩展为单个元素，所以我们可以将数组作为函数中的参数传递。首先，让我们看看在没有 spread 操作符的情况下如何实现它。**

```
const userInfo = ['George', 'Roubie'];
const getUser = function(name, surname) {
  console.log(name, surname);
};
getUser(userInfo[0], userInfo[1]);
```

**使用 spread 操作符，我们可以很容易地做到这一点:**

```
const userInfo = ['George', 'Roubie'];
const getUser = function(name, surname) {
  console.log(name, surname);
};
getUser(...userInfo);
```

**当有多个参数时，这非常有用。**

# **休息参数**

**语法与 spread 运算符相同，但它不是运算符。事实上，它与 spread 操作符正好相反，将剩余的元素组合成一个元素。**

```
const user = function (name, age, ...hobbies) {
  console.log(name);
  console.log(age);
  console.log(hobbies);
}
user('George', 30, 'coding', 'killing zombies');
```

**`hobbies` 参数现在是一个值为`['coding', 'killing zombies']`的数组。**

```
var user = function (name, age, ...hobbies) {
  console.log(name);
  console.log(age);
  console.log(hobbies);
}
user('George', 30);
```

**当没有值时，rest 参数使`hobbies`成为一个空数组。这是非常有帮助的，因为当我们没有增加额外的价值时，我们不必担心。**