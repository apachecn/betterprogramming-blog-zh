# 我的第一轮新语言清单

> 原文：<https://betterprogramming.pub/my-round-1-new-language-checklist-62d9e11b9335>

## 学习一门新的编程语言时首先要做的事情

![](img/130b78aaf86b1f2b6fd331cf14b3f47c.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

总有一天，所有的工程师都必须学会一门新语言。这是我第一次接触语言的方法。

# 避免句法糖

在我的第一轮中，我避免任何让语言变得美好的东西。我想做的是找出语言中与其他语言重叠的部分。

例如，不是每种语言都有 C 风格的循环(Rust 没有)，但是所有语言都有某种形式的`while`。因此，当寻找如何在一种语言中循环时，我将首先确定如何定义一个`while`循环。

目标/希望是找到新语言和我所知道的语言之间的一致性，以便建立一个工作的基线。为了建立这个基线，我会问类似这样的问题:“在这种新语言中，我如何像在我熟悉的语言中那样做一个基本的 while 循环？”

# 假设间距很重要

在一种语言中，空格要么重要，要么无关紧要。在第一遍中，我假设间距很重要。不管它是否存在，间距使代码更具可读性。

我确保我总是使用空格或制表符(不是两者都用),并且缩进量是一致的——例如，2 或 4。

# 假设造型不重要

一些语言对事物应该如何命名有很大的偏好。通常，这些会在某一步变成警告。在这第一遍语言中，我忽略这些。我选择了一种定义变量的恒定策略，如 camelCase，并在此时将其用于所有命名选择。

如果这种语言有其他的风格偏好，也忽略它们。

# 创建标准文件

所有语言都有首选扩展名。如果我做得不好，编译器会抱怨，文本编辑器也不会那么有用。所以我确定那是什么。

*   Javascript — `someScript.js`
*   Python— `someScript.py`
*   红宝石— `someScript.rb`
*   C — `someScript.c`
*   C ++— `someScript.cpp`
*   C# — `someScript.cs`
*   铁锈— `someScript.rs`
*   Go — `someScript/someScript.go`

# 单行注释

学习一门新语言时，能够自己做笔记是非常重要的。为此，我需要知道如何进行评论。大多数语言支持多种评论策略。单行注释是一个很好的开始方式。

我不认为在这一点上可以在行尾添加注释。我把评论放在他们自己的行上。

*   JavaScript — `// Some comment`
*   Python — `# Some comment`
*   红宝石— `# Some comment`
*   C — `/* Some comment */`
*   C++ — `// Some comment`
*   C# — `// Some comment`
*   生锈— `// Some comment`
*   Go — `// Some comment`

# 简单字符串文字

与其他类型相比，使用字符串更有趣，并且对于记录基本信息很有用。简单的字符串在初始测试中很重要。

*   JavaScript — `'Some string'`
*   Python — `'Some string'`
*   红宝石— `'Some string'`
*   C — `"Some string"`
*   C++ — `"Some string"`
*   C# — `"Some string"`
*   生锈— `"Some string"`
*   Go — `"Some string"`

# 打印到控制台

这将是初始测试所需要的，也是开始时和以后的强力调试所需要的。

*   JavaScript — `console.log('Some string');`
*   Python — `print('Some string')`
*   红宝石— `puts 'Some string'`
*   C — `printf("Some string");`
*   C++ — `cout << "Some string";`
*   C# — `Console.WriteLine("Some string");`
*   铁锈— `println!("Some string");`
*   去— `fmt.Println("Hello World")`

# 样板代码

为了正确执行，一些语言需要包含代码基线。通常，这段代码告诉程序应该从哪里开始。我发现了这个样板代码。

JavaScript、Python、Ruby: 无

**C:**

```
#include <stdio.h>
int main() {
  /* Logic goes here */
  return 0;
}
```

**C++:**

```
#include <iostream>
int main() {
  /* Logic goes here */
  return 0;
}
```

**C#:**

```
using System;

public class Program {
  public static void Main() {
    // Logic goes here  
  }
}
```

**铁锈:**

```
fn main() {
  // Logic goes here
}
```

**去:**

```
package main

import "fmt"

func main() {  
    // Logic goes here
}
```

# 如何编译代码

低级语言需要编译。我知道如何做到这一点。

*   JavaScript、Python、Ruby — 不适用
*   C — `gcc index.c -o scriptName.exe`
*   C++ — `g++ index.cpp -o scriptName.exe`
*   C# — `mcs -out:scriptName.exe scriptName.cs` ( [单声道](https://www.mono-project.com/download/stable/))
*   铁锈— `rustc someScript.rs`
*   Go — `go build`(从项目目录中)

# 如何执行代码

一旦代码被编译(如果必要的话)，我就确定如何执行它。

*   JavaScript(node . js)——`node scriptName.js`
*   Python — `python scriptName.py`
*   红宝石— `ruby scriptName.rb`
*   C — `./scriptName.exe`
*   C++— `./scriptName.exe`
*   C# — `mono ./scriptName.exe`
*   铁锈— `./someScript`
*   Go — `./someScript`

# 可重新分配的变量

我喜欢常量，因为它们增加了代码的可预测性。然而，在第一次学习语言时，我想知道如何定义可重赋值变量，因为它涵盖了更大的用例集，比如递增变量。

此时，我假设所有变量都是块范围的。

*   JavaScript — `let x = 'Some string';`
*   Python — `x = 'Some string'`
*   Ruby — `x = 'Some string'`(变量必须以小写字母开头)
*   C — `char x[] = "Some string";`
*   C ++— `char x[] = "Some string";`
*   C# — `string x = "Some string";`
*   生锈— `let mut x = "Some string";`
*   Go — `var x string = "Some string";`

# 基本功能:定义、调用、返回

函数对于代码的可重用性和可理解性非常重要。在这一点上，我假设函数在被调用之前必须被定义。我还假设我必须总是显式地从函数中返回值。

JavaScript:

```
function functionNameX (argA, argB, argC) { 
  return 'Some string';
}functionNameX('a', 'b', 'c');
```

Python:

```
def functionNameX (argA, argB, argC):
  return 'Some string'functionNameX('a', 'b', 'c')
```

红宝石:

```
def functionNameX (argA, argB, argC)
  return 'Some string'
endfunctionNameX('a', 'b', 'c')
```

丙:

```
char * functionNameX (char * argA, char * argB, char * argC) {
  return "Some string";
}functionNameX("a", "b", "c")
```

C++:

```
char * functionNameX (char * argA, char * argB, char * argC) {
  return "Some string";
}functionNameX("a", "b", "c")
```

C#:

```
// Placed within the "Program" classpublic static string functionNameX(string argA, string argB, string argC) {
  return "Some string";
}// Placed within Main()
functionNameX("a", "b", "c");
```

生锈:

```
fn functionNameX (argA: &str, argB: &str, argC: &str) -> String {
  return String::from("Some string");
}functionNameX("a", "b", "c");
```

去吧:

```
func functionNameX(argA string, argB string, argC string) string {
 return "Some string";
}functionNameX("a", "b", "c");
```

# 比较运算符

我想知道如何比较变量。因为不是所有的语言都有类型强制，所以我认为所有的语言都没有类型强制。在可能强制的语言中，我使用严格比较来确保变量类型是相同的。

## **JavaScript**

*   等于，不等于:`===`，`!==`
*   小于、小于或等于:`<`、`<=`
*   大于，大于或等于:`>`，`>=`

## **Python，Ruby，C，C++，C#，Rust，Go**

*   等于，不等于:`==`，`!=`
*   小于、小于或等于:`<`、`<=`
*   大于、大于或等于:`>`、`>=`

# 逻辑(和/或/非)运算符

对于决策来说，识别语言的逻辑操作符很重要。

## **Python**

*   还有:`and`
*   或者:`or`
*   不是:`not`

## **红宝石**

*   还有:`and`，`&&`
*   或者:`or`，`||`
*   不是:`not`，`!`

## **JavaScript，C，C++，C#，Rust，Go**

*   还有:`&&`
*   或者:`||`
*   不是:`!`

# If 条件语句

我不在乎条件句的不同味道。此时我只关心简单的`if`条件。`Else`可以在以后方便地查找，或者通过在函数底部提供逻辑来完成，在函数中，我从每个满足的`if`条件返回。

**JavaScript:**

```
if('Some string' === 'Some string') { 
  // do something 
}
```

**Python:**

```
if 'Some string' == 'Some string':
  # do something
```

**红宝石:**

```
if 'Some string' == 'Some string'
  # do something
```

**C:**

```
if("Some string" == "Some string") {
  // do something
}
```

**C++:**

```
if("Some string" == "Some string") {
  /* do something */
}
```

**C#:**

```
if("Some string" == "Some string") {
  // do something
}
```

**铁锈:**

```
if "Some string" == "Some string" {
  // do something
}
```

**去:**

```
if "Some string" == "Some string" {
  // do something
}
```

# 数组文字:定义，长度，检索第一项

使用集合是编程最强大的方面之一。首先，我想了解如何定义一个简单的数组，知道它的长度，以及如何访问它的索引值。

我还确保我知道如何访问数组的第一项(通常从零开始)。

**JavaScript:**

```
let arrayTest = [ 'a', 'b', 'c' ];
arrayTest.length;
array[0];
```

**Python:**

```
arrayTest = [ 'a', 'b', 'c' ];
len(arrayTest);
array[0];
```

**红宝石:**

```
arrayTest = [ 'a', 'b', 'c' ];
arrayTest.size;
arrayTest.length;
array[0];
```

**C:**

```
char arrayTest[] = { 'a', 'b', 'c' };
sizeof(arrayTest);
arrayTest[0];
```

**C++:**

```
char arrayTest[] = { 'a', 'b', 'c' };
sizeof(arrayTest);
arrayTest[0];
```

**C#:**

```
string[] arrayTest = { "a", "b", "c" };
arrayTest.Length;
arrayTest[0];
```

**铁锈:**

```
let mut arrayTest = [ "a", "b", "c" ];
arrayTest.len();
arrayTest[0];
```

**去:**

```
var testArr []string = []string{"a", "b", "c" };
len(testArr);
testArr[0];
```

# While 循环

最后，不是所有语言都支持所有类型的循环。比如 Rust 不支持 C 风格的 For 循环。然而，排除极端的异常值，所有语言都支持简单的`while`循环，这足以实现我需要的任何类型的循环。

**JavaScript:**

```
let array = ['a', 'b', 'c'];
let i = 0;
let max = array.length;
while(i < max) {
  let currentArrayValue = array[i];
  // Do something
  i = i + 1;
}
```

**Python:**

```
array = ['a', 'b', 'c'];
i = 0;
max = array.length;
while(i < max):
  currentArrayValue = array[i];
  # Do something
  i = i + 1; 
```

**红宝石:**

```
array = ['a', 'b', 'c'];
i = 0;
max = array.length;
while i < max do
  currentArrayValue = array[i];
  # Do something
  i = i + 1;
end
```

**C:**

```
char arrayTest[] = { 'a', 'b', 'c' };
int i = 0;
int max = sizeof(arrayTest);
while(i < max) {
  char currentArrayValue = arrayTest[i];
  /* Do something */
  i = i + 1;
}
```

**C++:**

```
char arrayTest[] = { 'a', 'b', 'c' };
int i = 0;
int max = sizeof(arrayTest);
while(i < max) {
  char currentArrayValue = arrayTest[i];
  // Do something
  i = i + 1;
}
```

**C#:**

```
string[] arrayTest = { "a", "b", "c" };
int i = 0;
int max = arrayTest.Length;
while(i < max) {
  string currentArrayValue = arrayTest[i];
  // Do something   
  i = i + 1;
}
```

**铁锈:**

```
let mut arrayTest = [ "a", "b", "c" ];
let mut i = 0;
let mut max = arrayTest.len();
while i < max {
  let mut currentArrayValue = arrayTest[i];
  // Do something;
  i = i + 1;
}
```

**去:**

```
var testArr []string = []string{ "a", "b", "c" }
var i int = 0;
var max int = len(testArr); 
for i < max {
  var currentValue string = testArr[i];
  // Do something
  i = i + 1;
}
```

# 摘要

对我来说，这就是第一轮。对我来说，了解新语言的基础并从那里开始深入细节就足够了。

我希望这有所帮助。