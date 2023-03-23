# 竞争编程导论

> 原文：<https://betterprogramming.pub/an-introduction-to-competitive-programming-e6e8950a7f4f>

## 这是什么——你为什么要考虑这么做？

![](img/59f72c33b523c7a9eb46d8706cba8e6b.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[émile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)拍摄

# 什么是竞争性编程？

竞争性编程是一种艺术形式。这是创造性解决问题的最佳方式，是艰难的分析思维和创造力的结合。

有竞争力的程序员利用他们的算法和数据结构知识以及逻辑推理技能，在有限的时间框架内解决具有挑战性的算法问题。

与 Python 这样的语言相比，Java 和 C++的运行效率相对较高，因此非常受欢迎。C++是我首选的竞争性编程语言，因为我喜欢它的标准模板库(STL ),它允许快速编写解决方案。

事不宜迟，让我们开始吧。

# 如何开始

## 学习 C++

我知道大多数人可能不想听到这些，但是正如我已经提到的，Python 将很难在竞争中取得成功。C++作为一门竞争性编程的语言很容易掌握，但是像任何语言一样，理解其中的细微差别需要更多的时间。这里有一些我觉得有用的资源(包括我制作的一个 youtube 视频):

*   [本齐的 GitHub 资源库](https://github.com/bqi343/USACO)
*   [《竞争性程序员手册》](https://cses.fi/book/book.pdf)
*   [“算法求解原理](http://www.csc.kth.se/~jsannemo/slask/main.pdf)”

## 学习算法分析

算法分析可以让你看到你想出的解决方案，看看它是否能赶上比赛，或者是否会超过在线裁判规定的时间限制。

时间复杂度分析，顾名思义，量化算法运行所花费的时间。这就是著名的 *O(n)* 符号的来源。这被称为*大 O 符号，*，它产生最坏情况下的运行时间——这是算法运行所需的最长时间。 *O(n)，*则表示如果有 *n* 个元素要执行运算，则算法将在与 *n* 个元素成比例的时间内运行。

同样， *O(n )* 与 *n* 成正比， *O(log n)* 与 *log(n)* 成正比。还有其他类型的时间复杂度分析也很方便。这里有一个关于算法分析的资源:

*   [“算法分析| Big-O 分析”](https://www.geeksforgeeks.org/analysis-algorithms-big-o-analysis/)

## 学习如何暴力解决问题

在一个人的竞争性编程之旅的开始，您经常会遇到一些问题，这些问题可以用简单的算法强行解决，并且不需要优化来解决。在这种情况下，您通常可以一步一步地编写解决方案，而不是应用特定的算法。

要想擅长蛮力问题，你真正要做的就是练习。我建议在 [Codeforces](http://codeforces.com/) 上练习 1000 道题。Codeforces 是一个很棒的网站，可以在上面练习你的竞争性编程技能。

## 学习贪婪算法

贪婪算法在算法的每个阶段做出局部最优选择，希望找到全局最优。

只要你能想出一个贪婪的解决方案，这些通常比蛮力解决方案更有效。

它们并不适合每一种类型的问题，如果你把它们用在不该用的地方，它们可能会变得低效。这里有一些关于贪婪算法的重要信息:

*   [辉煌的《贪婪算法》页面](https://brilliant.org/wiki/greedy-algorithm/)

## 学习动态编程

动态规划是对普通递归的优化。

本质上，它包括解决子问题并保存这些解决方案，这样你就不必像普通递归那样在以后解决它们。

这大大降低了时间复杂度，对递归类型的问题很有帮助。再说一遍，擅长这个的最好方法是解决多个动态编程问题。

*   [教育 DP 大赛](https://atcoder.jp/contests/dp)
*   [极客为极客的“动态编程”页面](https://www.geeksforgeeks.org/dynamic-programming/)

## 学习图形算法

一旦你进入编程竞赛的上层，你会发现大量的图形算法问题。这将包括像在图上寻找两个节点之间的最短路径这样的事情。

为了成功解决这类问题，你需要掌握相当多的算法。

*   [深度优先搜索](https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/)
*   [广度优先搜索(BFS)](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/)
*   [Dijkstra 算法(用于寻找最短路径)](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/)
*   [贝尔曼-福特(用于寻找最短路径)](https://www.geeksforgeeks.org/bellman-ford-algorithm-dp-23/)
*   [弗洛伊德-沃肖尔(用于寻找最短路径)](https://www.geeksforgeeks.org/floyd-warshall-algorithm-dp-16/)
*   [克鲁斯卡尔算法(用于寻找最小生成树)](https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/)
*   [普里姆算法(用于寻找最小生成树)](https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/)

当你在竞争性编程的世界中前进时，首先掌握 DFS 和 BFS 将会产生巨大的成果。

现在你知道了你需要学习什么算法和技术，你就准备好找出你可以用什么比赛来练习你的技能。

# 主要比赛/在线评委

## [美国计算机奥林匹克竞赛(USACO)](http://www.usaco.org/)

USACO 是一个竞争性编程竞赛，每年在 1 月、2 月、3 月和 12 月举行。它分为四个级别:青铜级、白银级、黄金级和白金级。每个部门都越来越难，白金是最难的。

他们还有一个[培训页面](https://train.usaco.org/usacogate)，这是一个练习和学习竞争性编程的好地方。

## [Codeforces](/codeforces.com)

Codeforces 是一个平台，在这个平台上举行了许多编程竞赛。这些问题通常质量很高，它们有一个很大的过去问题数据库，涵盖了你可以用来练习技能的各种各样的主题。Codeforces 致力于一个评级系统，如果你刚刚开始，我建议你解决 1000-1200 的评级问题。

## [AtCoder](https://atcoder.jp/)

AtCoder 是一个精彩的编程大赛，尤其适合初学者。他们经常举办初学者竞赛，这是新人进入竞争性编程世界并学习如何成功的好方法。

像 Codeforces 一样，AtCoder 也有大量的问题需要你去解决和提高你的技能。

# 利益

*酷，但是我永远不会在工作中实现 Dijkstra 算法，*对吗？我就用一个内置函数或者用一个库。

虽然这些观点都是正确的，但竞争性编程的目标并不是让你从头开始实现所有这些花哨的算法和数据结构。而是培养解决问题的能力。

解决大量竞争性编程问题有助于提高你解决问题的技能。这就是为什么许多公司让你解决竞争性编程——比如面试中的问题，不是为了看你是否能在工作中运用 Dijkstra 算法，而是为了看你是否能快速解决问题，从容应对。

现在出去，开始编程吧！

如果你喜欢这篇文章，可以考虑订阅我的简讯:[https://mailchi.mp/35c069691d2c/newsletter-signup](https://mailchi.mp/35c069691d2c/newsletter-signup)。