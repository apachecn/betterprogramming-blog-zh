# R 中按组欠采样

> 原文：<https://betterprogramming.pub/undersampling-by-groups-in-r-7afb74a34612>

## 处理不平衡数据时如何应用欠采样

![](img/bcfbeb4a72fae9ebfae9c6458b65561e.png)

图片由 [Unsplash](https://images.unsplash.com/photo-1505678261036-a3fcc5e884ee?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=500&q=60) 提供

在处理机器学习项目中的不平衡类时，有许多方法可以遵循。仅仅是其中的一些:

*   **欠采样**:我们试图减少多数类的观测值，以使最终数据集达到平衡。
*   **过采样**:我们通常通过复制少数类中的样本来尝试从少数类中生成更多的观察值，从而使最终数据集达到平衡。
*   **合成数据生成** (SMOTE):我们使用自举和 k-最近邻算法生成人工数据。

# 生成不平衡数据

我们正在处理的场景涉及三个具有不同点击率的电子邮件活动。我们希望应用欠采样来标准化活动的 CTR，以避免在构建机器学习模型时出现任何偏斜和偏差。假设数据集如下:

*   **活动 A** : 5000 次观察，10%点击率(近似值)
*   **活动 B** : 10000 次观察，20%点击率(近似值)
*   **活动 C** : 1000 次观察，30% CTR(近似值)

让我们试着在 R 中生成这个随机样本:

```
library(tidyverse)set.seed(5)
df = rbind(data.frame(Campaign = "A", Click = rbinom(n=5000, size=1, prob=0.1)),
           data.frame(Campaign = "B", Click = rbinom(n=10000, size=1, prob=0.2)),
           data.frame(Campaign = "C", Click = rbinom(n=1000, size=1, prob=0.3))) head(df)
```

**输出:**

```
Campaign Click
1        A     0
2        A     0
3        A     1
4        A     0
5        A     0
6        A     0
```

让我们按活动获取点击率:

```
df%>%group_by(Campaign)%>% summarise(CTR=mean(Click))
```

**输出:**

```
 Campaign   CTR
  <chr>    <dbl>
1 A        0.106
2 B        0.198
3 C        0.302
```

我们可以看到，A 广告系列的点击率为 10.6%，B 为 19.8%，C 为 30.2%。让我们添加一个名为 attribute 的随机列，它取值`X`、`Y`和`Z`，因为我们将处理包含两列以上的数据集:

```
df$Attribute<-sample(c("X","Y", "Z"), size = dim(df)[1], replace = TRUE, prob = c(0.2, 0.6, 0.2)) head(df)Campaign Click Attribute
1        A     0         Y
2        A     0         Y
3        A     1         Z
4        A     0         Y
5        A     0         Z
6        A     0         Z
```

现在，我们的目标是应用欠采样，使每个活动有大约 50%的点击率。

# 按组欠采样

我们将使用`purrr`包中的`map2`函数，它属于`tidyverse`家族:

```
campaign_summary <- df %>% group_by(Campaign)%>% summarize(rr=sum(Click)/n(), pos= sum(Click))df_neg_sample<- df %>% filter(Click==0) %>%
  group_by(Campaign) %>% 
  nest() %>%             #group all data by campaign name
  ungroup() %>% 
  inner_join(campaign_summary, by="Campaign")sampled_df_neg<-df_neg_sample %>%
  mutate(samp = map2(data, pos, sample_n, replace = FALSE))  %>%# sample based on the campaing summary
  select(-data) %>%  #remove original nested data
  unnest(samp) %>% select(c(-"rr",-"pos"))df_pos <- df %>% filter(Click==1) #positive samples
new_df <- rbind(df_pos,sampled_df_neg) #balanced set positive negative within each campaignhead(new_df)Campaign Click Attribute
1        A     1         Z
2        A     1         Y
3        A     1         Y
4        A     1         Y
5        A     1         Y
6        A     1         Ytail(new_df)Campaign Click Attribute
5613        C     0         Y
5614        C     0         Y
5615        C     0         Z
5616        C     0         Y
5617        C     0         Y
5618        C     0         X
```

让我们检查一下`new_df`是否被战役平衡了。我们将按活动分组，并展示点击率和观察次数:

```
new_df%>%group_by(Campaign)%>%summarise(CTR=mean(Click), Observations=n())Campaign   CTR Observations
  <chr>    <dbl>        <int>
1 A          0.5         1064
2 B          0.5         3950
3 C          0.5          604
```

如你所见，我们牺牲了一个样本，但是我们为每个活动提供了平衡数量的类(50-50)。

# 使用 ROSE 软件包按组进行欠采样

我们也可以使用玫瑰套装。下面我们将按活动应用一个`for`循环，这样我们可以使用欠采样技术得到一个平衡的样本:

```
library(ROSE)balanced_sample = NULLfor (c in unique(df$Campaign)) {
  tmp_df = df%>%filter(Campaign==c)
  tmp<-ovun.sample(Click ~ ., data = tmp_df, method = "under", p = 0.5, seed = 5)$data
  balanced_sample<-rbind(balanced_sample, tmp)
 }
```

让我们看看`balanced_sample`实际上是否平衡。

```
balanced_sample%>%group_by(Campaign)%>%summarise(CTR=mean(Click), Observations=n())
```

**输出:**

```
Campaign   CTR Observations
  <chr>    <dbl>        <int>
1 A        0.504         1056
2 B        0.496         3978
3 C        0.510          592
```

厉害！我们已经展示了按组应用欠采样的两种不同方法。

最初由[预测黑客](https://predictivehacks.com/undersampling-by-groups-in-r/)发布