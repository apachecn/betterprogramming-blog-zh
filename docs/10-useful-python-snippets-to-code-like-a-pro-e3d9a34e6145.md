# 10 个有用的 Python 片段，让你像专业人士一样编写代码

> 原文：<https://betterprogramming.pub/10-useful-python-snippets-to-code-like-a-pro-e3d9a34e6145>

## 我每天使用的有用的提示和技巧

![](img/55614a036ebcbf23002eb03e284ef9b2.png)

由 [Murat Onder](https://unsplash.com/@muratodr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我收集了一份使用 Python 时我喜欢使用的有用技巧和“窍门”的列表。

请随意将这个列表加入书签，并在以后参考它，因为很难只看一眼就记住所有的东西。

这个列表没有特定的顺序。

# 1.用一行代码交换两个变量

你能想出一个不用第三个临时变量就能交换两个变量的方法吗？嗯，这就是:

```
a = 1
b = 2a, b = b, a
```

# 2.不循环地复制字符串

```
name = "Banana"
print(name * 4)
```

输出:

```
BananaBananaBananaBanana
```

# 3.反转一根绳子

```
sentence = "This is just a test"
reversed = sentence[::-1]print(reversed)
```

输出:

```
tset a tsuj si sihT
```

# 4.将字符串列表压缩成一个字符串

您可以使用内置的`[join()](https://www.codingem.com/python-all-string-methods/#:~:text=False.-,join,-To)`方法轻松连接字符串列表。

```
words = ["This", "is", "a", "Test"]
combined = " ".join(words)print(combined)
```

输出:

```
This is a Test
```

请随意查看 Python 中更有用的[字符串方法来拯救你的一天。](https://www.codingem.com/python-all-string-methods)

# 5.比较链

在 Python 中，您可以将比较巧妙地结合在一起，而不是将语句分成两个或更多部分。例如:

```
x = 100
res = 0 < x < 1000print(res)
```

输出:

```
**True** 
```

# 6.查找列表中最频繁出现的元素

```
test = [6, 2, 2, 3, 4, 2, 2, 90, 2, 41]
most_frequent = max(set(test), key = test.count)print(most_frequent)
```

输出:

```
2
```

# 7.将列表解包为单独的变量

只要变量的数量与列表中元素的数量相同，您就可以将一个元素列表打包成单独的变量。

例如:

```
arr = [1, 2, 3]
a,b,c = arrprint(a, b, c)
```

输出:

```
1 2 3
```

# 8.一行 If-Else 语句

在 Python 中，单行`if-else`语句被称为三元运算符或[条件运算符](https://www.codingem.com/python-one-line-if-else/)。

例如:

```
age = 30
age_group = "Adult" if age > 18 else "Child"print(age_group)
```

输出:

```
Adult
```

通常，为了方便起见，条件运算符遵循以下模式:

```
true_expression **if** condition **else** false_expression
```

# 9.用一行代码遍历一个列表

您可以使用 *comprehensions* 用一行代码遍历一个列表。例如，让我们将列表中的每个数字提升到第二个[次方](https://www.codingem.com/python-exponent-maths/):

```
numbers = [1, 2, 3, 4, 5]
squared_numbers = [num * num **for** num **in** numbers]print(squared_numbers)
```

输出:

```
[1, 4, 9, 16, 25]
```

注意:理解不仅仅局限于使用列表。您可以以类似的一行程序方式使用综合，包括 [*字典*](https://medium.com/codex/python-make-your-code-fly-with-comprehensions-147873eea0ef#5b3b) *，* [*集合*](https://medium.com/codex/python-make-your-code-fly-with-comprehensions-147873eea0ef#dcdb) *，以及* [*生成器*](https://medium.com/codex/python-make-your-code-fly-with-comprehensions-147873eea0ef#54aa) *。*

让我们看另一个例子，通过使用*字典理解*到[提升](https://www.codingem.com/python-exponent-maths/)字典的值到二次幂:

```
dict1 = {'a': 1, 'b': 2, 'c': 3, 'd': 4}squared_dict = {key: num * num **for** (key, num) **in** dict1.items()}
print(squared_dict)
```

输出:

```
{'a': 1, 'b': 4, 'c': 9, 'd': 16}
```

点击查看更多实用的循环示例[。](https://www.codingem.com/for-loop-examples-in-python/)

# 10.简化 If 语句

而不是这种可怕的混乱:

```
**if** n == 0 **or** n == 1 **or** n == 2 **or** n == 3 **or** n == 4 **or** n == 5:
```

您可以简单地这样做:

```
**if** n **in** [0, 1, 2, 3, 4, 5]
```

# 结论

感谢阅读。欢迎在评论区分享你最喜欢的技巧。

我很想加入你的 LinkedIn 网络。随意连接 [Artturi Jalli](https://www.linkedin.com/in/artturi-jalli-29619413a) 。

# 你可能会发现见解深刻

[](/50-python-interview-questions-and-answers-5230fe2a0db6) [## 50 Python 面试问答

### Ace 您的下一次编码面试

better 编程. pub](/50-python-interview-questions-and-answers-5230fe2a0db6) 

# 资源

 [## 文件

### 欢迎光临！这是 Python 3.9.4 的文档。

docs.python.org](https://docs.python.org/3/)