# 招聘数据工程师的 3 项读写能力测试

> 原文：<https://betterprogramming.pub/3-literacy-tests-for-hiring-data-engineers-2d700075143f>

## 算术、SQL 知识等等

![](img/a0a0ecccff0ee46b81119c259c5edc5a.png)

乔恩·泰森在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

工程经理不断面临寻找一致的方法来雇佣最好的数据工程师的挑战。

对申请者进行一套一致的测试有助于团队在三个关键技能集上进行公平一致的测试:写作能力、计算能力和 SQL 读写能力。

这篇短文提供了一些测试示例，用于筛选数据工程候选人的关键技能。

# 读写能力——求职信

求职信是测试书面读写能力的绝佳方式。"告诉你关于你自己和你为什么想要在这里工作."这是一个简单的作业，它会把会写字的人和不会写字的人分开。

远程构建团队需要团队中的人写得好，在文档中用文字表达自己的想法。

永远不要雇佣不会或不愿写求职信的人。

# 算术——你理解数字 1000 吗？

算术就是数字素养。申请人理解数字吗？数据都是数字，要看他们知不知道。数字 1000 是发现候选人是否理解数字的绝佳方式。

## *1000 * 1000 是什么？*

为什么问这个问题？一千乘以一千就是一百万。一个懂数字的人不用计算就能脱口而出地回答这个问题。数字素养是关于知道数字意味着什么。弄清楚候选人是否对一千这个数字的绝对大小有直观的理解。

## 1000 天是多少年？

时间计算是数据工程师的日常工作。看考生能不能意识到一年有 365 天，除以 1000 得到 2.7 年。日期数学是数据分析的基础。

## *什么是 1000 * 1000？*

先说一百万吧。候选人直观理解一百万吗？基于 1000x1000 的问题，问他们一百万天有多少年？建立在以前的概念上。一百万天大约是 2700 年。算术是关于理解一千、一百万、十亿、一万亿等的相对大小。

为了更深入，通过询问日志来更深入地理解一百万个概念。如果候选人理解我们为什么使用树形数据结构，就没有必要让他们通过描述二叉树或快速排序来重复算法。

## *什么是 log(百万)？*

一个优秀的候选人会有一个关于日志的想法。确切的答案没有一个大概的答案那么重要。如果他们说是 6，那么他们会识别 log_10 结果。如果他们说大约 10 (13 和一些变化)，他们指的是 log_2 结果。

在日志问题之后提出一个哲学问题，看看他们是否理解为什么在分析数据时日志是必不可少的。

*我们在数据处理中会遇到哪几种 n*log(n)算法？*

一个优秀的数据工程师会有一个迷人的答案。

# 测试 SQL 读写能力

SQL 是数据的语言。要测试 SQL 读写能力，请给申请人一组要回答的问题，并让他们编写 SQL。在他们完成所有问题后，检查他们的答案。甚至合理的做法是给他们一些时间来处理面试中的粗糙工作，然后自己花一些时间来完善解决方案。坚持完善问题的候选人表现出他们对这份工作的渴望。

用一些数据仓库理论让他们兴奋起来。一点点将让你衡量他们的数据复杂性。

*星型模式和雪花型模式有什么区别？*

他们应该知道星型模式包含一组围绕事实的维度，雪花型模式是包含多个事件的规范化版本。

*什么是缓变维度？*

同样，这是每个数据工程师都应该牢记的一个重要概念。它们应该能够描述`type 1`和`type 2`缓慢变化的尺寸。

为他们建立一个模式。我们有一个简单的星型模式，包含 3 个表，`CUSTOMER_1`、`CUSTOMER_2`和`PURCHASES`。`CUSTOMER_1`和`CUSTOMER_2`有 email 的主键，`PURCHASES`是购物清单，有购物的金额和时间戳。

给他们一个问题，看看他们是否知道如何在数据表中找到丢失的行。

## *如何编写查询来输出两个客户表中任何一个的缺失记录？*

```
select c1.email
    , c2.email
from customer_1 c1
full outer join customer_2 c2 on c1.email = c2.email
where c1.email is null or c2.email is null;
```

如果他们使用“逗号第一”的符号，那就是双倍的荣誉。这表明他们习惯于处理大量的列，并在分析时进行注释或取消注释。

## *如何统计 CUSTOMER_2 表缺失记录的相异数？*

```
select count(distinct c1.email)
from customer_1 c1
full outer join customer_2 c2 on c1.email = c2.email
where c2.email is null;
```

## *我如何获得 CUSTOMER_1 和 CUSTOMER_2 中所有客户的不同列表？奖金，如果你用 CTE 得到名单。*

```
with all_emails as (
    select distinct coalesce(c1.email, c2.email) as email
    from customer_1 c1
    full outer join customer_2 c2 on c1.email = c2.email
)
select * from all_emails;
```

还有其他的方法。

这里有一个更难的问题，通过测试是否了解窗口功能来找到顶级申请者。

我下面的解决方案使用 windows 功能，但要开放给候选人的创造力。

*假设我想向年度最佳购买者和任何未购买的人发送优惠券。我如何使用所有电子邮件的列表来获得顶级购买者和任何没有购买任何东西的购买者？*

```
with all_emails as (
    select distinct coalesce(c1.email, c2.email) as email
    from customer_1 c1
    full outer join customer_2 c2 on c1.email = c2.email
)
select rank() over (order by sum(coalesce(
    case when PURCHASE_DATE > dateadd(year, -1, current_timestamp) then amount
        else null end, 0)) desc) top_purchaser
     , sum(case when PURCHASE_DATE > dateadd(year, -1, current_timestamp) then amount else 0 end) yearly_amount
    , ae.email
from all_emails ae
 left outer join purchases p on p.email = ae.email
group by ae.email
qualify top_purchaser = 1 or yearly_amount = 0;
```

# 结论

纪律严明的招聘流程有助于带来一致的结果。这是一个团队做的最重要的事情。一份标准化的招聘清单创造了一个公平的过程，让新员工在面试时就开始融入团队。当团队成员很好地了解新人知道什么和不知道什么时，就会建立起期望和纽带，这有利于伟大的团队。

## 附录

SQL 问题的解决方案在这里:[https://github . com/timowlmtn/bigdataplatforms/tree/master/data/SQL _ test](https://github.com/timowlmtn/bigdataplatforms/tree/master/data/sql_test)