# 防止对象属性被 JavaScript 的冻结功能修改

> 原文：<https://betterprogramming.pub/prevent-object-properties-from-being-modified-with-javascripts-freeze-function-dd13e5bdc920>

## 了解如何创建不能更改的对象

![](img/e056e3a1fe06c30f63a1c73b334f11a0.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com/s/photos/secure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# Object.freeze()

这将防止对象被修改。一旦我们冻结了一个对象，我们就不能:

*   删除属性
*   添加新的属性
*   更改属性的可枚举性、可配置性或可写性
*   更改现有属性的值

```
var user = {
    name : "John",
    age  : 20
}var frozenUser = Object.freeze(user);frozenUser === user ; // true
```

当我们使用一个对象的`delete`属性时，它返回`false`，对象属性不会被移除。

```
delete frozenUser.name; // falselog(frozenUser); // {name: "John", age: 20}
```

当我们试图添加一个新的属性时，它不会将该属性添加到对象中。

```
frozenUser.profession = "doctor 👨‍⚕️ ";log(frozenUser); // {name: "John", age: 20}
```

我们不能改变现有财产的价值。

```
frozenUser.age = 22; // it will not change the valuelog(frozenUser); // {name: "John", age: 20}
```

或者更改属性的可枚举性、可配置性或可写性。

```
Object.defineProperty(frozenUser, 'name', {enumerable : false});
//throws an error cannot redefine property
```

以上所有方法都会在`strict mode`中抛出一个`TypeError`异常。

我们也可以冻结一个`array`。

```
var array = [1,2,3,4,5]var frozenArray = Object.freeze(array);frozenArray[0] = 10;log(frozenArray); // [1,2,3,4,5]
```

当我们试图添加或删除一个值时，它会抛出一个`TypeError`。

object `freeze`方法只做一个浅层冻结，这意味着如果属性值是对象本身，那么这些对象不会被冻结。

```
var user = {
   name : "John",
   address : {
      doorNo : 20
   } 
}Object.freeze(user);
```

上述代码将防止添加、删除和修改属性。但是如果`property address`的值本身就是一个对象，`Object.freeze`方法就不会冻结`address`对象。我们可以改变地址对象。

```
user.address.doorNo = 100;log(user.address); // {doorNo: 100}user.address.street = "First Street";log(user.address); // {doorNo: 100, street: "First Street"}
```

# 来自 MDN 的深度冻结

要检查一个对象是否被冻结，我们可以使用`Object.isFrozen`。只有当一个对象的所有立即属性不是`Extensible`、不是`Writable`和不是`Configurable`时，该方法才返回`true`。

示例 1:

```
var user = {
   name : "John"
}Object.freeze(user);Object.isFrozen(user); // true
```

示例 2:

```
var obj = {
  name : "John"
}Object.isFrozen(obj); // falseObject.defineProperty(obj, 'name', {
   writable: false,
   configurable: false
});Object.isFrozen(obj); // false because the name is still extensibleObject.preventExtensions(obj);Object.isFrozen(obj); //true
```

# 对象。海豹

如果我们需要改变现有属性的值，那么我们需要使用`Object.seal`方法。

`Object.seal`方法阻止一个对象添加新属性，将所有现有属性标记为不可配置(不可删除)。

```
var user = {
   name : "John"
}user.age  = "20";user; // {name: "John", age: "20"}
```

密封后，

```
var sealedUser = Object.seal(user);user === sealedUser // true
```

我们可以改变现有财产的价值。

```
sealedUser.name = "JOHN";log(sealedUser); // {name: "JOHN", age: "20"}
```

但是我们不能添加新的属性。

```
sealedUser.profession = "programmmer";log(sealedUser); // {name: "JOHN", age: "20"}
```

此外，我们不能删除属性。

```
delete sealedUser.age;log(sealedUser); // {name: "JOHN", age: "20"}
```

要检查一个对象是否被密封，我们可以使用`isSealed`。一个物体被认为是密封的，如果

*   它是不可扩展的(不能添加新属性)
*   如果其所有属性都是不可配置的(不可移除的)

```
var user = {
   name : "John"
}Object.isSealed(user); // falseObject.seal(user);Object.isSealed(user); // true
```

参考: [MDN 网络文档](https://developer.mozilla.org/en-US/)