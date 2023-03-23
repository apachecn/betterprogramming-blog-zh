# 如何在 Go 中将指针作为参数传递

> 原文：<https://betterprogramming.pub/passing-pointers-as-arguments-in-go-a77a7d1f8493>

## 需要修改值时如何处理指针

![](img/c27733c79b96b96b3451979aaccf9629.png)

凯莉·西克玛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片。

这是围棋中一个简单而基本的概念，但作为一个新手，我很难理解到底发生了什么。所以这可能会为另一个困惑的初学者节省一点时间。

# 问题是

有时，当将一个变量作为参数传递给一个函数，目的是以某种方式改变该变量时，该变量在调用该函数后根本不会改变。如果我在函数范围内改变值，那怎么可能呢？

Go 中的变量总是通过值来传递，也就是说每次你把一个变量传递给一个函数，这个函数都会复制这个变量，而不会接触到原来的变量。

让我们举个简单的例子。我们想向购物列表添加一些项目(字符串片段中的字符串)，所以我们只需声明并初始化我们的项目，然后将它们追加到购物列表中:

但是当我们打印出购物清单时，我们得到:

```
//Output
Shopping list contains: []
```

这可不好。我们在购物清单上加了三样东西，但它看起来完全是空的。当我们在函数中明确地将这些项添加到列表中时，这怎么可能呢？

关键事实是:变量(我们的购物清单)确实在变化，但这只发生在被调用的函数范围内。如果我们对函数中的购物清单进行快照，我们会看到发生了什么。让我们添加一行来查看函数范围内的列表:

我们得到了:

```
//Output
[oreos]                               //appending cookies
[toilet paper]                        //appending vaccine
[Animal Crossing: New Horizons]        //appending lifePurpose
Shopping list contains: []            //Actual list still empty
```

现在我们确认了购物清单只在函数内部发生变化，因为当切片作为参数传递时，函数会复制一个只存在于函数范围内的副本。这是因为，Go 中的变量总是通过值传递的。

# 解决方案

为了使函数能够更改并实际向切片添加项目，它需要始终指向同一个`shoppingList`变量。因此，我们可以通过简单地传递一个指向`shoppingList`变量而不是变量的指针来解决这个基本但令人讨厌的问题。

这样，函数将总是引用同一个变量，而不仅仅是复制它。

注意:从技术上讲，即使当通过指针传递变量时，指针仍然是通过值传递的。该变量从不通过引用传递。

显然，我们改变了函数调用，给它变量的地址:

```
addToShoppingList(cookies, &shoppingList)
addToShoppingList(vaccine, &shoppingList)
addToShoppingList(life, &shoppingList)
```

因为我们在第 3 行直接修改了指向变量`shoppingList`的指针，我们可以简化函数:

```
func addToShoppingList(item string, shoppingList *[]string) {*shoppingList = append(*shoppingList, item)  //line 3}
```

但不管怎样，我们都会得到:

```
//Output
Shopping list contains: [oreos toilet paper Animal Crossing: New Horizons]
```

所以我们设法修改了我们的购物清单！

# 结束了

因此，虽然这显然是一个过于简化的案例，但如果您对这个琐碎但有用的概念有任何疑问，我希望这有助于消除您的疑虑。如果你想补充一些东西或者告诉我我可能犯的任何错误，就在下面写下你的回答。

干杯！