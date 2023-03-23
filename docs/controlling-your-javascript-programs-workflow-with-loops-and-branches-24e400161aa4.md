# 用循环和分支控制 JavaScript 程序的工作流

> 原文：<https://betterprogramming.pub/controlling-your-javascript-programs-workflow-with-loops-and-branches-24e400161aa4>

## 透过表面看我们认为理所当然的 JavaScript 部分

![](img/c3dec2cb11333035f42fb887615212e6.png)

扎克·赖纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 条件语句

条件语句允许我们的代码根据给定的条件以不同的方式运行。

例如，如果我们想在`age`小于 21 时显示“你太小，不能喝酒”，我们可以使用条件语句来实现这一点。

有几种方法可以在 JavaScript 中有条件地运行代码。一个是`if...else`语句。如果你有很多案例，那么你可以使用`switch`语句来简化你的代码。

## 如果…否则

`if...else`语句是一种根据传递给`if`语句的条件的条件来运行代码的方式。

无论传递给它的条件是什么，都必须计算为布尔表达式，或者可以自动转换为布尔表达式。

`if`语句可以单独使用，但是`else`语句必须和`if`语句一起使用。当`if`语句为假时，当您想要执行某个操作时，可以使用`else`语句。

如果你想在给定的条件下运行一些东西，你也可以写`else if (condition)`，你有多个不同条件的情况。

为了编写一个`if...else`语句，我们编写:

```
if (condition){
 // run code
}
```

我们在最简单的情况下这样做，比如当我们只想在有条件的情况下运行一些东西。

如果我们想在`condition`为假时运行不同的东西，那么我们写:

```
if (condition){
 // run code
}
else {
 // run other code
}
```

如果我们有两种以上的情况，那么我们可以像这样使用`else if`:

```
if (condition1){
 // run code
}
else if (condition2){
 // run different code
}
else {
 // run code when condition1 and condition2 are both false
}
```

但是`else if`和`else`并不总是可选的。

如果你想记录给饮酒者的信息，我们可以写:

```
const age = 19;
if (age < 21){
  console.log('You are too young to drink');
}
else {
  console.log('You are old enough to drink');
}
```

在上面的代码中，如果`age`小于 21，我们记录“你太小了，不能喝酒”。因为`age`是 19，小于 21，所以我们记录“你太小了，不能喝酒。”如果`age`设置为 22，那么我们记录“你已经到了可以喝酒的年龄了。”

如果您的`if...else`陈述只有两种情况，我们可以将其缩短为:

```
condition ? a : b
```

在这里，`a`和`b`是你想要执行的任何一段代码，但是如果`a`和`b`很短的话应该使用它们，因为它们应该写在一行中。

所以对于上面的例子，我们也可以写成:

```
const age = 19;
console.log(age < 21 ? 'You are too young to drink' : 'You are old enough to drink');
```

如果我们想用多个案例写`if`语句，我们写:

```
const score = 80;
if (score >= 80){
  console.log('You get an A');
}
else if (score >= 65 && score < 80){
  console.log('You get a B');
}
else if (score >= 50 && score < 65){
  console.log('You get a C');
}
else {
  console.log('You failed');
}
```

在上面的例子中，我们有多个案例。我们根据`score`的范围决定记录什么。

如果`score`等于或大于 80，我们记录“你得到 a”。如果`score`大于或等于 65 且小于 80，我们记录“你得到 a”。如果`score`大于或等于 50 且小于 65，我们记录“你失败了”。否则，我们记录“你失败了。”

## 交换语句

上面的`if...else`语句也可以用`switch`语句重写。一个`switch`语句可以这样写:

```
switch (expression) {  
  case value1:  
    // Statements  
    break;  
  case value2:  
    // Statements  
    break;  
  case value3:  
    // Statements  
    break;  
  default:  
    // Statements  
    break;
}
```

在上面的`switch`块中，如果`expression`等于`value1`，则执行该块中的语句。

这条规则也适用于其他区块。在每个`case`块的末尾都有`break`语句是很重要的，这样一旦正确的块被运行，它就不会运行其他的`case`块。如果给定的值与`case`块中的任何值都不匹配，则`default`块运行。

给定一个`language`，记录“Hello”的 switch 语句示例如下:

