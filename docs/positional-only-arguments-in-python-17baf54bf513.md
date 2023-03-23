# Python 中的仅位置参数

> 原文：<https://betterprogramming.pub/positional-only-arguments-in-python-17baf54bf513>

## Python 中的最新特性之一

![](img/32dafa52ac674adc165f048ca29f9b64.png)

有了这个特性，你的代码将变得有条理和简单，就像这张图片一样。(图片:[伊兰尼特·科皮斯](https://www.pexels.com/photo/three-white-ceramic-pots-with-green-leaf-plants-near-open-notebook-with-click-pen-on-top-796602/))

Python 版包含了使某些参数仅具有位置性的能力。这意味着您可以使这些参数不能与关键字一起使用。

例如，`len`函数不再接受关键字参数`obj`，它是要检查的对象。所以，这不再管用了:

```
>>> items = [1, 2, 3, 4]
>>> print(len(obj=items))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: len() takes no keyword arguments
```

相反，您需要这样做:

```
>>> items = [1, 2, 3, 4]
>>> print(len(items))
4
```

这个改动最初是在 [PEP 570](https://www.python.org/dev/peps/pep-0570/) 中提出的，你可以马上使用。

这可以使用新的`/`语法来完成，就像这样:

```
def func(a, b, /, c):
    print(a, b, c)
```

这里，你只能给出`a`和`b`作为位置论元。可以作为位置参数或关键字参数给出。如果您试图将`a`或`b`作为关键字参数，您将得到如下错误:

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: func() got some positional-only arguments passed as keyword arguments: 'a, b'
```

# 结论

这种改变可以使你的代码更易读，因为它要求你使你的代码更干净、更简洁。

Python 3.8 中还有一些其他的新特性，比如 walrus 操作符(参见[Jonathan Hsu](https://medium.com/better-programming/what-is-the-walrus-operator-in-python-5846eaeb9d95)撰写的本文)和 f 字符串的`=`说明符。

只有位置的参数简单但有用，Python 也应该如此。