# Java 中的相等性和比较:陷阱和最佳实践

> 原文：<https://betterprogramming.pub/equality-and-comparison-in-java-pitfalls-and-best-practices-96b713e7009>

## 仅仅因为它“平等”，并不意味着它是平等的。如何正确地比较对象和原语

![](img/5971c813502fc1d6ba5ff4987b1cfd18.png)

[约根·哈兰](https://unsplash.com/@jhaland?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/twins?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Java 有不同的比较对象和原语的方法，每种方法都有自己的语义。使用“错误的”会导致意想不到的结果，并可能引入微妙的、难以捕捉的错误。

在我们了解 Java 中的陷阱和相等与比较的最佳实践之前，我们需要理解不同类型及其行为。

# 原语与对象

Java 类型系统是双重的，由八个[原始数据类型](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html) ( `boolean`、`byte`、`char`、`short`、`int`、`long`、`float`、`double`)和对象引用类型组成。

## 基元

Java 中的原语不能是未初始化的或者`null`，它们总是有一个默认值。它代表`0`，适用于特定的数据类型:

```
 Primitive | Default Value 
-----------|--------------- 
 boolean   | false
 byte      | 0
 char      | '\u0000'
 short     | 0
 int       | 0
 long      | 0l
 float     | 0.0f
 double    | 0.0d
```

## 原始包装类

每个原始数据类型在`java.lang`中都有一个对应的包装类，将其值封装在一个 Java 对象中:

```
 Primitive | Wrapper | Superclass
-----------|---------|------------
 boolean   | Boolean | Object
 byte      | Byte    | Number
 char      | Char    | Object
 short     | Short   | Number
 int       | Int     | Number
 long      | Long    | Number
 float     | Float   | Number
 double    | Double  | Number
```

成为对象允许它们在更广泛的场景中使用:

*   通用类型(如`List<Integer>`)。
*   按引用传递而不是按值传递。
*   能力为空。
*   等等。

但是我们也必须处理所有的不利因素。像`NullPointerException`，更大的内存占用和性能影响。

## 自动装箱和拆箱

在我们了解平等和比较之前，我们需要了解的最后一件事是[拳击](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)。

尽管原语和对象引用具有不同的语义，但由于 Java 编译器，它们可以互换使用。

*自动装箱*是原语类型在其对应包装类中的自动转换，*拆箱*是另一个方向。这使得我们可以不加区别地使用这两种类型:

```
List<Integer> values = new ArrayList<>();for (int i = 0; i < 50; i++>) {
    values.add(i);
}
```

我们的`List`使用包装器类型`Integer`，但是我们的代码会编译，即使我们添加了一个`int`。多亏了编译器通过自动装箱`i`来改变我们的代码，这才成为可能:

```
List<Integer> values = new ArrayList<>();for (int i = 0; i < 50; i++>) {
    values.add(Integer.valueOf(i));
}
```

反之亦然:

尽管我们使用了像`%`和`+`这样的操作符，但这些操作符对于对象类型`Integer`来说是不可用的，代码编译得很好。因为编译器*拆箱*包装器类型。实际编译的代码看起来更像这样:

# 平等

如果我们看看其他编程语言，对于如何比较值，最符合逻辑的结论可能是`==`操作符和它的对手`!=`。

是的，我们可以用它们来检查相等性，它们相互比较值，但是它可能不是你期望的值。

## 基元

原语是 [*文字*](https://docs.oracle.com/javase/specs/jls/se7/html/jls-3.html#jls-3.10) ，内存中的固定值，可以用`==`测试是否相等。

除非他们不能。

与其他原始数据类型相比，浮点数据类型`float`和`double`由于其在内存中的[存储方法，无法可靠地检查其是否与`==`相等。它们不是精确的值:](https://en.wikipedia.org/wiki/IEEE_754)

```
float value = 1.0f;
value += 0.1f;      *// 1.1f*
value += 0.1f;      *// 1.2f*
value += 0.1f;      *// 1.3000001f*boolean isEqual = (value == 1.3f) *// false*
```

我们有两个选择来处理这件事。要么使用`java.util.BigDecimal`，也就是*确切的说是*。或者通过使用基于阈值的比较:

```
float value = 1.0f;
value += 0.1f;      *// 1.1f*
value += 0.1f;      *// 1.2f*
value += 0.1f;      *// 1.3000001f*float THRESHOLD = 0.00001f;
boolean isEqual = Math.abs(value - 1.3f) < THRESHOLD); *// true*
```

## 数组

另一个陷阱是基本数组，因为数组不是基本类型，它们是对象。

## 目标

如果用`==`比较对象，它也会比较对象的值。这里唯一的问题是对象的值实际上是它的引用，因此得名*对象引用类型*。

这意味着只有当两个值指向内存中的同一个对象时，它们才相等。

实际上，变量在某些情况下可能相等，但在其他情况下不相等:

```
String a = "a";
String b = "b";
String ab = "ab";boolean result1 = (a == "a");      *// true*
boolean result2 = (ab == "ab");    *// true*
boolean result3 = (a + b == "ab"); *// false*
```

编译器和 JVM 可能会优化字符串常量，所以`result2`就是`true`。而`result3`是`false`，因为`a + b`在内存中创建了一个新的对象。所有这些都依赖于实现，并且在不同的 JVM 之间有所不同。

另一个“不太明显”的缺陷可能发生在原始包装类型上:

什么？这个也让我大吃一惊。

`[java.util.Integer](https://docs.oracle.com/javase/7/docs/api/java/lang/Integer.html#valueOf(int))`和`[java.util.Long](https://docs.oracle.com/javase/7/docs/api/java/lang/Long.html#valueOf(long))` 的`valueOf(...)`方法实际上缓存了特定范围(-128 到 127)的值，使得`a`和`b`成为同一个对象，而`c`和`d`不是。而且感谢拆箱，`equalAgain`就是`true`。

## Object.equals(Object other)和 Object hashCode()

`java.lang.Object`类为其所有子类提供了一个`equals`方法，实现非常简单:

```
public boolean equals(Object obj) {
    return (this == obj);
}
```

默认情况下，我们的每一个类型都继承了对象引用的“有问题的”比较。为了能够将`equals`用于*实际的*相等，我们需要在我们的类型中覆盖它，使[具有某些属性](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object)):

*   反身性:一个物体应该与自身相等:`obj.equals(obj) == true`。
*   对称:如果`a.equals(b) == true`，那么`b.equals(a)`也必须是`true`。
*   传递性:如果`a.equals(b) == true`和`b.equals(c) == true`，那么`a.equals(c)`应该是`true`。
*   一致:`a.equals(b)`对于未修改的对象应该总是有相同的值。
*   空处理:`a.equals(null)`应该是`false`。
*   散列码:相等的对象必须有相同的散列码。

如果我们提供自己的`equals`方法，我们也需要覆盖`hashCode`。

从 Java 7 开始，类`[java.util.Objects](https://docs.oracle.com/javase/7/docs/api/java/util/Objects.html)`为简化我们的代码提供了帮助:

注意第 19 行的类比较。我们可能倾向于使用`instanceof`来比较对象，但是这可能违反了`equals`和`hashCode`之间的一般约定:相等的对象必须有相同的散列码。

当然，我们可以设计我们的对象，即使子类也等于它们的父类。但是两者相等的定义必须相同，哈希代码计算必须发生在基类中。

类`java.util.Date`及其子类`java.sql.Date`就是这样定义的。`sql`版本没有`equal`或`hashCode`方法，基类仅从时间戳构建它的哈希代码。

再比如集合类:`java.util.ArrayList`和`java.util.LinkedList`都是`java.util.AbstractList`的子类，使用它的`equal`和`hashCode`方法。集合的相等性大多数时候是由它们内容的相等性定义的，所以使用`instanceof`而不是硬类检查似乎是合适的。

# 比较

仅仅测试平等是远远不够的。其他重要的操作是值的比较。

## 基元

像在其他语言中一样，我们可以用`<`、`>`、`<=`和`>=`操作符来比较原语的值。

浮点数据类型的同样问题也适用于它们，所以要注意。另外，`boolean`除了与`==`和`!=`相等之外，没有可比性。

## java.lang.Comparable

对象不支持这些运算符。为了比较对象类型，我们需要实现接口`[java.lang.Comparable<T>](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html)`及其单一方法`int compareTo(T)`。

`left.compareTo(right)`的结果应该如下:

```
 Result | Meaning / Order
--------|---------------------------
  0     | left is the same as right
 -1     | left < right
  1     | left > right
```

结果代表了我们这种类型的自然顺序，而不仅仅是算术上的可比性。这样，我们可以使我们类型的集合可排序。

# 最佳实践

在比较相等值或它们的自然顺序时，我们应该遵循一些简单的规则，以免得到错误的结果。

## 从不与==比较对象

只有当它是同一个对象时才有效。具有相同值的不同对象*不*相等。总是使用`boolean equals(Object other)`来比较是否相等。

## 始终执行`equals`和`hashCode if needed`

为了使类型可测试相等性，我们需要同时实现`equals` *和* `hashCode`来确保正确和一致的行为。

## 浮点数据类型并不精确

永远记住，浮点数据类型并不精确，也更难比较。

如果我们需要处理十进制数值，并且需要绝对精度，我们应该总是使用`java.util.BigDecimal`。但是要知道它的`equals`是基于它的精度:

```
BigDecimal a = new BigDecimal("2.0");
BigDecimal b = new BigDecimal("2.0");
BigDecimal c = new BigDecimal("2.00");boolean equal    = a.equals(b); // true
boolean notEqual = a.equals(c); // false
```

如果我们需要更轻松的比较，我们可以使用`compareTo`:

```
BigDecimal a = new BigDecimal("2.0");
BigDecimal b = new BigDecimal("2.0");
BigDecimal c = new BigDecimal("2.00");boolean equal = a.equals(b);    *// true*
int result    = a.compareTo(c); *// 0*
```

与原始操作相比,`BigDecimal` API 并不漂亮，主要是因为它的不变性。但那其实是好事，正确先于美丽。

## 注意自动装箱/拆箱

因为编译器在幕后完成这项工作，我们必须确保比较原语或包装对象，这要归功于`Integer` / `Long`缓存。

为了 100%确定，我们可以使用包装器类型的`Comparable<T>#compareTo(T)`,它总是使用封装的值，而不是对象引用。

# 资源

*   [原始数据类型](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html) (Oracle)
*   [自动装箱](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)(甲骨文)
*   IEEE 浮点运算标准(维基百科)
*   [Object.equals(对象其他)](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#equals(java.lang.Object))(甲骨文)
*   [可比< T >](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html) (甲骨文)