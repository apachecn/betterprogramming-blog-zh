# 用 Ruby 查找数组中出现频率最高的元素

> 原文：<https://betterprogramming.pub/two-ways-of-finding-the-element-that-occurs-the-most-in-an-array-with-ruby-7fb484ea1a6d>

## 迭代数组的两种方法

![](img/0d6d76969afb7a820d50d59574d1e7c8.png)

照片由[克莱姆·奥诺杰霍](https://unsplash.com/@clemono2?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/smarties?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在我的文章中，我将讨论一个对数组进行操作的特定任务，这个任务对我来说有点有趣，我喜欢它。这也体现了 Ruby 的想法，即执行一项任务有多种方式，单一的一种方式是不正确的。这难道不是一个非常令人接受的观点吗？

无论如何，我已经多次遇到一个任务，它要求我迭代一个数组，目的是试图选择在那个数组中出现最多的元素。我将讲述一个有趣的例子。

卡罗尔是一个动物囤积者。她就是控制不住自己。她遇到的任何流浪狗，她都要带回家。这确实是个问题。

这成了一个问题，动物控制中心告诉她，她需要记录她有哪些宠物，因为她必须处理掉其中一些动物。她住在一个一居室的公寓里！

她决定扔掉她最喜欢的一只宠物。但是这么多，她又不想用手数。

这里是卡罗尔的宠物阵列:

```
pets = [ “dog”, “dog”, “cat”, “bird”, “chinchilla”, “rat”, “hampster”, “dog”, “rat”, “bird”, “cat”, “giraffe”, “cat”, “bird”,”dog”, “snake”, “hamster”, “dog” , “mouse”, “spider”, “dog”, “cat”]
```

# 方法一。

我将使用的第一种方法是将数组转换成带有值的散列。我们假设您了解如何设置 hash 的键和值。这个方法所做的是遍历数组，将数组中的一个元素设置为哈希的键，并且对于每个元素的出现，将其值增加 1。因此，如果它遇到散列中已经存在的宠物，它会将该宠物的值加 1，同时保持密钥的唯一性。

下面是第一种方法的样子:

```
pet_count = Hash.new(0)
pets.each {|pet| pet_count[pet] += 1}
pet_count.sort_by { |pet,number| number}.last[0]
```

让我们稍微分解一下。

```
pet_count = Hash.new(0)
#creates a new hash, sets its default values to 0.pets.each {|pet| pet_count[pet] += 1}
#iterates each element, sets our empty hashes key to that of a pet. # “+=“ is the operator on the value of a keys occurrence in the hash.
```

(如第一次迭代:`pet_count = {“dog” => 1 }`。这是第一次出现“狗”)

如果我们的第二次迭代在我们的 hash 中包含了一个键的相同元素，那么它将那个键的值加 1，同时保持键的唯一性，这就是所发生的情况。(例如第二次迭代:`pet_count = {“dog” => 2}`。这是第二次出现“狗”)

我们的第三次迭代设置了一个新的键和一个新的值，因为我们的`carols_pets`数组中的下一个元素对我们的散列来说是新的。我们的散列不包含“cat”的键，所以创建了一个新的键，并自动将值设置为 1。现在我们的`pet_count = {“dog”=>2, “cat”=> 1}`。

对我来说这是美丽的。它涉及到迭代和散列创建的结构，以及块中的键和值设置。如果您是操作数组和散列的新手，这是一个很好的分析方法。查看结果也很满意。这是:

```
pet_count = {“dog"=>6, "cat"=>3, "bird"=>3, "chinchilla"=>1, "rat"=>2, "hamster"=>2, "snake"=>1, "mouse"=>1, "spider"=>1}
```

如你所见，我们的散列不太整齐。

现在我们可以使用下面的方法对这个散列进行操作，以返回最常出现的 pet。

```
pet_count.sort_by{|pet,number| number}.last[0]
#hash gets sorted by number
```

现在让我们稍微分解一下:

`.sort_by`是哈希方法，块是哈希排序的依据(宠物出现的次数)。

如果我们只是跑:

```
pet_count.sort_by {|pet,number| number}
#returns 
[["snake", 1], ["spider", 1], ["mouse", 1], ["chinchilla", 1], ["rat", 2], ["hamster", 2], ["bird", 3], ["cat", 3], ["dog", 6]]
```

这种方法对整数的默认排序是从低到高，所以实际上最后一个键值对是最常见的。你所要做的就是调用方法“last”

```
pet_count.sort_by{|pet,number| number}.last
returns [“dog”=> 6]
```

你所要做的就是加上[0]，它对一个数组索引号进行操作。

```
pet_count.sort_by{|pet,occurrences| occurrences}.last[0]
returns "dog"
```

我们走吧。我们有最受欢迎的宠物。虽然有点冗长复杂。

# **方法二。**

这个要短得多，给出了您需要的确切答案，并且不需要您将数组转换成散列。这也很好。如果我只想归还最常出现的宠物，我可能会从现在开始使用它。

还记得我们的卡罗尔宠物系列吗:

```
pets = [ “dog”, “dog”, “cat”, “bird”, “chinchilla”, “rat”, “hamster”, “dog”, “rat”, “bird”, “cat”, “giraffe”, “cat”, “bird”,”dog”, “snake”, “hamster”, “dog” , “mouse”, “spider”, “dog”, “cat”]
```

这是我们的方法:

```
pets.max_by {|i| pets.count(i)}
```

这是怎么回事？方法中的方法。方法的起源。

## **什么。max_by does**

它返回最大值的对象。如果我们想查看哪种动物的名字最长，我们可以使用:

```
pets.max_by{|pet| pet.length}
returns "chinchilla"
```

哪个是名字最长的宠物？

你会注意到:

```
chinchilla.length = 10
```

`.max_by`返回对象。

## **什么。计数※**

不言自明:它计算数组中的项数。如果我们这样做，它将返回数组中的项数，20。

```
pets.count = 20
```

如果我们给它一个参数，比如`pets(“cat”)`，它会返回猫在数组中出现的次数。

```
pets(“cat”) = 4
```

当我们使用下面的方法时，我们只是简单地结合了这些方法。

```
pets.max_by{|pet| pets.count(pet)} 
```

我们知道一个宠物对象将被返回，用`max_by`，数组中的每个宠物都用`pets.count(pet)`计数。每个元素都被迭代和计数，然后`max_by`挑选出计数最高的对象——也就是“狗”

```
pets = [ “dog”, “dog”, “cat”, “bird”, “chinchilla”, “rat”, “hamster”, “dog”, “rat”, “bird”, “cat”, “giraffe”, “cat”, “bird”,”dog”, “snake”, “hamster”, “dog” , “mouse”, “spider”, “dog”, “cat”]pets.max_by {|pet| pets.count(pet)}
returns "dog"
```

# **结论**

每种方法都有自己的好处。假设您想要更多信息，比如有多少最受欢迎的宠物，hash 方法是一个不错的选择。

如果你特别希望回报是宠物，并且不需要任何额外的信息，第二种方法是可行的。只有一句好听的台词。

所以现在卡罗尔必须除掉老拉斯蒂。他是一只好狗，我相信他会让一些家庭非常高兴。

# **资源**

如果你刚开始，这里有一些资源可能会有帮助。

用[Ruby-Doc.org](https://ruby-doc.org/)习惯数组和散列方法

[Ruby Doc 数组方法](https://ruby-doc.org/core-2.2.0/Array.html)

[Ruby Doc 哈希方法](https://ruby-doc.org/core-2.2.0/Hash.html)

[使用哈希](http://www.informit.com/articles/article.aspx?p=26943&seqNum=3)

[更多方法！](https://stackoverflow.com/questions/412169/ruby-how-to-find-item-in-array-which-has-the-most-occurrences)