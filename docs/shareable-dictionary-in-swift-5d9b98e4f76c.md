# Swift 中的可共享词典

> 原文：<https://betterprogramming.pub/shareable-dictionary-in-swift-5d9b98e4f76c>

## 通过引用学习在 Swift 中建立字典

![](img/d76ff6d78f7077b37ea8168e4c649e90.png)

来源: [Undraw](https://undraw.co/)

Swift 中的字典是按值传递的。这意味着如果传递或复制了一个字典，就会创建第二个实例。

当你修改第二个实例时，很明显，第一个没有变异。这很酷也很安全，但有时你需要在应用程序的不同部分共享和修改字典。让我们看看你怎么做。

我们用一点小技巧就能达到这个效果。当然，值类型不能共享，但引用类型可以，所以我们可以将字典“包装”在一个类中，该类将在应用程序之间共享:

我使用了泛型占位符来存储任何类型的数据。

好了，这段代码可以工作了，你现在就可以使用它，但是有一个小问题…让我们看看:

```
**var** foo: PPBSharedDictionary<String, String> = PPBSharedDictionary()foo.dict = [“key”: “value”]
```

每次我们需要使用我们的字典时，我们必须访问类的内部属性，这不是很优雅…让我们用下标的力量来修复它，并隐藏内部字典使它`private`:

搞定了！现在，我们可以以规范的方式直接访问包装好的字典:

```
**var** foo: PPBSharedDictionary<String, String> = PPBSharedDictionary()foo = [“key”: “value”]
```