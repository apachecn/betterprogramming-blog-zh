# JDK 的实用类:集合和数组

> 原文：<https://betterprogramming.pub/utility-classes-of-the-jdk-collections-and-arrays-cc07dbcb8586>

## 一些你可能不知道的隐藏的小宝石

![](img/7ad124bcc3b887e04ce2291053537baf.png)

托马斯·西泽夫斯基在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

JDK 随着每个新版本的推出而不断发展，增加了更多功能并改进了现有功能。在表面之下是隐藏的宝石，可以使我们作为开发人员的生活更加容易。

缺失的功能通常由第三方库来补充，比如不同的 [Apache Commons](https://commons.apache.org/) 库，或者 [Google Guava](https://github.com/google/guava) 。但是随着时间的推移，他们的一些功能和想法融入了 JDK。

第一篇文章强调了实用程序类及其处理集合和数组的静态方法。

> 至少假设 Java 8。一些方法在早期版本中可能不可用。所有列出的方法都省略了`static`关键字和泛型类型定义，以减少视觉混乱。每个方法都链接到 Oracle 提供的 [Java Platform SE 8 文档](https://docs.oracle.com/javase/8/docs/api/overview-summary.html)。

# 使用集合

Java 2 引入的实用程序类`[java.util.Collections](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html)`，在 Java 5 的帮助下通过泛型得到了改进，它提供了许多静态方法来操作或返回集合。

## 不可变的空集合

当我们需要返回一个空的集合或者与集合相关的类型时，我们可以创建一个新的空实例。如果我们的代码设计允许这个实例是不可变的，我们可以使用`[Collections](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html)`来代替一个空的静态实例:

```
 Set<T> [**emptySet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptySet--)() List<T> [**emptyList**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyList--)()Map<K,V> [**emptyMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyMap--)()
 SortedSet<E> [**emptySortedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptySortedSet--)()SortedMap<K,V> [**emptySortedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptySortedMap--)() NavigableSet<E> [**emptyNavigableSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyNavigableSet--)()NavigableMap<K,V> [**emptyNavigableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyNavigableMap--)() Enumeration<T> [**emptyEnumeration**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyEnumeration--)() Iterator<T> [**emptyIterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyIterator--)()ListIterator<T> [**emptyListIterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#emptyListIterator--)()
```

## 一个

除了空集合，我们还可以返回一个只有一个元素的不可变集合:

```
 Set<T> [**singleton**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#singleton-T-)(T o) List<T> **singletonList**(T o)Map<K,V> **singletonMap**(K key,
                      V value)
```

## 不变

创建由现有集合支持的不可修改的视图。这样，我们可以使预先存在的集合不可变:

```
Collection<T> [**unmodifiableCollection**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableCollection-java.util.Collection-)(Collection<? extends T> c) Set<T> [**unmodifiableSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableSet-java.util.Set-)(Set<? extends T> s) List<T> [**unmodifiableList**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableList-java.util.List-)(List<? extends T> list) Map<K,V> [**unmodifiableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableMap-java.util.Map-)(Map<? extends K,? extends V> m) SortedSet<T> [**unmodifiableSortedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableSortedSet-java.util.SortedSet-)(SortedSet<T> s)SortedMap<K,V> [**unmodifiableSortedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableSortedMap-java.util.SortedMap-)(SortedMap<K,? extends V> m)
 NavigableSet<T> [**unmodifiableNavigableSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableNavigableSet-java.util.NavigableSet-)(NavigableSet<T> s)NavigableMap<K,V> [**unmodifiableNavigableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#unmodifiableNavigableMap-java.util.NavigableMap-)(NavigableMap<K,? extends V> m)
```

## 排序和(重新)排序

[给定集合按自然顺序排序](https://docs.oracle.com/javase/tutorial/collections/interfaces/order.html)，或者使用提供的`[Comparator](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)`:

```
void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#sort-java.util.List-)(List<T> list)void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#sort-java.util.List-java.util.Comparator-)(List<T> list,
          Comparator<? super T> c)
```

还支持与排序无关的(重新)排序，如颠倒顺序、旋转或移动所有元素以及随机调整内容:

```
void [**reverse**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#reverse-java.util.List-)(List<?> list)void [**rotate**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#rotate-java.util.List-int-)(List<?> list,
            int distance)void [**shuffle**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#shuffle-java.util.List-)(List<?> list)void [**shuffle**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#shuffle-java.util.List-java.util.Random-)(List<?> list,
             Random rnd)
```

## 二进位检索

可以使用[二分搜索法算法](https://en.wikipedia.org/wiki/Binary_search_algorithm)来定位特定元素的索引，而不是遍历集合:

```
int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#binarySearch-java.util.List-T-)(List<? extends Comparable<? super T>> list,
                 T key)int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#binarySearch-java.util.List-T-java.util.Comparator-)(List<? extends T> list,
                 T key,
                 Comparator<? super T> c)
```

## 线程安全

创建现有集合的同步(线程安全)视图:

```
Collection<T> [**synchronizedCollection**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedCollection-java.util.Collection-)(Collection<T> c)Set<T> [**synchronizedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedSet-java.util.Set-)(Set<T> s)List<T> [**synchronizedList**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedList-java.util.List-)(List<T> list)Map<K,V> [**synchronizedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedMap-java.util.Map-)(Map<K,V> m)SortedSet<T> [**synchronizedSortedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedSortedSet-java.util.SortedSet-)(SortedSet<T> s)SortedMap<K,V> [**synchronizedSortedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedSortedMap-java.util.SortedMap-)(SortedMap<K,V> m)NavigableSet<T> [**synchronizedNavigableSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedNavigableSet-java.util.NavigableSet-)(NavigableSet<T> s)NavigableMap<K,V> [**synchronizedNavigableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedNavigableMap-java.util.NavigableMap-)(NavigableMap<K,V> m)
```

为了保持线程安全，对后备集合的所有访问都必须由返回的同步集合进行。

另一个限制是，如果集合被`[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`、`[Spliterator](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`或`[Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)`遍历，则需要手动同步。遍历对象*的创建必须*在`synchronized`块内:

## 类型安全的非泛型集合

在引入泛型之前，集合类型必须处理缺乏类型安全的问题，一切都是一个`Object`。如果我们有这样一个非泛型集合的遗留代码，我们可以创建该集合的类型安全视图:

```
Collection<E> [**checkedCollection**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedCollection-java.util.Collection-java.lang.Class-)(Collection<E> c,
                                Class<E> type) Set<E> [**checkedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedSet-java.util.Set-java.lang.Class-)(Set<E> s,
                         Class<E> type) List<E> [**checkedList**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedList-java.util.List-java.lang.Class-)(List<E> list,
                          Class<E> type) Map<K,V> [**checkedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedMap-java.util.Map-java.lang.Class-java.lang.Class-)(Map<K,V> m,
                         Class<K> keyType,
                         Class<V> valueType) Queue<E> [**checkedQueue**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedQueue-java.util.Queue-java.lang.Class-)(Queue<E> queue,
                           Class<E> type) SortedSet<E> [**checkedSortedSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedSortedSet-java.util.SortedSet-java.lang.Class-)(SortedSet<E> s,
                                Class<E> type)SortedMap<K,V> [**checkedSortedMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedSortedMap-java.util.SortedMap-java.lang.Class-java.lang.Class-)(SortedMap<K,V> m,
                                Class<K> keyType,
                                Class<V> valueType) NavigableSet<E> [**checkedNavigableSet**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedNavigableSet-java.util.NavigableSet-java.lang.Class-)(NavigableSet<E> s,
                                      Class<E> type)NavigableMap<K,V> [**checkedNavigableMap**](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#checkedNavigableMap-java.util.NavigableMap-java.lang.Class-java.lang.Class-)(NavigableMap<K,V> m,
                                      Class<K> keyType,
                                      Class<V> valueType)
```

这里有一个简单的例子来说明如何使`[List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)`类型安全:

# 使用数组

实用程序类`[java.util.Arrays](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html)`类似于`[java.util.Collections](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html)`的基于数组的对应物，提供静态方法来操作、排序、复制、搜索数组等。

由于值类型和`Object`的普遍性，大多数方法可用于值类型，特别是`Object`和泛型。

为了不列出每一个方法，占位符`type[]`将作为不同类型的替代。类型`boolean`是一个特例，除非提到，否则它不会支持一个方法。

```
boolean[]  <-- Only available if explicitly stated
byte[]
char[]
int[]
long[]
short[]
long[]
float[]
double[]
Object[]
```

## 列表创建

从任意数量的元素创建列表的最简单方法是:

```
List<T> [**asList**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#asList-T...-)(T... a)
```

多亏了`[varargs](https://www.baeldung.com/java-varargs)`，我们可以提供多个元素或者一个数组:

请注意，`[List](https://docs.oracle.com/javase/8/docs/api/java/util/List.html)`实例是不可变的。为了再次创建一个可变视图，我们可以将它包装在另一个`[ArrayList](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html)`中:

## 整理

就像以前的集合一样，数组也可以排序。对于值类型，这意味着以数字顺序，对于`Object`和`<T>`，以自然顺序。

```
void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-byte:A-)(type[] a)void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-byte:A-)(type[] a)void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-T:A-)(T[] a)
```

也支持仅对数组的特定范围进行排序:

```
void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-byte:A-int-int-)(type[] a,
          int fromIndex,
          int toIndex)void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-T:A-int-int-java.util.Comparator-)(T[] a,
         int fromIndex,
         int toIndex, Comparator<? super T> c) void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-byte:A-int-int-)(type[],
                  int fromIndex,
                  int toIndex)void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-T:A-int-int-)(T[] a,
                  int fromIndex,
                  int toIndex)
```

如果自然顺序不够，可以提供定制`[Comparator](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)`:

```
void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-T:A-java.util.Comparator-)(T[] a,
          Comparator<? super T> c)void [**sort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#sort-T:A-int-int-java.util.Comparator-)(T[] a,
          int fromIndex,
          int toIndex,
          Comparator<? super T> c) void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-T:A-java.util.Comparator-)(T[] a,
                  Comparator<? super T> cmp)void [**parallelSort**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSort-T:A-int-int-java.util.Comparator-)(T[] a,
                  int fromIndex,
                  int toIndex,
                  Comparator<? super T> cmp)
```

## 流

我已经多次写到我有多爱[溪流](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184)。在`[Arrays](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html)`的帮助下，我们可以很容易地创建`[Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)<T>`和[值类型流](https://medium.com/better-programming/functional-programming-with-java-streams-f930e0e4d184#2ef1)，至少对于支持的类型:

```
 **type[]**   | **ValueTypeStream**
----------|-----------------
 int[]    | [IntStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html)
 long[]   | [LongStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/LongStream.html)
 double[] | [DoubleStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/DoubleStream.html) ValueTypeStream [**stream**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#stream-int:A-)(type[] array)ValueTypeStream [**stream**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#stream-int:A-int-int-)(type[] array,
                       int startInclusive,
                       int endExclusive) Stream<T> [**stream**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#stream-T:A-)(T[] array)Stream<T> [**stream**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#stream-T:A-int-int-)(T[] array,
                 int startInclusive,
                 int endExclusive)
```

## 分裂器

Java 8 引入了`[java.util.Spliterator](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`接口，这是一个基本的流实用程序。它用于遍历和划分序列。

分割符也根据它们的值类型划分了子类，和以前一样，只支持三种数值类型:

```
 **type[]**   | **ValueTypeSpliterator**
----------|---------------------
 int[]    | [Spliterator.OfInt](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfInt.html)
 long[]   | [Spliterator.OfLong](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfLong.html)
 double[] | [Spliterator.OfDouble](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfDouble.html) ValueTypeSpliterator [**spliterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#spliterator-int:A-)(type[] array)ValueTypeSpliterator [**spliterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#spliterator-int:A-int-int-)(type[] array,
                                 int startInclusive,
                                 int endExclusive) Spliterator<T> [**spliterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#spliterator-T:A-)(T[] array)Spliterator<T> [**spliterator**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#spliterator-T:A-int-int-)(T[] array,
                           int startInclusive, 
                           int endExclusive)
```

## 字符串表示

为了更好地调试或记录输出，我们可以创建数组的字符串表示。除了上面列出的类型，还支持`boolean`。`Object[]`可能包含其他数组，因此也有一个用于深度*字符串表示的方法:*

```
String [**toString**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#toString-boolean:A-)(type[] a)String [**deepToString**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#deepToString-java.lang.Object:A-)(Object[] a)
```

除了调试或简单的显示目的，我们不应该使用由`toString()`方法生成的字符串表示。实际的实现可能会随着时间的推移而改变，或者不同的 JDK 对它的处理可能不完全相同。

## 平等

像字符串表示一样，`boolean`也受支持，我们可以检查`Object[]`的*深度*相等:

```
boolean [**equals**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#equals-boolean:A-boolean:A-)(type[] a,
               type[] a2)boolean [**deepEquals**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#deepEquals-java.lang.Object:A-java.lang.Object:A-)(Object[] a1,
                   Object[] a2)
```

## 哈希码

计算 hashcode 还支持`boolean`和*深度*计算:

```
int [**hashCode**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#hashCode-boolean:A-)(type[] a,
             type[] a2)int [**deepHashCode**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#deepHashCode-java.lang.Object:A-)(Object[] a1,
                 Object[] a2)
```

## 二进位检索

使用[二分搜索法算法](https://en.wikipedia.org/wiki/Binary_search_algorithm)查找数组中特定元素的索引:

```
int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-byte:A-byte-)(type[] a,
                 type key)int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-byte:A-int-int-byte-)(type[] a,
                 int fromIndex,
                 int toIndex,
                 type key) int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-T:A-T-java.util.Comparator-)(T[] a,
                 T key,
                 Comparator<? super T> c)int [**binarySearch**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#binarySearch-T:A-int-int-T-java.util.Comparator-)(T[] a,
                 int fromIndex,
                 int toIndex,
                 T key,
                 Comparator<? super T> c)
```

## 复制

我们可以将任何数组复制到特定的长度，或者截断它，或者用适当的未初始化的值填充新的空间，或者`null`。支持值类型`boolean`:

```
type[] [**copyOf**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOf-boolean:A-int-)(type[] original,
              int newLength) T[] [**copyOf**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOf-T:A-int-)(T[] original,
              int newLength) T[] [**copyOf**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOf-U:A-int-java.lang.Class-)(U[] original,
              int newLength,
              Class<? extends T[]> newType) type[] [**copyOfRange**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOfRange-boolean:A-int-int-)(type[] original,
                   int from,
                   int to) T[] [**copyOfRange**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOfRange-T:A-int-int-)(T[] original,
                   int from,
                   int to) T[] [**copyOfRange**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#copyOfRange-U:A-int-int-java.lang.Class-)(U[] original, 
                   int from,
                   int to,
                   Class<? extends T[]> newType)
```

## 填充和设置

用提供的值填充数组或指定的范围。支持值类型`boolean`:

```
void [**fill**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#fill-boolean:A-boolean-)(type[] a,
          type val)void [**fill**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#fill-boolean:A-int-int-boolean-)(type[] a,
          int fromIndex,
          int toIndex,
          type val)
```

如果我们需要对值进行更多的控制，可以提供一个生成器函数:

```
 **type[]**   | **ValueGenerator**
----------|---------------------
 int[]    | [IntUnaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntUnaryOperator.html)
 long[]   | [IntToLongFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntToLongFunction.html)
 double[] | [IntToDoubleFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntToDoubleFunction.html) void [**setAll**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#setAll-int:A-java.util.function.IntUnaryOperator-)(type[] array,
            ValueGenerator generator)void [**setAll**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#setAll-T:A-java.util.function.IntFunction-)(T[] array,
            IntFunction<? extends T> generator) void [**parallelSetAll**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSetAll-int:A-java.util.function.IntUnaryOperator-)(type[] array,
                    ValueGenerator generator)void [**parallelSetAll**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelSetAll-T:A-java.util.function.IntFunction-)(T[] array,
                    IntFunction<? extends T> generator)
```

## 平行前缀

`parallelPrefixing`方法并行累积数组的每个元素。对于较大的阵列，这种操作通常比顺序处理更有效:

```
 **type[]**   | **ValueOperator**
----------|---------------------
 int[]    | [IntBinaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/function/IntBinaryOperator.html)
 long[]   | [LongBinaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-long:A-java.util.function.LongBinaryOperator-)
 double[] | [DoubleBinaryOperator](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-double:A-java.util.function.DoubleBinaryOperator-) void [**parallelPrefix**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-int:A-java.util.function.IntBinaryOperator-)(type[] array,
                    ValueOperator op)void [**parallelPrefix**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-T:A-java.util.function.BinaryOperator-)(T[] array,
                    BinaryOperator<T> op) void [**parallelPrefix**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-int:A-int-int-java.util.function.IntBinaryOperator-)(type[] array,
                    int fromIndex,
                    int toIndex,
                    ValueOperator op)void [**parallelPrefix**](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html#parallelPrefix-T:A-int-int-java.util.function.BinaryOperator-)(T[] array,
                    int fromIndex,
                    int toIndex,
                    BinaryOperator<T> op)
```

如果你以前没有用过这种方法，可能不清楚它到底有什么作用，至少我的经验是这样的。下面举个例子来解释一下:

# 结论

许多我们可能自己编码或使用第三方库的常见任务，可以直接用 JDK 安全地完成。这样，我们依赖更少的外部因素，不会通过编写额外的代码引入错误，并且以简洁、一致和可重复的方式处理常见任务。

下一部分将介绍用于处理 I/O 的实用程序类。

# 资源

*   `[java.util.Arrays](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html)`
*   `[java.util.Collections](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html)`