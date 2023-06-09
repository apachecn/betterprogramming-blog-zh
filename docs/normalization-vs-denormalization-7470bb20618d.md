# 规范化与反规范化

> 原文：<https://betterprogramming.pub/normalization-vs-denormalization-7470bb20618d>

## 什么时候各合适？

![](img/9b77fe48d430292c6915de7ef69f823e.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 一个地方或许多地方

规范化和反规范化之间的区别很简单。当数据被规范化时，它存在于且仅存在于一个真实来源位置。非规范化数据存在于多个汇总位置。

存储在一个或多个位置的数据对*的准确性和速度*有着重要的影响。如果数据位于一个源位置(即被规范化)，则必须将其与其他数据汇总才能使用。如果数据在多个位置汇总(即反规范化)，当数据更新时，在每个位置可以更改之前，它将有一段时间不同步。

下面是每种方法的一个例子:

## **归一化数据:**

下面我们可以看到三个用户:Patrick、Eric 和 Robert。帕特里克是其他人的朋友。埃里克和罗伯特彼此不认识。

用户表:

```
ID NAME    EMAIL
1  Patrick patrick@somewebsite.com
2  Eric    eric@somewebsite.com
3  Robert  robert@somewebsite.com
```

友谊连接表:

```
Friendship join table:
USER_A_ID USER_B_ID
1         1
1         2
```

这里需要注意的重要一点是，每个人的姓名和电子邮件地址只列出一次。如果 Eric 的电子邮件地址发生变化，它只需在其存在的位置进行更新。然而，如果 Patrick 登录到系统中，并想知道他的朋友是谁以及他们的电子邮件地址，数据库将需要使用一些计算时间来将所有这些联系在一起，并将结果传递给 Patrick。

## **非规范化数据:**

下面是彼此关系相同的三个用户。

```
{
  name: "Patrick",
  email: "patrick@somewebsite.com",
  friends: [
    {
      name: "Eric",
      email: "eric@somewebsite.com"
    },
    {
      name: "Robert",
      email: "robert@somewebsite.com"
    },
  ]
},
{
  name: "Robert",
  email: "robert@somewebsite.com",
  friends: [
    {
      name: "Patrick",
      email: "patrick@somewebsite.com"
    }
  ]
},
{
  name: "Eric",
  email: "eric@somewebsite.com",
  friends: [
    {
      name: "Patrick",
      email: "patrick@somewebsite.com"
    }
  ]
}
```

这里需要注意的重要一点是，每个人的姓名和电子邮件地址都被多次列出。

如果 Patrick 登录系统并想知道他的朋友是谁以及他们的电子邮件地址，他可以很快获得信息，因为数据已经准备好了，不需要额外的计算时间来收集这些信息。但是，如果 Eric 的电子邮件地址发生变化，它必须在多个位置进行更新，并且可能需要一些时间来传播，从而导致一些数据不同步。

# 正常化

规范化的好处是准确。

使用规范化，一段数据存在于系统中的一个且仅一个位置。数据不可能在一个位置正确而在另一个位置不正确，因为它只存在于一个位置。如果数据在某一时刻是不正确的，通过在一个位置更新它，现在它在任何地方都是正确的。

规范化的缺点是一段数据不会立即有用。计算时间必须花在总结它的飞行，它是一个像样的形式。这种计算时间导致访问有用数据的速度变慢。

数据是完美的，但可能需要一些时间。

# 反规格化

反规范化的好处是速度。

通过反规范化，数据可以立即呈现出来。数据已经与其他相关数据一起作为一个包收集，可以作为一个整体一起交付。因为打包数据不需要额外的计算，所以反规范化的数据很快就可以提供。

反规范化的缺点是数据可能会不同步。一条数据可以存在于同一系统中的多个位置。因为它存在于多个位置，所以有可能一个位置被更新而另一个位置不被更新。如果某一时刻的数据不正确，则必须在所有位置进行纠正。

数据很快，但可能并不完美。

# 两者都用

规范化和反规范化针对不同的事情进行了优化。两个都用是个好主意。

对于您的真实来源，最好将您的数据标准化，这样当它需要更新时，您只需在一个位置更改它，它就会在整个系统中得到完美的更新。

为了快速交付数据，最好获取规范化的数据，并从中派生出非规范化的现成表单。这种反规格化的形式不需要完美，只要快就行。完美性仍然保持在规范化的形式中。

# 摘要

总是规格化，但是为了速度而反规格化。