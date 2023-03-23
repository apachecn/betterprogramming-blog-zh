# 使用 Java 流模拟递归

> 原文：<https://betterprogramming.pub/simulate-recursion-using-java-streams-9219c834957e>

## 逼近平方根的 Bakhshali 方法的函数实现

![](img/1e75b851d7e28f0eff47159ec3ecf8c9.png)

巴赫沙里手稿。国家地理，公共领域，通过维基共享

在上一篇文章[重新思考结构化编程](https://levelup.gitconnected.com/rethinking-structured-programming-7c0ae2f73b90)中，我谈到了在 Java 中使用流和反应式函数编程作为结构化编程中的控制结构。我描述了一个我认为目前无法用流或通量的特征解决的问题:如果一个函数的输出是同一个函数迭代的输入。这描述了一种特殊类型的递归——尾递归——它可以变成一个循环。但是在我所有的例子中，一个阶段的输出总是下一个阶段的输入。一个阶段绝不会仅仅通过将其输出传递回其输入来进行迭代。

原来有一个 Java `Stream`类的方法，`iterate`，它就是这样做的。在我的上一篇文章中，我使用了[巴比伦](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method)方法来计算近似平方根，作为这类问题的一个例子。所以，我决定试试能否用`iterate`函数实现它。

`iterate`函数是`Stream`类的静态方法。它返回一个由种子值和一个`Unary`函数计算得到的无限流，这个函数接受一个值并返回一个相同类型的值。在我们的例子中，类型将是一个`double`。

我们可以通过使用`Random.doubles(int size)`方法生成一个随机的`Stream`双精度浮点数。我将创建一个包含 15 个 doubles 的流来测试它。我可以使用流的`forEach`方法，因为这将是一个终端任务，即只计算值并打印出来。因此，代码以下面的代码开始和结束:

```
public class Recurse {
    public static void main(String [] args) {
        Random random = new Random();
        random.doubles(15)
           .forEach(s -> {
...
            });
    }
}
```

这将调用`lambda`方法 15 次，随机双精度 15 次。我们将用它来测试我们的新方法。

我不会用巴比伦的方法。我将使用古印度数学手稿 [Bakhshali 手稿](https://en.wikipedia.org/wiki/Bakhshali_manuscript)中描述的 [Bakhshali 方法](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Bakhshali_method)。它浓缩了巴比伦方法的两次迭代，并将四次分解。基本公式是，如果`S`是你想要求平方根的数字，`x`是一个种子近似值，`intermediate a`是`S minus x squared divided by 2x`。`Intermediate b`是`x plus a`。最后，新的近似值是`b minus a squared divided by 2b`。不要让我解释，我只是个程序员。

无论如何，这个函数的`Unary`应该是:

```
double a = (s-(x*x))/(2*x);
double b = x + a;
return b - (a*a)/(2*b);
```

我用了很多括号，因为我从来不记得运算符优先级。

现在我们可以把它放入迭代函数:

```
Stream.iterate(0.1, x -> {
                    double a = (s-(x*x))/(2*x);
                    double b = x + a;
                    return b - (a*a)/(2*b);
                })
```

种子值是任意的；您可以使用任何东西，但是您越接近实际值，您需要的迭代次数就越少。我们所有的值都是介于 0.0 和 1.0 之间的双精度值。

像现在这样，不会有任何好处，因为它会创造一个永无止境的双价值流。我准备用`dropWhile`的方法，当近似平方根的平方非常接近原始数的时候，结束它:

```
dropWhile(x -> Math.abs((x*x)-s) > 0.00000001)
```

我还将对迭代次数进行硬性限制，以防不收敛。把所有这些放在一起，你得到这个:

```
double r = Stream.iterate(0.1, x -> {
  double a = (s-(x*x))/(2*x);
  double b = x + a;
  return b - (a*a)/(2*b);
})
.limit(1000) // force a limit
.dropWhile(x -> Math.abs((x*x)-s) > 0.00000001)
.findFirst() // returns Optional<Double>
.orElse(Double.NaN);
System.out.println("calculated square root of "
   + s
   + " = " 
   + r);
System.out.println("math lib square root of "
   + s
   + " = " 
   + Math.sqrt(s));
```

将上面的内容放入产生 15 个 doubles 的`forEach`并运行它，您可以看到输出并确认我们的计算值等于由`Math.sqrt`返回的值。

这只是使用函数式编程抽象出控制结构的另一个例子。像`iterate`这样的方法是一元的:它们旨在充当函数的流机制，并且可以链接在一起以产生期望的结果。如果你知道你的一元函数，你将能够最大限度地使用像`Stream`和`Flux`这样的类型。