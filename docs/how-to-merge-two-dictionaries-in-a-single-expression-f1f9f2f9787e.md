# 如何在 Python 中将两个字典合并成一个表达式

> 原文：<https://betterprogramming.pub/how-to-merge-two-dictionaries-in-a-single-expression-f1f9f2f9787e>

## 将两本词典合并成一本新的

![](img/0135f5f4bb8caa72e4cb86861baf6f82.png)

[Bangkit Ristant](https://unsplash.com/@bangkitristant?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/merge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 挑战

使用两个字典，创建第三个字典，它是最初两个字典的合并。第一个字典将被视为基本字典，第二个字典中的重复键将覆盖第一个字典。解决方案将是浅层合并，这意味着嵌套字典将*而不是*随后被合并。

# 解决办法

通过定义我们自己的函数，我们满足了在一个表达式中合并两个字典的要求。该功能将是一个两步过程:制作第一个字典的副本，然后向其中添加第二个字典的术语。

```
def merge_dictionaries(first_dict, second_dict):
   merged = first_dict.copy()
   merged.update(second_dict)
   return mergedd1 = { "A": "Auron", "B": "Braska", "C": "Crusaders" }
d2 = { "C": "Cid", "D": "Dona" }print(merge_dictionaries(d1,d2))
# {'A': 'Auron', 'B': 'Braska', 'C': 'Cid', 'D': 'Dona'}
```

# 荣誉奖

## **使用(**)运算符**

从 Python 3.5 开始，双星号可以用来解包字典。利用字典理解和解包操作符，我们可以在一个表达式中合并两个字典。

```
d1 = { "A": "Auron", "B": "Braska", "C": "Crusaders" }
d2 = { "C": "Cid", "D": "Dona" }
d3 = {**d1, **d2}print(d3)
# {'A': 'Auron', 'B': 'Braska', 'C': 'Cid', 'D': 'Dona'}
```

## **使用(+)运算符**

如果您仍在使用 Python 2.7，那么使用加号来组合两个字典中的条目会产生一个非常易于阅读的单一表达式解决方案。然而，这在 Python 3 中不起作用。

```
d1 = { "A": "Auron", "B": "Braska", "C": "Crusaders" }
d2 = { "C": "Cid", "D": "Dona" }
d3 = dict(d1.items(), d2.items())print(d3)
# {'A': 'Auron', 'B': 'Braska', 'C': 'Cid', 'D': 'Dona'}
```