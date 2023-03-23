# Python 有三元条件运算符吗？

> 原文：<https://betterprogramming.pub/does-python-have-a-ternary-conditional-operator-830454c34de7>

## Python 编写 if/else 语句的简写方法

![](img/2ade1d7f43fddaaba2d639cfdc9f8083.png)

照片由 [Dil](https://unsplash.com/@thevisualiza?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/doors?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

**简而言之就是*是的*** 。

三元条件运算符是编写 if/else 语句的一种简便方法。三元运算符有三个组成部分:表达式/条件、正值和负值。

三元运算符传统上用问号和冒号表示，组织为`expression ? positive value : negative value`

当表达式计算结果为 true 时，使用正值，否则使用负值。

Python 不遵循与以前语言相同的语法；然而，这种技术确实存在。在 Python 中，组件被重组，并使用关键字`if`和`else`，读作`positive value if expression else negative value`

给定 Python 语法，三元运算符转换如下:

```
# Traditional Ternary Operator
can_vote = (age >= 18) true : false;# Python Ternary Operator
can_vote = True if age >= 18 else False
```

请记住，三元运算符的目的是编写更简洁的代码，提高可读性。然而，Python 实现可能会产生相反的效果，读起来令人困惑。此外，缺少显式操作符会导致操作顺序不一致。

```
x = 5
y = 10z = 1 + x if x == 1 else y   # 10
z = 1 + (x if x == 1 else y) # 11
```