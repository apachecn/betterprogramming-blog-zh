# Javascript 中反转字符串的五种方法

> 原文：<https://betterprogramming.pub/5-ways-to-reverse-a-string-in-javascript-466f62845827>

![](img/58dd3022e96565712953d4d92fabc165.png)

**“编写一个函数来反转字符串”**是你在 JavaScript 面试中可能会被问到的最明显的算法问题之一，尤其是如果你刚刚开始软件工程师的职业生涯。

这篇博客将为这一挑战提出五种不同的解决方案。

# 1.For 循环

最直接的——也可以说是天真的方法——是利用`for loop`。在这种方法中，我们可以使用递减或递增循环来遍历字符串的每个字母，并创建一个新的反向字符串:

```
function reverse(str){
  let reversed = "";    
  **for** (var i = str.length - 1; i >= 0; i--){        
    reversed += str[i];
  }    
  return reversed;
}
```

然而，ES6 引入了新的`for loop`语法，比如`for…of`。它消除了在声明 for 循环时犯许多愚蠢的打字错误的可能性，并产生了一段更整洁的代码:

```
function reverse(str){
  let reversed = "";
  **for**(let char **of** str){
    reversed = char + reversed;
  }
  return reversed;
}
```

# **2。数组的 reverse()方法**

当你的面试官没有特别要求你避免使用内置的`reverse()`方法时，你绝对要好好利用。在 JavaScript 中，`reverse()`方法只存在于数组中，所以首先我们需要使用`split()`将字符串转换成数组。然后应用`reverse()`方法，最后`join()`将其全部恢复:

```
function reverse(str){
  return str.split("").reverse().join("");
}
```

# 3.数组的 Spread 语法(ES6) + reverse()方法

ES6 引入了另一种将字符串拆分成数组的方法，那就是扩展操作符`[…]`。尽管这个解决方案与前一个类似，但我相信它值得一提，因为它看起来和工作起来都非常棒。

```
function reverse(str){
  return [...str].reverse().join('');
}
```

# **4。数组的 reduce()方法**

我在反向字符串讨论中很少见到的一种非常规方法是使用`reduce()`方法。同样，它只对数组有效，所以首先我们需要分割我们的字符串。然后，我们将我们的值累积到一个空字符串中，该空字符串在操作结束时成为原始字符串的反转:

```
function reverse(str){
  return str.split("").reduce((rev, char)=> char + rev, ''); 
}
```

# **5。递归**

最后，但肯定不是最不重要的解决反向字符串问题的方法是递归。我们让函数调用它自己`string.length`，直到它碰到我们的基本情况，一个空字符串。我们使用`substr()`方法将字符串的第一个字符去掉，然后将其添加到字符串的末尾。

```
function reverse(str){
 if(str === ""){
  return str 
 }else{
  return reverse(str.substr(1)) + str[0]
 }
}
```

重构递归的一种方法是使用三元运算符:

```
function reverse(str){
 return str ? reverse(str.substr(1)) + str[0] : str
}
```

太棒了。

既然你已经学会了五种很酷的扭转绳子的方法，你就可以通过讨论忍者踢这个挑战的各种方法来给你的面试官留下深刻印象！