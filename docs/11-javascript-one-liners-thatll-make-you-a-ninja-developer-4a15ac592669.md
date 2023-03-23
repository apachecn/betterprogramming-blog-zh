# 让你成为忍者开发者的 11 个 JavaScript 一行程序

> 原文：<https://betterprogramming.pub/11-javascript-one-liners-thatll-make-you-a-ninja-developer-4a15ac592669>

## 在六分钟内提升你的 JavaScript 知识

![](img/15dd7f6cb48c05dfe501ac1a0ada0001.png)

来自 [Pexels](https://www.pexels.com/photo/light-fashion-man-people-7792259/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [cottonbro](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 摄影

> 比尔·盖茨说:“我选择一个懒惰的人去做一件艰难的工作。因为一个懒惰的人会找到一个简单的方法去做。”

作为一名开发人员，我们通常会尽量避免写很长的代码。我的意思是，没有人愿意写 500 行代码，而不是用 50 行代码写同样的功能。

成为一名忍者开发者也意味着找到一种简单的方法来完成一项任务——写五十行代码，而不是写五百行代码。忍者开发者也喜欢使用一行程序。他们有一套将长行代码转换成单行代码的技能。

学会这 11 个 JavaScript 一行程序后，最有可能的是，比尔·盖茨可能想雇佣你做开发人员。

# 1.找出最短的长度

使用这个函数，你将找到给定字符串中单词的最短长度。我们可以从`split(), sort() and pop()`等一些功能中得到帮助。

```
const findShort = (s) => s.split(' ').sort((a, b) => b.length - a.length).pop().length;console.log(findShort("It's an amazing day to code"));
// Result: 2
console.log(findShort("Lorem Ipsum"));
// Result: 5
```

# 2.返回单词的中间字符

这个函数返回一个单词的中间字符。如果给这个函数一个奇数长度的单词，它将返回中间的字符。但是，如果给定长度为偶数的单词，它将返回中间的两个字符。

```
const getMiddle = s => s.substring(Math.ceil(s.length/2)-1, Math.floor(s.length/2)+1);console.log(getMiddle("Amazing"));
// Result: zconsole.log(getMiddle("Rubber"));
// Result: bb
```

# 3.将零移动到末尾

函数`moveZeros()`接受一个数组并将所有的零移动到末尾。在这样做的同时，它还保留了其他元素的顺序。

```
let moveZeros = (arr) => arr.filter(i => i !== 0).concat(arr.filter(i => i === 0));console.log(moveZeros([false, 1, 0, true, 1, 2, 5, 0, 1, 3, "Ajax"]));// Result: [false, 1, true, 1, 2, 5, 1, 3, "Ajax", 0, 0]
```

# 4.数组差异

如果您想从一个数组中减去另一个数组并返回差值，这是一个非常好的方法。它保留了结果列表中其他元素的顺序。

```
var array_diff = (a, b) => a.filter(item => b.indexOf(item) < 0)console.log(arrayDiff([1, true, "1", "2", 2, 5, 3], [false, "8", 3, 1]));
// Result: [true, "1", "2", 2, 5]console.log(arrayDiff([1, true, "1", "2"], [1, true, "1", "2", 3]));
// Result: []
```

# 5.消除字符串中的元音

使用`eliminateVowels()`功能，你可以删除给定字符串中的元音。

```
const eliminateVowels = s => s.replace(/[aeiou]/gi, '');console.log(eliminateVowels("The Master Splinter Developer"));
// Result: Th Mstr Splntr Dvlpr
```

# 6.无线电紧急呼救信号

使用这个函数，您可以检查在给定的字符串中是否有相同数量的“O”和“S”。

```
const SOS = s => s.replace(/o/ig, '').length == s.replace(/s/ig, '').length;console.log(SOS("SOS"));
// Result: Falseconsole.log(SOS("So so"));
// Result: Trueconsole.log(SOS(""));
// Result: True
```

# 7.降序排列的整数位数

您可以使用此函数返回一个数字降序排列的整数。如果你给这个函数一个非负整数会有所帮助。

该函数将把数字重新排列到整数中可能的最高数字。在这个函数中，我们会得到一些内置函数的帮助，这些函数就是`parseInt(), toString(), split(), sort(), reverse(), and join()`。

```
const descendingOrder = n => parseInt(n.toString().split("").sort().reverse().join(""));console.log(descendingOrder(234782)); // Result: 874322
console.log(descendingOrder(10010101010)); // Result: 11111000000
console.log(descendingOrder(123456)); // Result: 654321
```

# 8.找出奇数次出现的整数

这个函数将帮助你找到一个在给定数组中出现奇数次的整数。这个功能有一个不好的地方。如果你给出一个以上奇数次出现的整数，那就不行。

```
const findOdd = (a) => a.reduce((a, b) => a ^ b);console.log(findOdd([1, 2, 2, 1])); // Result: 0
console.log(findOdd([1, 2, 2, 1, 2, 3, 3])); // Result: 2
console.log(findOdd([1, 2, 3, 4])); // Result: 4 -> Not working
```

# 9.找出给定数组的最大子数组和

使用这个函数，您可以找到给定数组的最大子数组和。这是一个需要解决的大问题。不容易解决。尤其是如果你想用一行程序实现这个功能，就更有挑战性了。但是，你可以从一个内置函数得到帮助，这个函数就是`reduce()`。

```
const maxSequence = (a,sum=0) => a.reduce((max,v) => Math.max(sum = Math.max(sum + v, 0), max), 0);console.log(maxSequence([1,2,3,4])); // Result: 10
console.log(maxSequence([1,2,3,4,-8])); // Result: 10
console.log(maxSequence([1,-2,3,-2,5,-1,3,-9,1,6])); // Result: 8
```

# 10.第二个数组的字符串包含第一个数组的字符串吗？

你可以用这个函数来回答上面的问题。这个函数将返回一个按第一个数组中字符串的字典顺序排序的数组，这些字符串是第二个数组中字符串的子字符串。

```
const inArray = (arr1, arr2) => arr1.filter(item => new RegExp(item).test(arr2)).sort()console.log(inArray(["Donald", "Strong", "The"],["Stronger, "More", "They"]));
// Result: ["Strong", "The"]console.log(inArray(["Stronger", "More", "They"],["Donald, "Strong", "The"]));
// Result: []console.log(inArray(["Novice"],["NOVICE"]));
// Result: []
```

# 11.求 3 的前倍数

该函数删除最后一个数字，直到找到一个是 3 的倍数的数字。如果给定的参数已经是三的倍数，它将返回它。如果没有不是 3 的倍数的数字集合，它将返回 null。

自己用一个很好的例子，你可以考虑，如果一个整数的每一位数之和被三整除，那么这个整数就是三整除。

```
const f = prevMultOfThree = n => n ? n % 3 ? f(n/10 |0) : n : nullconsole.log(prevMultOfThree(3)); // Result: 3
console.log(prevMultOfThree(36)); // Result: 36
console.log(prevMultOfThree(1236)); // Result: 1236
console.log(prevMultOfThree(5245)); // Result: null
console.log(prevMultOfThree(4552)); // Result: 45
console.log(prevMultOfThree(11)); // Result: null
```

# 结论

今天，我向您展示了 11 个 JavaScript 一行程序。有些对你来说可能很容易，而有些却让你绞尽脑汁。学习这些一行程序中的内置函数也可以给你一个很好的视野。

下一步，你可以找到一些问题，借助我给你看的一些内置函数，试着写成一行。这种实践将帮助你成为一名忍者开发者，比尔·盖茨可能会聘请他作为开发人员。