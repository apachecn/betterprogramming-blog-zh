# 流行的 JavaScript 数组方法

> 原文：<https://betterprogramming.pub/popular-javascript-array-methods-ca4d24c9243f>

## 最好的点击率，都在一个地方

![](img/5cc4188377f9096c3e3e85d022b9bc0a.png)

在深入研究数组方法之前，有两个术语必须澄清:

*   一个*破坏性方法*改变原始数组。
*   *非破坏性方法*不会*不会*改变原始数组——它们创建原始数组的副本。

同样重要的是要注意 JavaScript 中有更多的数组方法。这些只是最常见的一些。

# concat()

Concat 用于连接多个数组。它不是破坏性的，所以它返回它所连接的数组的副本。

```
var array1 = [“Hello”];var array2 = [“ World!”];var output = array1.concat(array2); //output: “Hello, World!”
```

# 过滤器()

Filter 创建一个新数组，该数组包含数组中符合指定条件的所有元素。

```
var array = [1, 0, 3, "puppies", false, 5, 4, 7];   
var output = array.filter((element)=>{ return element > 4; });//output: 5,7
```

# 查找()

Find 返回数组中符合指定条件的第一个元素。

```
var array = ["puppies", "dogs", "puppy", "my dog", "doggo"];   
var output = array.find((dog)=>{ return dog === "my dog"; });//output: "my dog"
```

# forEach()

For each 对数组的每个元素调用一个函数。

```
var array = ["Hello", "World"];array.forEach((element)=> {
  console.log(element);
});//output: "Hello"
//output: "World"
```

# 包括()

Includes 返回一个布尔值。如果数组包含符合指定条件的元素，includes 将返回 true。如果数组不包含这样的元素，includes 将返回 false。

```
var pets = ['dog', 'cat', 'hamster'];console.log(pets.includes('dog'));
// output: trueconsole.log(pets.includes('dinosaur'));
// output: false
```

# 加入()

Join 将数组中的所有元素连接成一个字符串。它还允许指定分隔符。

```
var array = ["Look", "at", "all", "the", "smiles!"];
var joined = array.join(" :) ");//joined: Look :) at :) all :) the :) smiles!
```

# 地图()

Map 创建一个新数组。该数组的每个元素都是一个函数的输出，该函数将数组的每个元素作为输入。

```
var array = [1, 2, 3, 4];const map = array.map(x => x * 2);console.log(map);
// output: [2, 4, 6, 8]
```

# 流行()

流行音乐具有破坏性。它移除数组的最后一个元素，并返回移除的元素。

```
var pets = ["dog", "cat", "hamster"];console.log(pets.pop());
// output: "hamster"
```

# 推送()

推是破坏性的。它向数组末尾添加新元素，并返回数组的新长度。

```
var pets = ["dog", "cat", "hamster"];console.log(pets.push("bird", "fish"));
// output: ["dog", "cat", "hamster", "bird", "fish"]
```

# 减少()

将数组的值缩减为单个值。reducer 有四个参数，一个累加器、一个当前值、一个当前索引和一个源数组。源阵列是正在减少的阵列。当前值是缩减器当前正在处理的元素的值。当前索引是该元素的索引。累加器累加归约器已经处理过的元素的返回值。

```
const array = [1, 2, 3];const reducer = (accumulator, currentValue) => accumulator + currentValue;console.log(array.reduce(reducer));
// output: 6console.log(array.reduce(reducer, 4));
// output: 10
```

# 反向()

反转数组中所有元素的顺序。

```
var pets = ["dog", "cat", "hamster"];console.log(pets.reverse());
// output: ["hamster", "cat", "dog"]
```

# shift()

转变是破坏性的。它移除数组的第一个元素，并返回移除的元素。

```
var pets = ["cat", "dog", "hamster"];console.log(pets.shift());
// output: ["cat"]
```

# 切片()

Slice 选择数组的一部分，并返回包含所选元素的新数组。

```
var pets = ["dog", "cat", "hamster", "bird", "fish"]console.log(pets.slice(2, 4));
// output: ["hamster", "bird"]
```

# 排序()

排序对数组中的元素进行排序。它按升序排列数字，按字母顺序排列字符串。

```
var pets = ["dog", "cat", "hamster", "bird"];
console.log(pets.sort());
// output: ["bird", "cat", "dog", "hamster"]var array = [1, 3, 4, 2];
console.log(array.sort());
// output: [1, 2, 3, 4]
```

# toString()

将数组转换为字符串。它返回结果字符串。

```
var pets = ["dog", "cat", "hamster"];console.log(pets.toString());
// output: "dog,cat,hamster"
```

# 未移位()

不移位是破坏性的。它向数组的开头添加新元素，并返回数组的新长度。

```
var pets = ["dog", "cat", "hamster"];console.log(pets.unshift("bird"));
// output: ["bird", "dog", "cat", "hamster"]
```