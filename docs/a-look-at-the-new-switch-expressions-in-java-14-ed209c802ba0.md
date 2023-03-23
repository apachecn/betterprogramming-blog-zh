# 看看 Java 14 中新的“开关”表达式

> 原文：<https://betterprogramming.pub/a-look-at-the-new-switch-expressions-in-java-14-ed209c802ba0>

## 举了很多例子

![](img/a8c194d35365da6cfafd2b8d6584892e.png)

照片由[卡里姆·曼吉拉](https://unsplash.com/@karim_manjra?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Java 14 准备在 2020 年 3 月 17 日发布。新版本的 Java 包含了一个有趣的 Java 语言更新:新的`switch`表达式。让我们看看如何使用新的`switch`表达式，它们提供了什么样的优势，以及什么可能会出错。最后，我们将解决一个关于`switch`表达的棘手问题。

# 经典的“转换”语句

Java 中`switch`语句的当前设计遵循 C 和 C++等语言。它只作为一个语句工作，默认情况下支持失败语义。下面是一个带有枚举的经典`switch`语句的例子:

您可能已经注意到了上面示例中的许多 case 和 break 语句。这些语句引入了一些视觉噪声，使代码变得不必要地冗长。这种视觉噪音可能会掩盖错误，如遗漏 break 语句，这将意味着意外失败。

# 新的“开关”表达式

Java 14 扩展了`switch`,因此它既可以用作语句，也可以用作表达式。特别是，新的 Java 引入了以下内容:

*   一种新形式的`switch`标签`case ... ->`，如果标签匹配，则只执行标签右边的代码。`case ... ->`标签右边的代码可能是一个表达式、一个块或一个 throw 语句。
*   一个新的`yield`语句产生一个值，该值成为封闭的`switch`表达式的值
*   每个案例中有多个常量，用逗号分隔

有了新的 Java 14，可以同时使用传统的`case ... :`标签和新的`case ... ->`标签。重要的是传统标签仍然默认支持 fall through，但新标签不需要。

让我们看看如何用新的`switch`表达式重写上面的例子:

这个简单的例子只需一条命令就可以编译和运行(多亏了 JEP 330，它允许从 Java 11 开始启动单文件源代码程序):

```
$ java WhoIsWho.java
Mozart was a composer
Dali was a painter
Dostoevsky was a writer
```

您可以看到几个 case 语句已经合并，代码不再使用 break 语句。结果，`print()`方法变得更短，看起来更好。

下面的例子展示了一个多行的`default`块，它使用新的`yield`语句来产生一个值。请注意，`Person`枚举中有几个新常量没有被`switch`表达式中的`case`标签覆盖:

下面是程序打印出来的内容:

```
$ java WhoIsWho.java 
Mozart was a composer 
Dali was a painter 
Oops! I don't know about Einstein 
Einstein was a ...
```

下一个例子展示了如何用新的`switch`表达式实现`factorial`:

# 重要细节

关于新的`switch`表达式，有几件重要的事情需要了解。

首先要知道的是一个`switch`表达式的情况必须是详尽的。换句话说，对于所有可能的值，必须有一个匹配的`switch`标签。让我们向枚举添加一个新元素，看看会发生什么:

编译将立即失败，并显示以下错误消息:

```
$ java InvalidWhoIsWho.java
InvalidWhoIsWho.java:19: error: the switch expression does not cover all possible input values
         String title = switch (person) {
                        ^
1 error
error: compilation failed
```

添加一个简单的`default`案例让 Java 编译器很高兴:

一般来说，除非使用枚举，并且一个`switch`表达式的情况覆盖了所有常量，否则在`switch`表达式中需要一个`default`子句。

第二件要记住的事情是一个`switch`表达式必须要么正常完成，要么抛出一个异常。让我们来看看下面的代码:

如果我们试图编译这段代码，Java 编译器会立即抱怨:

```
InvalidSwitchExpressionWithoutDefault.java:8: error: the switch expression does not cover all possible input values
        return switch (n) {
               ^
1 error
error: compilation failed
```

同样，添加一个`default`案例使其工作:

要记住的第三件重要的事情是`yield`现在是一个受限标识符。特别是，这意味着名为`yield`的类变得非法:

```
$ cat YieldClassName.java 
class yield {}
$ javac YieldClassName.java
YieldClassName.java:1: error: 'yield' not allowed here
class yield {
      ^
  as of release 13, 'yield' is a restricted type name and cannot be used for type declarations
1 error
error: compilation failed
```

但是，允许使用`yield`作为变量或方法名:

```
$ cat ValidUseOfYieldWord.java 
public class ValidUseOfYieldWord {
    void yield() {
        int yield = 0;
    }
}
$ javac ValidUseOfYieldWord.java && echo ok || echo failed
ok
```

# 结论

以下是作者对新的`switch`表达的看法:

> "这些变化将简化日常编码."

让我们看看。

# 奖金

你认为会发生什么？以下是选项:

1.  编译错误。
2.  运行时错误。
3.  `Oops`是打印出来的。
4.  `OopsOops`被打印出来
5.  `Forty-two`被打印出来。

# 资源

*   [JEP 361:开关表达式](https://openjdk.java.net/jeps/361)
*   [OpenJDK 14 时间表和增强功能列表](https://openjdk.java.net/projects/jdk/14/)