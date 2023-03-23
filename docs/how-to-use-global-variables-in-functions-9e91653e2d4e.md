# 如何在函数中使用全局变量

> 原文：<https://betterprogramming.pub/how-to-use-global-variables-in-functions-9e91653e2d4e>

## 忘记范围，让我们走向全球

![](img/fef16dba1aca983b1b7bd615f731341b.png)

Adolfo Félix 在 [Unsplash](https://unsplash.com/s/photos/globe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

全局变量是不管范围如何都可以访问的变量。传统上，函数在局部范围内操作，对不作为参数传递的变量的访问受到限制。将变量设置为全局变量会破坏封装规则，从而使指定的变量更易于访问。

# 变量在函数内部可用

在 Python 中，变量可以在函数中访问。这可以在下面的代码片段中看到，其中没有值作为参数传递，但是变量`a`和`b`能够被打印出来。

```
def myFunction():
  print(a,b)a = "Terra"
b = "Branford"myFunction() # Terra Branford
print(a,b) # Terra Branford
```

# 修改是局部范围的

这些值也可以编辑；但是，一旦函数执行完成，在本地环境中所做的更改就会丢失。在下面的代码片段中，变量值被更改并打印出来，当在函数外部打印时，它们不受影响。

```
def myFunction():
  a = "Celes"
  b = "Chere"
  print(a,b)a = "Terra"
b = "Branford"myFunction() # Celes Chere
print(a,b) # Terra Branford
```

# 使用“全局”关键字

要全球化一个变量，在函数定义中使用`global`关键字。现在，对变量值的更改将被保留。在下面的代码片段中，`a`是全球化的，而`b`不是。随后，保持对`a`的改变，而`b`保持为功能执行前的原始值。

```
def myFunction():
  global a
  a = "Celes"
  b = "Chere"
  print(a,b)a = "Terra"
b = "Branford"myFunction() # Celes Chere
print(a,b) # Celes Branford
```