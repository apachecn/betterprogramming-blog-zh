# R 程序设计回顾一年

> 原文：<https://betterprogramming.pub/a-year-in-review-of-r-programming-18c26aae6631>

## 2019 年最大的 R 编程趋势

![](img/d312a74197fcc04fabb467d66fd8d927.png)

# 介绍

2019 年对许多领域来说都是令人兴奋的一年，如数据科学、机器学习和物联网。今年我们看到了许多创新——不仅如此，科技行业在道德方面也取得了很大进步，这可以从小型项目的推广、为促进易用性的举措提供的资助以及科技行业女性人数的增加中看出来。

和往常一样，R 编程的世界不会被抛在后面。R 社区一直是领先创新者的聚集地。2019 年对于 R 社区来说没有任何不同。充满了创新、改进和挑战，R 度过了梦幻般的一年，并准备好迎接更多的挑战。

是时候和 T2 一起成为明星了。以下是 2019 年 R 编程的 10 大亮点。

# r . 2019 年的节目趋势

## 1.统计机器学习

R community 并不是第一个进入机器学习领域的，但这并没有阻止他们尽最大努力在数据科学的所有领域保持前沿，包括机器学习。r 今年在统计机器学习方面取得了显著的进步。随着 Sarah Romanes 对高维和复杂数据的研究以及她的软件包 multiDA 和 genDA，R 在生物信息学和图像分析方面获得了许多新的应用。

## 2.改进的数据处理

2019 年还看到了针对充满双精度类型值的数据的`data.table`包的完全优化。此外，随着 vroom 包的引入，处理充满字符的数据集变得前所未有的简单。

## 3.傀儡套装

r 有能力用它闪亮的软件包制作交互式网络应用。但是这个软件包需要一些实践和大量的试验和错误，才能产生干净和完整的应用程序。今年我们看到了文森特·古亚德及其团队的 golem 包。该软件包使得创建生产就绪的闪亮应用程序变得轻而易举。

## 4.mlr3

mlr 是 R 中最流行的机器学习包，这是由于 mlr 是一个完整的机器学习包，可以实现 R 编程中所有的机器学习算法和模型。mlr 的最新更新 mlr3 是今年发布的。随着新框架的出现，机器学习能力也有了显著的提高。

```
library(mlr3)# create learning tasktask_iris = TaskClassif$new(id = "iris", backend = iris, target = "Species")task_iris<TaskClassif:iris> (150 x 5)* Target: Species* Properties: multiclass* Features (4):- dbl (4): Petal.Length, Petal.Width, Sepal.Length, Sepal.Width# load learner and set hyperparameterlearner = lrn("classif.rpart", cp = 0.01)
```

## 5.数据可视化

r 编程语言过去是，现在仍然是数据可视化的大师。这种趋势今年仍然保持着，因为各种扩展 R 图形库的新包已经发布。还添加了改进 ggplot 包提供的功能的包。最吸引人的是 barplot3d 软件包，它能够生成 3d 条形图。

## 6.测试和项目设置自动化

这个软件包在 R 社区引起了巨大的反响。这个包自动化了代码测试和发布新的 R 包和项目的过程。

```
> library(usethis)> path<-file.path(tempdir(),"mypkg")> create_package(path)✔ Creating '/tmp/Rtmpq2TjGl/mypkg/'✔ Setting active project to '/tmp/Rtmpq2TjGl/mypkg'✔ Creating 'R/'✔ Writing 'DESCRIPTION'Package: mypkgTitle: What the Package Does (One Line, Title Case)Version: 0.0.0.9000Authors@R (parsed):* First Last <first.last@example.com> [aut, cre] (YOUR-ORCID-ID)Description: What the package does (one paragraph).License: What license it usesEncoding: UTF-8LazyData: true✔ Writing 'NAMESPACE'✔ Writing 'mypkg.Rproj'✔ Adding '.Rproj.user' to '.gitignore'✔ Adding '^mypkg\\.Rproj$', '^\\.Rproj\\.user$' to '.Rbuildignore'✔ Opening '/tmp/Rtmpq2TjGl/mypkg/' in new RStudio session✔ Setting active project to '<no active project>'
```

## 7.克兰利

到目前为止，在 CRAN 上寻找新的和合适的 R 包一直是一个具有挑战性的过程。不再是了！有了新的 Cranly 包，探索 CRAN 库变得前所未有的简单。

## 8.更好的数据处理

r 处理机器内存中的数据。这是 R 编程语言的一大局限。有几个包试图弥补 r 的这一缺点。disk.frame 包是一个很有前途的包，它可以操作比系统 RAM 大的数据。

## 9.r 会议

R 社区还组织了几次会议和社交聚会，让用户聚在一起，分享他们对 R 编程未来的看法和想法。像用户这样的事件！2019，庆典 2019，为什么是 R？2019，在世界各地组织了更多的活动，讨论 R 编程涉及的每个领域的新应用和创新。

## 10.道德成长

R 社区还采取了多种措施来促进道德和社区发展，采取多种举措来赞助小型项目、环境项目、出版有助于处理环境数据的软件包，以及增加和促进妇女在技术行业的发展。

# 包装它

2019 年对于 R 社区来说是很神奇的一年。由于几个天才程序员、数据科学家和爱好者的辛勤工作，他们不知疲倦地努力使 R 更好、更强大、更易于使用。R 的最大优势之一是庞大的、创新的、非常有用的社区。r 公司今年的进展是他们独创性和创新的一个例子。

在成功的 2019 年之后，我们相信 R 社区会有一个更加惊人的 2020 年。

# 资源

一张[小抄](https://docs.google.com/spreadsheets/d/1eNBLcKqCVN9zZQvfGUmm5bAzsETqB_ugVOlUtmvJGYU/edit#gid=1566091853)，这是对所有 R 程序员的款待。