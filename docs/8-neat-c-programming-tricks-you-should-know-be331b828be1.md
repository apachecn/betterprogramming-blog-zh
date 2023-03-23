# 你应该知道的 8 个简洁的 C++编程技巧

> 原文：<https://betterprogramming.pub/8-neat-c-programming-tricks-you-should-know-be331b828be1>

## 掌握一些关键的 C++概念，节省几行代码

![](img/1e9637c3089e05956b008852d7df3384.png)

照片由[内特·格兰特](https://unsplash.com/@nateggrant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 1.一次性包含所有标准库

使用`**#include** <bits/stdc++.h>`在你的项目中包含所有的标准库，而不是单独包含它们。这在时间宝贵的编程竞赛中尤其有用。

例如，您可以替换这个(以及更多):

```
**#include** <iostream>
**#include** <algorithm>
**#include** <vector>
**#include** <string>
**#include** <stack>
**#include** <set>
**#include** <queue>
**#include** <map>
```

有了这个:

```
**#include** <bits/stdc++.h>
```

**谨记**

*   `<bits/stdc++.h>`包含了很多你可能不需要在项目中使用的头文件。这最终会增加编译时间。
*   `<bits/stdc++.h>`不是 GNU C++库的标准头文件。因此，非 GCC 编译器可能很难编译。然而，大多数时候这是不会发生的！

# 2.使用 Auto 省略变量的数据类型

在 C++ 11 和更高版本中，可以通过使用`auto`关键字省略变量的数据类型。当你需要在运行时声明一个变量时，例如，当使用迭代器时，这是非常有用的。

例如，以下变量的数据类型在运行时声明。

```
**auto** a = 'a';
**auto** t = true;
**auto** x = 1;
**auto** y = 2.0;
```

# 3.全面的基于范围的 for 循环

基于范围的 for 循环是传统 for 循环的升级版本，它是在 C++ 11 中引入的。

基于范围的 for 循环的语法是:

```
for(range_declaration : range_expression)
```

例如，您可以使用基于范围的 for 循环遍历数字数组，如下所示:

```
**int** numbers[] = {1,2,3,4,5};**for** (**auto** number: numbers){
    cout << number << endl;
}
```

或者，您可以使用基于范围的 for 循环以类似的方式遍历字符串中的字符:

```
string name = "Charlie";**for** (**auto** character: name){
    cout << character << endl;
}
```

# 4.一行 If…Else 语句

您可以使用三元运算符(也称为条件运算符)轻松地将`if...else`语句转换成一行程序。

例如，您可以替换此表达式:

```
**int** age = 9;

**if**(age < 18) {
    printf("A Child");
} **else** {
    printf("An Adult");
}
```

用更简洁的简写，就像这样:

```
age < 18 ? printf("A Child") : printf("An Adult");
```

三元运算符的一般语法是:

```
condition ? true_expression : false_expression
```

三元运算符在其他编程语言中也很常见，因此它绝对不仅仅是“C++的事情”

**提示:**过度使用三元运算符会降低代码的可读性。只有当`if...else`语句作为一行程序足够清晰时，才使用它是有益的。

# 5.交换两个变量，不交换第三个变量

您可以使用`XOR`操作符交换两个变量，而不使用第三个辅助变量。这里有一个例子:

```
**int** a = 1;
**int** b = 2;

a ^= b ^= a ^= b;
```

# `6\. The -->` 操作员

您可以在 while 循环中使用`-->`“操作符”作为“Goes to”操作符。

例如，您可以像这样用 while 循环打印数字`9 8 7 6 5 4 3 2 1`:

```
**int** x = 10;**while**( x --> 0 ) { 
    printf("%d ", x);
}
```

**注:** `-->`其实不是一个运算符，而是两个运算符的组合，`--`和`>`。上面的`while`和`while( (x--) > 0 )`一样，都是“用`1`递减`x`，然后和`0`比较结果。”

# 7.前增量比后增量快

在 C++中，有两个运算符可用于通过`1`递增一个值:

*   预递增，`++i` —在给变量赋值之前，值递增 1。
*   后递增，`i++` —将值赋给变量后，值递增。

因此，前增量`++i`比后增量快，因为后增量保留了前一个值的副本，其中前增量直接加 1，而不复制前一个值。

# 8.将赋值与函数调用结合起来

在 C++中，可以将赋值和函数调用结合起来。

而不是这个:

```
int i;i = 10;
printf("%d", i); // prints 10
```

您可以这样做来节省一些代码行:

```
int i;printf("%d", i = 10); // prints 10
```

## 就是这样！

感谢阅读！希望你觉得有用。

# 资源

[](https://devdocs.io/cpp/) [## DevDocs

### 具有即时搜索、离线支持、键盘快捷键、移动版本等功能的 C++ API 文档。

devdocs.io](https://devdocs.io/cpp/)