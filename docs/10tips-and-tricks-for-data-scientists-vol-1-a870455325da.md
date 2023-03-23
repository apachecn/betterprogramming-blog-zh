# 数据科学家的 10 个技巧和诀窍

> 原文：<https://betterprogramming.pub/10tips-and-tricks-for-data-scientists-vol-1-a870455325da>

## R 和 Python 中有用的代码片段

![](img/6365e60a393dc59f00032e51156e45e2.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/ten?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 介绍

作为数据科学家，我们喜欢高效地完成工作，而不是重新发明轮子。提示和技巧文章提供了数据科学领域常见任务的代码片段。在本文中，我们将主要介绍 Python 和 R，以及 Unix、Excel、Git、Docker、Google 电子表格等的其他技巧。

不要错过技巧和窍门第 2 卷

[](/10-tips-and-tricks-for-data-scientists-vol-2-5640ccd1aa5e) [## 数据科学家的 10 个惊人技巧和诀窍

### 一些更有用的 R 和 Python 代码片段

better 编程. pub](/10-tips-and-tricks-for-data-scientists-vol-2-5640ccd1aa5e) 

# 计算机编程语言

## 1.如何按元素对元组列表进行排序

假设我有以下列表:

```
l **=** [(1,2), (4,6), (5,1), (1,0)]
l
```

输出:

```
[(1, 2), (4, 6), (5, 1), (1, 0)]
```

我想按照元组的第二个元素来排序:

```
sorted(l, key**=lambda** t: t[1])
```

输出:

```
[(1, 0), (5, 1), (1, 2), (4, 6)]
```

如果我想按元组的第一个元素排序:

```
sorted(l, key**=lambda** t: t[0])
```

输出:

```
[(1, 2), (1, 0), (4, 6), (5, 1)]
```

## 2.如何展平列表列表

假设我们的列表是:

```
l = [[1, 2, 3], [4, 5, 6], [7], [8, 9]]
```

我们想把它展平成一个列表。我们可以使用[列表理解方式，](https://predictivehacks.com/python-list-comprehension-cheat-sheet/)如下:

```
[item **for** sublist **in** l **for** item **in** sublist]
```

输出:

```
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## 3.列表理解中的“elif”

**场景**:您正在处理从`1`到`5`取值的产品评论，并且您想要创建三个类别:

*   **好，**如果评审大于等于`4`
*   **中立**，如果审查是一个`3`
*   **否定**，如果审查不到`3`

```
x = [1,2,3,4,5,4,3,2,1]["Good" if i>=4 else "Neutral" if i==3 else "Bad" for i in x]
```

输出:

```
['Bad', 'Bad', 'Neutral', 'Good', 'Good', 'Good', 'Neutral', 'Bad', 'Bad']
```

## 4.a 社邦线:#！/usr/bin/python3

在很多`.py`文件中，我们可能会看到脚本顶部的[射棒线](https://en.wikipedia.org/wiki/Shebang_(Unix))。其目的是定义解释器的位置。通过在脚本顶部添加行`#!/usr/bin/python3`,我们可以在 Unix 系统上运行`file.py`,它会自动理解这是一个 Python 脚本。或者，您可以作为`python3 file.py`运行脚本。例如，假设`file.py`是:

```
#!/usr/bin/python3print("Hello shebang line")
```

我们可以在 Unix 上运行:

```
$ ./file.py
```

# 稀有

## 6.在多列上用“dplyr”联接

`dplyr`允许我们在不止一列上连接两个数据框。您所要做的就是像`by = c("x1" = "x2", "y1" = "y2")`一样在`by`中添加列。例如:

```
library(dplyr)
set.seed(5)
df1 <- tibble(
    x1 = letters[1:10],
    y1 = LETTERS[11:20],
    a = rnorm(10)
)df2 <- tibble(
    x2 = letters[1:10],
    y2 = LETTERS[11:20],
    b = rnorm(10)
)df<-df1%>%inner_join(df2, df2, by = c("x1" = "x2", "y1" = "y2"))df
```

输出:

```
# A tibble: 10 x 4
   x1    y1          a      b
   <chr> <chr>   <dbl>  <dbl>
 1 a     K     -0.841   1.23 
 2 b     L      1.38   -0.802
 3 c     M     -1.26   -1.08 
 4 d     N      0.0701 -0.158
 5 e     O      1.71   -1.07 
 6 f     P     -0.603  -0.139
 7 g     Q     -0.472  -0.597
 8 h     R     -0.635  -2.18 
 9 i     S     -0.286   0.241
10 j     T      0.138  -0.259
```

## 7.如何用 for 循环在 R 中存储模型

假设我们想要为`iris`数据集中的每个`Species`运行不同的回归模型。我们可以通过两种不同的方式来实现，如下所示:

**将模型存储在列表中**

```
my_models<-list()for (s in unique(iris$Species)) {
    tmp<-iris[iris$Species==s,]
    my_models[[s]]<-lm(Sepal.Length~Sepal.Width+Petal.Length+Petal.Width, data=tmp)
}# get the 'setosa' model
my_models[['setosa']]
```

输出:

```
Call:
lm(formula = Sepal.Length ~ Sepal.Width + Petal.Length + Petal.Width, 
    data = tmp)

Coefficients:
 (Intercept)   Sepal.Width  Petal.Length   Petal.Width  
      2.3519        0.6548        0.2376        0.2521
```

**使用**T5 按名称存储型号

```
for (s in unique(iris$Species)) {
    tmp<-iris[iris$Species==s,]
    assign(s,lm(Sepal.Length~Sepal.Width+Petal.Length+Petal.Width, data=tmp))
}# get the 'setosa' model
get("setosa")
```

输出:

```
Call:
lm(formula = Sepal.Length ~ Sepal.Width + Petal.Length + Petal.Width, 
    data = tmp)

Coefficients:
 (Intercept)   Sepal.Width  Petal.Length   Petal.Width  
      2.3519        0.6548        0.2376        0.2521
```

## 8.如何将多个参数传递给' sapply '

假设我们要在 R 中运行一个`sapply`或者`lapply`，函数有多个参数。然后，我们可以定义想要应用`sapply`的参数，并为其余参数分配固定值:

```
# this is the function like a linear equation
# of the form y= a + b * x
my_func<- function(a,b,c) {
  a+b*c
}# the values of the x
x = c(1,5,10)# we set a=1 and b=2
sapply(x,my_func,a=1, b=2)
```

输出:

```
[1]  3 11 21
```

## 9.如何逐行获取最大值的列

假设我们的数据帧是:

```
set.seed(5)df<-as.data.frame(matrix(sample(1:100,12),ncol=3))
df
```

输出:

```
 V1 V2 V3
1 66 41 19
2 57 85  3
3 79 94 38
4 75 71 58
```

我们可以逐行获取 max 列的索引和名称，如下所示:

```
colnames(df)[max.col(df,ties.method="random")]
```

输出:

```
[1] "V1" "V2" "V2" "V1"
```

## 10.如何生成随机日期

我们可以使用均匀分布从特定范围的 Unix 时间戳中生成随机日期。例如，让我们生成 10 个随机日期:

```
library(lubridate)lubridate::as_datetime( runif(10, 1546290000, 1577739600))
```

输出:

```
[1] "2019-12-09 15:45:26 UTC" "2019-08-31 19:28:03 UTC" "2019-01-13 12:15:13 UTC" "2019-11-15 00:13:25 UTC"
 [5] "2019-01-19 06:31:10 UTC" "2019-11-02 12:46:34 UTC" "2019-09-04 19:16:31 UTC" "2019-07-29 11:53:43 UTC"
 [9] "2019-01-25 23:08:20 UTC" "2019-02-03 02:30:21 UTC"
```

# 想获得更多提示吗？

如需更多提示，您可以访问预测黑客的[提示](https://predictivehacks.com/tips/)