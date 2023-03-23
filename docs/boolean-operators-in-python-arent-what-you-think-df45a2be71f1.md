# Python 中的布尔运算符不是你所想的那样

> 原文：<https://betterprogramming.pub/boolean-operators-in-python-arent-what-you-think-df45a2be71f1>

## 这些小家伙并不像看上去那么简单

![](img/87450c78d27b90a859d663935b989c45.png)

照片由[米切尔罗](https://unsplash.com/@mitchel3uo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我认为自己是一个不错的 Python 程序员。我过去曾广泛使用过这种语言，它是我使用起来最舒服的一种语言。

但是，即使在我们舒适区的舒适范围内，时不时地，我们都会遇到令人兴奋的不熟悉的“哼”的时刻。最近，我有一个这样的时刻，它来自一个你可能最意想不到的地方:古老的布尔运算符。

你看，在那之前，我一直认为表达式`True and False`、`"abc" and ""`和`None or 0`的值都是`False`，因为 Python 将`False`、`None`、空字符串、所有类型的数字 0 以及[其他值](https://docs.python.org/3/reference/expressions.html#boolean-operations)解释为 false。

为什么我不会这么想呢？毕竟，我们一直在使用`if`语句，他们从未失望过。

```
if 1 and 0:
    print("You will never see this...")
if 1 or 0:
    print("But this you will see!")
```

很明显，`1 and 0`相当于`False`，`1 or 0`相当于`True`，对吧？

但是这里有一个问题:Python 将某些非布尔值(如`1`或`""`)解释为`True`或`False`，当它们出现在布尔上下文中时(例如，当与布尔操作符结合使用时),这一事实与所述上下文的返回值无关。换句话说，`val = True and 1`评估的是什么？

结果是布尔运算返回最后一个求值的参数的值。在`and`的情况下，一旦遇到非真值(在`or`的情况下为真),表达式[就会短路](https://en.m.wikipedia.org/wiki/Short-circuit_evaluation)。否则，需要检查所有参数的真值。

所以在上面的表达式中，`1`将是最后一个被求值的参数，因此`val`将得到值`1`。

这里还有一些例子:

```
val = False and True # val = False
val = "abc" and "" # val = ""
val = "" or "abc" # val = "abc"
val = None or 0 # val = 0
```

# 这为什么有用？

知道了这一点，例如，可以利用这一行为在赋值期间执行简洁的空值检查:

另一种方法是明确检查`person`是否是`None`:

```
age = person.age if person else None
```

# 不是 Python 独有的

事实上，Python 并不是唯一有这种行为的编程语言，一些 Redditors 很快指出了这一点。特别是 JavaScript，行为类似，甚至更进一步，提供了一个[可选链接](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)操作符(`?.`)。

假设我们已经将`Person`类移植到 JavaScript，这个操作符将允许我们执行:

```
person = new Person(18)
age = person?.age // age = 18person = undefined
age = person?.age // age = undefinedname = person?.name ?? "John Doe" // name = "John Doe" 
```

此外，可以使用`??`指定回退值。

*注意:虽然根对象(在我们的例子中*、`*person*` *)可以是* `*None*` *(Python)或者*`*undefined*`*/*`*null*`*(JavaScript)，但是必须声明。*

# 结论

我希望这个小花絮有用。分享你的想法，如果有任何问题，一定要让我知道。

# 资源

*   [布尔运算| Python 文档](https://docs.python.org/3/reference/expressions.html#boolean-operations)
*   [短路评估|维基百科](https://en.m.wikipedia.org/wiki/Short-circuit_evaluation)
*   [Python 的布尔运算符不返回布尔| Reddit](https://www.reddit.com/r/ProgrammerTIL/comments/n6y76l/pythons_boolean_operators_do_not_return_boolean/)
*   [可选链接(？。)| MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)