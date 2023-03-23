# 如何删除列表中重复的词典

> 原文：<https://betterprogramming.pub/how-to-remove-duplicate-dictionaries-in-a-list-aaa05b506c1d>

## 创建一个函数从列表中删除重复的词典。

![](img/166280ac069fd3233cbd5a5af674bea6.png)

[维克多](https://unsplash.com/@victor_g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/duplicates?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 挑战

创建一个函数从列表中删除重复的词典。如果字典具有完全相同的键和值，则认为它是重复的。该函数应该支持深度比较(嵌套字典)。

# 解决方案

使用 list comprehension 和`json`库对每个字典和`set`数据类型进行排序和字符串化，以确保值的唯一性。

```
import jsondef remove_dupe_dicts(l):
  list_of_strings = [
    json.dumps(d, sort_keys=True)
    for d in l
  ] list_of_strings = set(list_of_strings) return [
    json.loads(s)
    for s in list_of_strings
  ]
```

为了测试我们的函数，我们传入了一些样本数据，这些样本数据包括嵌套字典，它们具有相似和不相似的嵌套键。

```
data = [
  {"id": 1, "sub": {"id": 1}},
  {"id": 1, "sub": {"id": 2}},
  {"id": 4},
  {"id": 1, "sub": {"id": 1}}
]print(remove_dupe_dicts(data))
"""
[
  {'id': 1, 'sub': {'id': 1}},
  {'id': 4},
  {'id': 1, 'sub': {'id': 2}}
]
"""
```

# 荣誉奖

**将字典转换成元组:**如果您不想使用`json`库，因为您的字典不是有效的 JSON，那么将字典列表转换成元组列表是一个可行的选择。请记住，强制对嵌套的数据结构不起作用。

```
def remove_dupe_dicts(l):
  return [
    dict(t) 
    for t in {
      tuple(d.items())
      for d in l
    }
  ]
```