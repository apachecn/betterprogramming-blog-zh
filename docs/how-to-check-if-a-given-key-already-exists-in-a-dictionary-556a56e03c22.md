# 如何检查给定的键是否已经存在于字典中

> 原文：<https://betterprogramming.pub/how-to-check-if-a-given-key-already-exists-in-a-dictionary-556a56e03c22>

## 两种不同的检查方式

![](img/7cadbd7519beea63892013aecd1521cc.png)

照片由[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/dictionary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

字典是可变的——它有一组无序的键和值。知道如何检查一个键是否存在是至关重要的，无论是在赋值之前，以避免覆盖现有数据，还是在访问值时，以确保该键存在。

使用`in`关键字或`.get()`方法检查字典中给定的键。

# “in”关键字

在 Python 中，`in`关键字在 iterable 中查询指定的值。如果 iterable 是一个列表，将会查询值。例如:

```
person = {
  "first_name": "Sarah",
  "last_name": "Kerrigan",
  "nickname": "Queen of Blades"
}print("nickname" in person.keys()) # True
print("age" in person.keys()) # False
```

请注意，`.keys()`方法用于从字典中以字符串列表的形式返回键。我们可以加速这项技术，因为`in`也可以直接用于字典。查询字典时，搜索的是键，而不是值。

```
person = {
  "first_name": "Sarah",
  "last_name": "Kerrigan",
  "nickname": "Queen of Blades"
}print("nickname" in person) # True
print("age" in person) # False
```

# 的。get()方法

引用字典中不存在的键会导致`KeyError`异常，可能会破坏您的代码。

```
person = {
  "first_name": "Sarah",
  "last_name": "Kerrigan",
  "nickname": "Queen of Blades"
}print(person['age'])"""
Traceback (most recent call last):
  File "medium.py", line 7, in <module>
    print(person['age'])
KeyError: 'age'
"""
```

为了避免这种情况，您可以使用上面的方法首先检查一个键。然而，这可能会变得非常重复。

```
person = {
  "first_name": "Sarah",
  "last_name": "Kerrigan",
  "nickname": "Queen of Blades"
}if "age" in person:
  print(person['age'])if "first_name" and "last_name" in person:
  print(first_name + " " + last_name)
```

或者，使用`.get()`,如果密钥不存在，它不会导致异常。该方法需要单个字符串(将被检索的键)，并接受可选的第二个参数，该参数将在找不到键时用作默认值。当未指定默认值时，如果找不到关键字，则`.get()`将返回`None`。

```
person = {
  "first_name": "Sarah",
  "last_name": "Kerrigan",
  "nickname": "Queen of Blades"
}middle_name = person.get("middle_name","") # empty string
print(person.get("age")) # None
```