```
const language = "Spanish";
switch (language){
 case "English":
   console.log("Hello");
   break;
 case "Spanish":
   console.log("Hola");
   break;
 case "Portugese":
   console.log("Ola");
   break;
 case "French":
   console.log("Bonjour");
   break;
 default:
   console.log(`I don't speak ${language}`);
}
```

在这个语句中，如果我们将`language`设置为英语、西班牙语、葡萄牙语或法语，我们会记录一条 hello 消息。否则，我们记录``I don’t speak ${language}``，其中`language`是除英语、西班牙语、葡萄牙语或法语之外的任何内容。

![](img/2afcc5386382ccddc9bf4620558cf620.png)

照片由[Tine ivani](https://unsplash.com/@tine999?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 环

循环用于运行重复的操作。我们需要它们，这样我们可以避免多次编写相同的代码。他们让我们重复运行代码，直到满足结束条件。

在 JavaScript 中，我们有`for`循环、`while`循环和`do...while`循环。

## For 循环

`for`回路有三个部分。

有初始化代码，它让我们定义如何通过将变量设置为某个初始值来开始循环。

它们还有一个在每次迭代中评估的条件，如果条件为真，循环将继续执行。

然后是第三个表达式，它在循环的每次迭代后运行。

这三个表达式不是必需的，但它们几乎总是包含在内。它们通常用于重复运行代码一定的次数。

例如，对于`for`循环，假设我们有`name`数组，我们可以通过运行以下命令来遍历该循环:

```
let names = new Array('Bob','Jane', 'John');
for (let i = 0; i < names.length; i++){
  console.log(names[i]);
}
```

一个`for`循环通常以开始条件作为第一条语句，然后是第二条语句的结束条件，第三条语句的索引改变条件。这些语句用分号分隔。因为在 JavaScript 中数组从零开始，所以当数组的索引比数组的长度小 1 时，我们终止循环。

在上面的示例中，运行循环的步骤是:

1.  变量`i`在循环开始时被设置为 0
2.  `names[i]`从阵列中检索并记录在控制台中
3.  `i`增加 1，因此`i`变为 2
4.  重复以上三步，直到`i`变为 3，这是`names.length`的值，循环结束，因为`3 < 3`为`false`。

## 对于…在循环中

`for...in`循环让我们遍历对象的属性。

例如，如果我们有这个物体…

```
let obj = {a: 1, b: 2, c: 3}
```

…然后我们可以使用`for...in`循环来遍历该对象的属性，方法是运行:

```
for (let prop in obj){
  console.log(obj[prop]);
}
```

`prop`将包含对象的属性名或键。因此当循环运行时，`prop`将为`a`、`b`或`c`。在上面的循环中，我们通过将`prop`传递到`obj`对象的括号中来记录这些键的值。

## For…of 循环

`for...of`循环让我们以一种方便的方式遍历一个像数组一样的可迭代对象。它消除了像普通 for 循环那样设置遍历整个数组的条件的需要。

例如，如果我们想使用`for...of`循环来遍历上面的`names`数组，那么我们写:

```
let names = new Array('Bob','Jane', 'John');
for (const name of names){
  console.log(name);
}
```

当我们运行循环时，`name`变量自动获得`names`变量的一个条目——所以我们首先记录`Bob`，然后记录`Jane`，然后记录`John`。

注意，我们使用了`const`,因为我们不必对数组条目做任何事情，但是如果我们想对每个条目做一些事情，我们也可以使用`let`。

例如，如果我们通过写`let nums = [1,2,3]`来定义`nums`，那么我们可以通过写:

```
let nums = [1,2,3];
for (let num of nums){
  num += 1;
  console.log(name);
}
```

通过为`let`切换出`const`，那么我们可以为`nums`的每个条目加 1。所以我们记录 2、3 和 4。

其他使用`for...of`循环的可迭代对象包括 map、set、strings 和 TypedArray。

如果我们循环遍历一个字符串，那么我们在每次迭代中得到这个字符串的单个字符。例如，如果我们有…

```
const str = 'foo';

for (const char of str) {
  console.log(char);
}
```

…然后我们在每次迭代中得到`f`、`o`和`o`。

对于 TypedArrays，如果我们有…

```
const typedArray = new Uint8Array([0x00, 0x06]);

for (const value of typedArray) {
  console.log(value);
}
```

…然后当我们记录条目时，我们得到 0 和 6。

如果我们循环遍历一个地图，我们会得到如下所示的地图条目:

```
const map = new Map([['a', 4], ['b', 5], ['c', 6]]);

for (const entry of map) {
  console.log(entry);
}
```

我们记录了`['a', 4]`、 `['b', 5]`和`['c', 6]`。

此外，我们可以使用析构操作符来分解每个条目的键和值，如下所示:

```
for (const [key, value] of map) {
  console.log(value);
}
```

在上面的代码中，我们只记录了`map`的值，分别是 4、5 和 6。

集合也是可迭代的对象，所以我们可以遍历它们，就像这样:

```
const set = new Set([1, 1, 2, 2, 3, 6]);

for (const val of set) {
  console.log(val);
}
```

我们应该记录 1、2、3 和 6，因为集合消除了重复值。

`arguments`对象是每个函数中都有的特殊对象。不管您在参数中定义了什么，它都将参数传递给对象。我们可以用`for...of`循环遍历它

例如，如果我们有…

```
(function() {
  for (const arg of arguments) {
    console.log(arg);
  }
})(4, 5, 6);
```

…然后我们记录 4、5 和 6，因为这是我们作为参数传递给匿名函数的内容。

## While 循环

只要条件保持为真，`while`循环就会循环。

例如，如果索引号`i`小于 3，将运行下面的循环:

```
const array = [1,2,3];let i = 0;
while(i < array.length){
  console.log(i);
}
```

如果括号中的条件从不为真，那么循环内容将永远不会运行，如果它总是为真，那么它将永远运行，从而创建一个无限循环。

## Do…while 循环

`do...while`循环将总是执行第一次迭代。

```
const array = [1,2,3];let i = 0;
do{
  console.log(i);
}
while(i < array.length)
```

在上面的例子中，它至少会记录 0，但是在这种情况下它也会记录 1 和 2，因为这两个数字小于 3。

使用上述循环，您可以在循环完全结束前调用`break`停止循环或`return`。

```
//do while loop
const array = [1,2,3];let i = 0;do{
  console.log(i);  if (i == 1}{    break;  }}
while(i < array.length)//while loop
i = 0;while(i < array.length){  if (i == 1{    break;  }
  console.log(i);
}//for loop
for (let j = 0; j < array.length; j++){  if (j == 1){
    break;
  }
  console.log(j);
}
```

在上面的例子中，您不会看到 2 个日志。

从循环内部返回的示例:

```
const loop = ()=>{
  const array = [1,2,3];  
  for (let j = 0; j < array.length; j++){       
    if (j == 1){
      return j;
    }
    console.log(j);
  }
}
loop() //returns 1
```

你也可以用`continue`语句跳过迭代:

```
const array = [1,2,3];
for (let j = 0; j < array.length; j++){  if (j == 1){
    continue;
  }
  console.log(j) // 1 will be skipped;
}
```