# 在 JavaScript 中访问对象的键、值和条目

> 原文：<https://betterprogramming.pub/accessing-an-objects-keys-values-and-entries-in-javascript-e7bb5d33d11c>

## 让我们深入研究对象

![](img/52e9fbc5c071ebf873de2c879227f51b.png)

马特·阿特兹在 [Unsplash](https://unsplash.com/s/photos/object?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

*   `Object.keys(obj)` →返回给定对象的属性名数组。
*   `Object.values(obj)` →返回给定对象自身属性值的数组。
*   `Object.entries(obj)` →返回给定对象自己的字符串键属性`[key, value]`对的数组。

```
var user = { name : "John", profession : "👨‍⚕️", salary  : 10000}Object.keys(user); // ["name", "profession", "salary"]Object.values(user); // ["John", "👨‍⚕️", 10000]Object.entries(user); //output
0: (2) ["name", "John"]
1: (2) [“profession”, “👨‍⚕️”]
2: (2) [“salary”, “10000]
length: 3
```

以上三种方法都只返回对象的可枚举属性。

```
var user = {
    name : "John"
}Object.defineProperty( user , 'salary', {
   value: 10000,
   enumerable: false
});
```

在上面的代码中，我们定义了一个属性`salary`，并将`enumerable`属性设置为`false`。这将使`salary`对`Object.keys`隐藏。

```
Object.keys(user); ["name"]Object.values(user); ["John"]Object.entries(user); //  ["name", "John"]
```

我们不能遍历对象的`symbol`属性。

```
var user = {
    name : "John"
} let Passcode = Symbol("Passcode");user[Passcode] = "1234";
```

现在我们已经将`symbol`设置为对象的属性，但是使用上面的三种方法将无法访问它。

```
Object.keys(user); //["name"]Object.values(user); // ["John"]Object.entries(user); //  ["name", "John"]
```

要访问对象的符号属性，我们可以使用`getOwnPropertySymbols()`。

```
Object.getOwnPropertySymbols(user); //[Symbol(Passcode)]
```

我们可以使用`for…`作为上述方法的替代方法。

```
var user = {
    name : "John",
    age  : 25
}for(const property in user) {
    console.log(`user[${property}] = ${user[property]}`);
}//outputuser[name] = John
user[age]  = 25
```

将`map`与`Object.entries`一起使用。

```
var user = {
    name : "John",
    age  : 25
}let entries = Object.entries(user);entries.map( ([prop, val]) => console.log(prop, val));
```

或者，我们也可以使用`forEach`。

```
var user = {
    name : "John",
    age  : 25
}let entries = Object.entries(user);entries.forEach( ([prop, val]) => console.log(prop, val));
```

使用`for…of`和`Object.entries`:

```
var user = {
    name : "John",
    age  : 25
}let entries = Object.entries(user);for(const [prop, val] of entries) {
    console.log(prop, val);
}
```

参考资料:

*   [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)
*   [JavaScript.info](https://javascript.info/keys-values-entries)

跟随 Javascript Jeep🚙💨

[](https://sitepoint.tapfiliate.com/p/payout-methods/new/) [## 登录|站点点

### 不支持的浏览器虽然我们的跟踪技术支持旧的浏览器，不幸的是我们的网站不支持…

sitepoint.tapfiliate.com](https://sitepoint.tapfiliate.com/p/payout-methods/new/)