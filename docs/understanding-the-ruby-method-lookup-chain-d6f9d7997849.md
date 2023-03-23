# 理解 Ruby 方法查找链

> 原文：<https://betterprogramming.pub/understanding-the-ruby-method-lookup-chain-d6f9d7997849>

## 揭开 Ruby 的神秘面纱

![](img/7216ed2bbdb944728b58d7c2ae79019b.png)

照片由[通讯社跟随](https://unsplash.com/@olloweb?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

当你在 Ruby 中调用一个方法时，很多事情都是在幕后发生的。Ruby 解释器必须知道这个方法是在哪里声明的，才能知道它必须解释什么行为。

这个过程被称为*方法查找。*

它遍历一个对象的祖先链，查看这个方法是在哪里声明的。

我的好朋友 Mehdi Farsi 写了一篇[文章解释祖先链](https://medium.com/rubycademy/ruby-object-model-part-1-4d06fa486bec)。我不能比他解释得更好，所以如果你不熟悉 Ruby 的祖先链，我建议你去看看。

# 那么它是如何表现的呢？

假设您有以下几个类:

`Dog`类继承自`Animal`类，后者定义了一个`#speak`方法。

所以我们可以这样做:

```
dog = Dog.new
=> #<Dog:0x00007ff5b78e9270>dog.speak
"I am from the Animal class"
=> "I am from the Animal class"
```

通过简单的继承，我们的`Dog`类实例可以访问其父类的`#speak`方法。

但是 Ruby 究竟如何知道在哪里可以找到这个方法呢？

嗯，很简单。

它使用类似下面的方法检查调用该方法的对象(在本例中，是一个`Dog`类的实例):

`#instance_methods`顾名思义，以数组的形式返回实例的方法。在这种情况下，`Dog.instance_methods`返回一堆从其祖先继承的方法，包括我们的`#speak`方法:

```
Dog.instance_methods=> [**:speak**, :instance_variable_defined?, :remove_instance_variable, :instance_of?, :kind_of?, :is_a?, ...]
```

但是这不是我们想要的，因为我们只想要在`Dog`类中定义的方法，而不要它的祖先的方法。

为此，我们将`false`传递给`instance_methods`,以便只获取在`Dog`类中定义的方法:

```
Dog.instance_methods(false)
=> []
```

我们可以得出结论，Ruby 使用了类似于:

```
Dog.instance_methods(false).include?(:speak)
=> false
```

既然我们知道`Dog`类没有定义`#speak`方法，我们可以在祖先链中向上一个类到`Animal`类。

```
Dog.ancestors
=> [Dog, Animal, Object, Kernel, BasicObject]
```

它进入`Animal`类，在那里它找到了圣杯。

```
Animal.instance_methods(false)
=> [:speak]
```

算法很简单:

1.  检查当前对象，查看它是否定义了该方法，如果没有，请转到步骤 2。
2.  只要它没有被定义，它就调用它的超类并重复步骤 1。

# 结论

这种算法使得查找链密封，没有方法重叠，也使得覆盖方法成为可能。

这里有一个简单的例子:

我们可以看到`Dog`类和`Animal`类都定义了`#speak`方法。所以在一个`Dog`实例上调用这个方法时…

```
Dog.new.speak
"I am from the Dog class"
=> "I am from the Dog class"
```

…算法检查该方法是否在调用它的对象中定义。在这种情况下，它是`Dog`的一个实例:

```
Dog.instance_methods(false)
=> [:speak]Dog.instance_methods(false).include?(:speak)
=> true
```

然后，它停止了——因为我们已经找到了我们的赢家。