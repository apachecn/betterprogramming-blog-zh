# 如何用 JavaScript 从数组创建字典

> 原文：<https://betterprogramming.pub/how-to-create-a-dictionary-from-an-array-in-javascript-c78f37c80bb1>

## 有时候，拥有一个包含所有元素的对象可以简化代码的复杂性！

![](img/d4d2d79edb2602ff759d311d58c611a6.png)

*照片由*[*snap wire*](https://www.pexels.com/@snapwire/)*发自* [*像素*](https://www.pexels.com/photo/black-and-white-book-browse-dictionary-6997/) *。*

通常，我以一个对象列表结束，并希望使用一个惟一的标识符在列表中找到一些项目。一个例子:增强与我的列表上的项目相关的其他数据。

有许多方法可以做到这一点，但有些方法比其他方法更好，尤其是在有大量数据的情况下。

让我们看一个简单的例子:

加拿大省和地区的简略列表。对于这个例子，省份只有两个属性，但在现实生活中更多。

如果我们想检索由 id `10`标识的省名，我们该怎么做呢？

```
const province10 = `?`;
```

让我们看看我们可以使用的一些解决方案！

# 解决方案#0 —使用查找

一个简单的解决方案是使用`find`函数检索满足我们搜索的第一个元素:

```
const province10 = data.find(element => element.id === 10);
```

这种方法非常有效，但是如果你需要查找很多项目和/或多次，可能会很慢。让我们看看我们是否能做得更好。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) [## array . prototype . find()-JavaScript | MDN

### find 方法对数组的每个索引执行一次 callbackFn 函数，直到 callbackFn 返回一个 true thy…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find) 

# 解决方案 1 —使用 reduce

如果我们手中有以下数据会怎么样:

```
{
  3: {id: 3, name: 'New Brunswick'},
  8: {id: 8, name: 'Ontario'},
  10: {id: 10, name: 'Quebec'},
}
```

有人称这种结构为[字典](https://www.w3schools.com/python/python_dictionaries.asp)或者索引。如果我们有了它，我们可以使用它的标识符:`dictionary[10]`直接访问我们想要的元素。那不是更容易吗？

让我们看看如何使用我最喜欢的一个数组函数来创建它:`reduce`！

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) [## array . prototype . reduce()-JavaScript | MDN

### reduce()方法在数组的每个元素上执行用户提供的“reducer”回调函数，以便…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 

```
const dictionary = data.reduce((dictionary, province) => {
  dictionary[province.id] = province;
  return dictionary;
}, {});const province10 = dictionary[10];
// {id: 10, name: 'Quebec'}
```

这种结构的最大优点是只需要在数组上迭代一次(构建字典)。如果你有很多数据，你应该能更快地得到你想要的元素。

但是，如果你对[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)不感兴趣，我有另一个你可以使用的解决方案。

# 解决方案 2——使用对象析构

在写这篇文章的时候，我发现了另一个优雅的解决方案，我喜欢它，但不会在好的代码库中使用，因为它更复杂。

```
const dictionary = Object.assign({}, ...data.map((x) => ({[x.id]: x})));
```

你很容易理解这个吗？如果没有，我来帮你。

```
const dictionary = Object.assign({}, ...[
    {8: {id:8,name:"Ontario"}},
    {10: {id:10,name:"Quebec"}},
])
```

这个解决方案使用析构来创建一个包含许多较小对象的字典。所有生成的对象被合并成一个更简单的对象。

更复杂，对吧？

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) [## 析构赋值- JavaScript | MDN

### 基本赋值给新变量名一个属性可以从一个对象中解包并赋给一个变量…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 

# 结论

我希望这篇文章向你介绍了 JavaScript 字典的概念，并且在将来对你有所帮助！🤗

*原载于*[*www . Benjamin rancourt . ca*](https://www.benjaminrancourt.ca/how-to-easily-create-a-json-file-in-bash/)*。*