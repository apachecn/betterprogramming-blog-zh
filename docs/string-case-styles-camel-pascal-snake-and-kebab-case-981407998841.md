# 案件风格:骆驼，帕斯卡，蛇，和烤肉串案件

> 原文：<https://betterprogramming.pub/string-case-styles-camel-pascal-snake-and-kebab-case-981407998841>

## 将单词组合成单个字符串的最常用方法

![](img/64487a37df6741b723edaafe8ffcf028.png)

奥斯卡·伊尔迪兹拍摄于 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## TLDR；

*   茶包
*   帕斯卡凯斯
*   蛇 _ 案例
*   烤肉串盒

## 删除单词之间的空格

在编程中，我们经常删除单词之间的空格，因为不同种类的程序保留空格(“”)字符用于特殊目的。因为空格字符是保留的，所以我们不能用它来表示我们用人类语言用多个单词表达的一个概念。

举例来说，我们的代码中不经常引用*用户登录计数*作为`user login count`的概念。我们不做以下事情:

`user login count = 5`

典型的语言解析会将每个单词视为一个独立的概念。用户、登录和计数将被分别处理。所以，我们做了如下的事情:

`userLoginCount = 5`

现在，解析器将看到一个概念，`userLoginCount`，我们程序员可以很容易地看到它的表示。

## 组合单词的最佳方式

没有最好的单词组合方式。在上面的例子中，我们去掉了空格，把第一个单词后面的每个单词都大写。然而，有大量的合并单词的算法，以及一些非常常见的算法。

常用的组词策略有:骆驼格、帕斯卡格、蛇格、烤肉串格。我们会在这里讨论这些。

## 骆驼箱(骆驼箱)

![](img/604c39fadd0a914385f7ccad8ac1d3d3.png)

[隆布·卡巴索](https://unsplash.com/@llombe_kbs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的《站在街上的三只骆驼》

Camel case 通过大写第一个单词后面的所有单词并删除空格来组合单词，如下所示:

**生:**

**骆驼案:**

这是一种非常流行的将单词组合成一个概念的方法。在许多语言中，它经常被用作变量声明的约定。

## 帕斯卡格(Pascal Case)

Pascal case 通过大写所有单词(甚至是第一个单词)并删除空格来组合单词，如下所示:

**生:**

**帕斯卡格:** `UserLoginCount`

这也是一种非常流行的将单词组合成一个单一概念的方法。在许多语言中，它经常被用作声明类的约定。

## 蛇案(snake_case)

![](img/3fd54b2e0badd4033e6c110b4b54d47c.png)

《棕蛇》由[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上演

Snake case 通过用下划线(_)替换每个空格来组合单词，在全大写版本中，所有字母都大写，如下所示:

**Raw:**

**蛇案:** `user_login_count`

**蛇案(全大写):** `USER_LOGIN_COUNT`

这种风格，当大写时，经常被用作许多语言中声明常数的约定。小写时，它通常用于声明数据库字段名。

## 烤肉串盒(烤肉串盒)

![](img/d348d27e225f3e8848e40f867c928184.png)

[潘晓珍](https://unsplash.com/@zhenhappy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的《棕色板上的烤肉》

Kebab case 通过用破折号(-)替换每个空格来组合单词，如下所示:

**生:**

**烤肉串案例:** `user-login-count`

这种样式经常在 URL 中使用。例如，*www.blog.com/cool-article-1*。这是一种很好的、干净的、人类可读的组合单词的方式。

## 哪个最好？

没有最好的单词组合方法。最重要的是要与所使用的约定保持一致，如果你在一个团队中，要就约定达成一致。

感谢阅读！

感谢阅读！

感谢阅读！

感谢阅读！

干杯！