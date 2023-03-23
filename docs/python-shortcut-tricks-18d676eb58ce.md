# Python 快捷方式和技巧

> 原文：<https://betterprogramming.pub/python-shortcut-tricks-18d676eb58ce>

## 少写，多做

![](img/9111cb19e4232f744f18710bbd444006.png)

由[奥斯丁·尼尔](https://unsplash.com/@arstyy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/tricks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 列表中的出现计数器

```
from collections import Counternum_lst = [1, 1, 2, 3, 4, 5, 3, 2, 3, 4, 2, 1, 2, 3]
cnt = Counter(num_lst)
print(dict(cnt))# first 2 most occurrence
print(dict(cnt.most_common(2)))str_lst = ['blue', 'red', 'green', 'blue', 'red', 'red', 'green']
print(dict(Counter(str_lst)))
```

输出:

```
{1: 3, 2: 4, 3: 4, 4: 2, 5: 1}
{2: 4, 3: 4}
{'blue': 2, 'red': 3, 'green': 2}
```

# 强大的一行程序

```
salary = 4000

if salary > 9000:
    tax = 900
elif 3000 < salary < 9000:
    tax = 100
else:
    tax = 0

print(f"Tax to pay : ${tax}")

tax = 900 if salary > 9000 else 100 if 3000 < salary < 5000 else 0

print(f"Tax to pay : ${tax}")
```

输出:

```
Tax to pay : $100
Tax to pay : $100
```

# for 循环最小化

```
lst = [1, 2, 3]
doubled = []
for num in lst:
    doubled.append(num*2)
print(doubled)

doubled = [num*2 for num in lst]
print(doubled)
```

输出:

```
[2, 4, 6]
[2, 4, 6]
```

# 检查所有大写字符

```
import string

def is_upper(word):
    return all(c in string.ascii_uppercase for c in list(word))

print(is_upper('HUMANBEING'))
print(is_upper('humanbeing'))
```

输出:

```
True
False
```

# for 和 if 在一行中

```
d = [1, 2, 1, 3]
a = [each for each in d if each == 1]
print(a)
```

输出:

```
[1, 1]
```

# 带索引的循环

```
lst = ['a', 'b', 'c', 'd']
for index, value in enumerate(lst):
    print(f"{index}, {value}")for index, value in enumerate(lst, start=10):
    print(f"{index}, {value}")
```

输出:

```
0, a
1, b
2, c
3, d10, a
11, b
12, c
13, d
```

# 同时循环多个列表

```
colors = ["red", "green", "yellow", "blue"]
codes = [1, 2, 3, 4]
for color, code in zip(colors, codes):
    print(f"{code}, {color}")
```

输出:

```
1, red
2, green
3, yellow
4, blue
```

请注意，压缩不同大小的列表将在最短的列表用完项目后停止。

```
colors = ["red", "green", "yellow", "blue"]
codes = [1, 2, 3]
for color, code in zip(colors, codes):
    print(f"{code}, {color}")
```

输出:

```
1, red
2, green
3, yellow
```

如果你需要一种不同的行为，你可能想研究一下`itertools.zip_longest`。

```
from itertools import zip_longestcolors = ["red", "green", "yellow", "blue"]
codes = [1, 2, 3, 4, 5, 6]for color, code in zip_longest(colors, codes):
    print(f"{code}, {color}")for color, code in zip_longest(colors, codes, fillvalue='Nothing'):
    print(f"{code}, {color}")
```

输出:

```
1, red
2, green
3, yellow
4, blue
5, None
6, None1, red
2, green
3, yellow
4, blue
5, Nothing
6, Nothing
```

# 转置 2D 阵列

```
chars = [['a', 'b'], ['c', 'd'], ['e', 'f']]
transposed = zip(*chars)
print(list(transposed))chars = [['a', 'b'], ['c', 'd'], ['e', 'f', 'g', 'h']]
transposed = zip_longest(*chars)
print(list(transposed))
```

输出:

```
[(‘a’, ‘c’, ‘e’), (‘b’, ‘d’, ‘f’)]
[('a', 'c', 'e'), ('b', 'd', 'f'), (None, None, 'g'), (None, None, 'h')]
```

# 累积()

```
itertools.accumulate(iterable[, func])
```

这个函数生成一个迭代器，返回函数的结果。

函数可以像变量一样传递。`accumulate()`函数接受一个函数作为参数。它也需要一个 iterable。它返回累积的结果。

结果本身包含在一个 iterable 中。这听起来可能非常令人困惑。我向你保证，当你玩代码时，它将是有意义的。

```
import itertools
import operator
data = [1, 2, 3, 4, 5]
result = list(itertools.accumulate(data, operator.add))
print(result)
```

输出:

```
[1, 3, 6, 10, 15]
```

# 反转字符串或列表

```
a = "humanbeing"
print("Reversed : {a[::-1]}")

b = [1, 2, 3, 4, 5]
print("Reversed : {b[::-1]}")
```

输出:

```
Reversed : gniebnamuh
Reversed : [5, 4, 3, 2, 1]
```

# 将字符串和列表连接在一起

```
str1 = "do"
str2 = "more"
lst = ["Write", "less", "code"]

str3 = ' '.join(lst) + ', ' + str1 + ' ' + str2
print(str3)
```

输出:

```
Write less code, do more
```

# 将列表转换为逗号分隔的字符串

```
fruits = ['apple', 'mango', 'orange']
print(', '.join(fruits))

numbers = [1, 2, 3, 4, 5]
print(', '.join(map(str, numbers)))

items = [1, 'apple', 2, 3, 'orange']
print(', '.join(map(str, items)))
```

输出:

```
apple, mango, orange
1, 2, 3, 4, 5
1, apple, 2, 3, orange
```

# 展平嵌套列表

```
import itertools
flatten = lambda x: list(itertools.chain.from_iterable(x))s = [['Every', 'piece of'], ['software written today is', 'likely'], ['going to'], ['infringe on', "someone else’s", 'patent.']]print(' '.join(flatten(s)))
```

输出:

```
Every piece of software written today is likely going to infringe on someone else’s patent.
```

# 基于条件调用具有相同参数的不同函数

```
def product(a, b):
    return a*b

def add(a, b):
    return a+b

c = True

print((product if c else add)(5, 6))
```

输出:

```
30
```

# 找不到关键字时获取字典

```
d = {'a': 1, 'b': 2}
print(d.get('c'))
print(d.get('c', 3))
```

输出:

```
None
3
```

# 分类词典

```
from operator import itemgetter

d = {'a': 10, 'b': 20, 'c': 5, 'd': 8, 'e': 5}# sort by value
print(sorted(d.items(), key=lambda x: x[1]))# sort by value
print(sorted(d.items(), key=itemgetter(1)))# sort by key
print(sorted(d.items(), key=itemgetter(0)))# sort by value and return keys
print(sorted(d, key=d.get))
```

输出:

```
[(‘c’, 5), (‘e’, 5), (‘d’, 8), (‘a’, 10), (‘b’, 20)]
[(‘c’, 5), (‘e’, 5), (‘d’, 8), (‘a’, 10), (‘b’, 20)]
[(‘a’, 10), (‘b’, 20), (‘c’, 5), (‘d’, 8), (‘e’, 5)]
[‘c’, ‘e’, ‘d’, ‘a’, ‘b’]
```

# 从列表中删除重复项

```
lst = [7, 3, 3, 5, 6, 5]

# removes duplicates but does not preserves the list order
no_dups = list(set(lst))
print(no_dups)

# removes duplicates and preserves the list order
from collections import OrderedDict
no_dups = list(OrderedDict.fromkeys(lst).keys())
print(no_dups)
```

输出:

```
[3, 5, 6, 7]
[7, 3, 5, 6]
```

# 轻松打印

```
row = [100, "android", "ios", "blackberry"]
print(', '.join(str(x) for x in row))
print(*row, sep=', ')
```

输出:

```
100, android, ios, blackberry
100, android, ios, blackberry
```

# 交换值

```
a, b = 9, 10
a, b = b, a
print(a, b)
```

输出:

```
10, 9
```

# 最小/最大元素的索引

```
def min_index(lst):
    return min(range(len(lst)), key=lst.__getitem__)

def max_index(lst):
    return max(range(len(lst)), key=lst.__getitem__)

lst = [20, 40, 70, 10]
print("min index : {}".format(min_index(lst)))
print("max index : {}".format(max_index(lst)))
```

输出:

```
min index : 3
max index : 2
```

# for 和 else

```
a = [1, 2, 3, 4, 5]# else gets called only when for loop does not reach break statement
for each in a:
    if each == 0:
        break
else:
    print('break statement did not executed')
```

输出:

```
break statement did not executed
```

# 检查字谜

```
from collections import Counter

print(Counter('elbow') == Counter('below'))
print(Counter('study') == Counter('night'))
```

输出:

```
True
False
```

# 合并词典

```
d1 = {'a': 1}
d2 = {'b': 2}print(dict(d1.items() | d2.items()))
print({**d1, **d2})d1.update(d2)
print(d1)
```

输出:

```
{‘a’: 1, ‘b’: 2}
{‘a’: 1, ‘b’: 2}
{‘a’: 1, ‘b’: 2}
```