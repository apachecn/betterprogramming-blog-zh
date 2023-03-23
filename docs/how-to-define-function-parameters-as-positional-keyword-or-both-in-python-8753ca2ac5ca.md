# 如何在 Python 中将函数参数定义为位置参数、关键字参数或两者都定义

> 原文：<https://betterprogramming.pub/how-to-define-function-parameters-as-positional-keyword-or-both-in-python-8753ca2ac5ca>

## 开始在函数定义中使用新的/和*符号

![](img/4f46e8629fdf293ba0a07a9c827418b4.png)

由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/keyword?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

从 Python 3.8 开始，现在可以将函数参数定义为仅位置参数、仅关键字参数或可选关键字参数。

让我们讨论一下什么是函数，函数参数的类型，最后，如何将参数定义为位置参数、关键字参数或两者兼有。

# 什么是功能？

简而言之，函数是定义好的可以重复调用的语句序列——称为*函数调用。*

把函数的定义想象成一个黑盒，意味着函数中发生的事情留在函数中。这个规则的例外是指定的输入(称为参数)和指定的输出(称为返回)。

使用函数创建一系列语句，这些语句在受控环境中完成较小的任务，并受益于性能更好且更易于维护的代码。

# 函数参数的类型

定义自定义函数时，保存传入值的变量称为参数。

传统上，参数列表的顺序非常关键，因为参数和实参的顺序是一对一匹配的。这些被称为*位置参数。*

```
def myFunction(a,b,c):
   print(a,b,c)myFunction(1,2,3) # 1,2,3
```

在 Python 中，函数参数也可以指定为*关键字参数*。在这种情况下，调用函数时会包含参数名，从而显式声明哪个值位于何处。

```
def myFunction(a,b,c):
   print(a,b,c)myFunction(b=2,c=3,a=1) 1,2,3
```

此外，可以为参数定义默认值，从而使其成为可选的。

```
def myFunction(a,b,c=None):
   print(a,b,c)myFunction(1,2,3) # 1,2,3
myFunction(1,2)   # 1,2,None
```

当函数中的某些参数是可选的，并且您只想指定参数的子集时，使用关键字参数很有帮助。

```
def myFunction(a,b,c=None,d=None):
   print(a,b,c,d)myFunction(1,2,d=4) # 1,2,None,4
# no need to pass a value for c
```

请注意，虽然参数默认为位置参数，但一旦参数作为关键字传递，调用中其后的所有参数都需要是关键字。

不能在 positional 和 keyword 之间来回跳转。

```
def myFunction(a,b,c):
   print(a,b,c)myFunction(1,b=2,3) # will cause an error
# SyntaxError: positional argument follows keyword argument
```

这种灵活性确实会带来后果和问题。给定我们之前的代码片段，如果我们更改函数内部参数的名称，将该参数作为关键字引用的调用将会中断。

```
def myFunction(x,y,z):
   print(a,b,c)myFunction(1,2,3)       # still works with new definition
myFunction(c=3,b=2,a=1) # this is no longer valid
```

# 如何将参数定义为位置参数、关键字参数或两者都定义

从 Python 3.8 开始，现在可以显式地将参数定义为仅位置参数、关键字可选参数或仅关键字参数。

使用`/`和`*`符号，参数将基于它们相对于符号的位置而具有前述属性。

```
positional only / keyword optional * keyword only
```

通过修改我们的原始函数，我们可以看到这一点。我们将只设置`a`为位置关键字，`b`为可选关键字，而`c`为唯一关键字。

```
def myFunction(a,/,b,*,c):
   print(a,b,c)myFunction(1,2,3) 
# TypeError: myFunction() takes 2 positional arguments but 3 were givenmyFunction(a=1,b=2,c=3) 
# TypeError: myFunction() got some positional-only arguments passed as keyword arguments: 'a'# valid function calls
myFunction(1,2,c=3)
myFunction(1,b=2,c=3)
```

# 结论

如何使用仅位置参数、可选关键字参数和仅关键字参数？

根据 [PEP 570](https://www.python.org/dev/peps/pep-0570/) ，仅使用位置参数允许通过`**kwargs`使用与参数同名的参数。你怎么想呢?