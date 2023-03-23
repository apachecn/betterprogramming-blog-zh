# 19 个 Java 集合类及示例

> 原文：<https://betterprogramming.pub/19-java-collection-classes-with-examples-1597f106d0f2>

## **每个 Java 程序员应该知道的基本概念**

![](img/0d8ec09ae9839e5fde905b81c894fa4d.png)

照片由[像素](https://www.pexels.com/photo/abstract-close-up-cobweb-connection-276502/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的 [Pixabay](https://www.pexels.com/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

在这篇文章中，我将展示用于在 Java 中处理数据的集合框架的主要方法。这个框架提供了一个架构，用于通过不同的接口和实现来存储和操作不同的对象。

## 目录

列表界面在`Java.util`包中定义。它包括基于索引的方法来插入、更新、删除和搜索元素。它也可以有重复元素和空元素。

```
public interface List<E> extends Collection<E>
```

## 数组-数组列表

## **数组**

Array 类是 List 接口的实现。这个类可以包含多个值，不像常规变量只包含一个值。

示例:

```
//Need to specify the size for array
**int**[] array1 = 
 new int[4];array1[0] = 1;
array1[1] = 2;
array1[2] = 3;
array1[3] = 4;
```

## **数组列表**

List 接口的`ArrayList`实现是使用最广泛的实现之一，并且不需要同步访问。

在这两种情况下，我们都可以获得位于索引位置的任何数据。

示例:

```
//It is not necessary to specify the size
ArrayList<Integer> array2 =
 **new** ArrayList<Integer>();array2.add(1);
array2.add(2);
```

一个`Array`是固定长度的数据结构，而`ArrayList`是可变长度的集合类。

我们不能在`ArrayList`中存储原语；它只能存储对象。但是数组可以包含原语和对象。

## 矢量

```
public class Vector<E>  extends Object<E>  implements List<E>, Cloneable, Serializable
```

这个类用于实现列表接口。尽管如此，它还是很少被使用，因为它是一个总是被同步访问的列表结构，这在许多情况下会降低性能。

我们可以存储 n 个元素，因为没有大小限制。

这个实现包含许多不属于集合框架的遗留方法。

示例:

```
//Create Integer type vector
Vector<Integer> vector=
 new Vector<>();vector.add(1);//Using index number
vector.add(2, 2);
```

## 堆

```
public class Stack<E>extends [Vector](https://docs.oracle.com/javase/7/docs/api/java/util/Vector.html)<E>
```

Stack 类是 LIFO(后进先出)的一个类，它实现了 List 类，并扩展了 de Vector 类的五个操作，允许将一个 Vector 视为一个堆栈。

基本操作是 push(将元素推送到堆栈上)、pop(将元素弹出堆栈)、peek(查询堆栈顶部的第一个元素)、empty(检查堆栈是否为空)和 search(搜索堆栈中的给定元素并返回其在堆栈中的位置)。

示例:

```
Stack<Integer> stack =
 **new** Stack<Integer>();stack.push(1);
stack.pop();//1
```

## 链接列表

```
public class LinkedList<E> extends AbstractSequentialList<E> implements List<E>, Deque<E>, Cloneable, Serializable
```

这个类几乎与`ArrayList`相同，因为它们实现了 List 接口。我们可以类似地添加、修改和删除项目，但是它们的构建方式不同。

`ArrayList`类内部有一个常规数组。当添加一个元素时，它被放入数组中。如果数组的大小不够，就会创建一个新的更大的数组来替换旧的数组，然后删除旧的数组。`LinkedList`使用双向链表来存储元素。

这种实现在操作数据时具有更好的性能，因为它使用双向链表，所以在内存中不需要移位。另一方面，`ArrayList`更适合存储和访问数据。

两者都是非同步类。

示例:

```
LinkedList<String> l 
 = new LinkedList<String>();l.add("a");
l.add("b");//Iterating LinkedList
Iterator<String> iterator = l.iterator();
while(iterator.hasNext()){
  System.out.println(iterator.next());
  //a, b
}
```

## 地图

```
public interface Map<K,V>
```

Map 接口用于匹配键值和值或对象。地图支持任何对象。但是，映射不能有重复的键。

请记住，equals 和`hashCode`方法在通用地图上没有帮助，所以您必须覆盖它们以使它们根据您的需要工作。

一个`Map`不能被遍历，所以你需要用`entrySet`方法或者`keySet`方法把它转换成 Set。

## 散列表

```
public class HashMap<K,V> extends AbstractMap<K,V>                          implements Map<K,V>, Cloneable, Serializable
```

A `HashMap`是 Map 接口的实现之一；这个接口是一种集合，它存储将键与值相关联的数据，但是它不维护任何顺序。

`HashMap`的工作原理是通过 Java `hashCode()`方法为一个键分配一个位置。

示例:

```
HashMap<String, Integer> numbers 
= new HashMap<String, Integer>();numbers.put("one", 1)
numbers.put("two", 2)System.out.println(numbers.get("one"));
//1
```

## LinkedHashMap

```
public class LinkedHashMap<K,​V> extends HashMap<K,​V> implements Map<K,​V>
```

`LinkedHashMap`实现扩展实现 Map 接口的`HashMap`类。它保持插入顺序。

`LinkedHashMap`与`HashMap`相似，但增加了一个保持插入顺序的特征。

示例:

```
LinkedHashMap<Integer, String> lmap 
= new LinkedHashMap<>();lmap.put(1, null);
lmap.put(2, "two");System.out.println(lmap.get(1));
//null
```

## 分类地图

```
public interface SortedMap<K, V> extends Map<K, V>
```

这个界面非常类似于地图界面。然而，它的不同之处在于`SortedMap`允许收集组中的元素被完全排序，使得搜索和查询中的访问更快。

这些元素按其关键元素排序。

## 树形图

```
public class TreeMap<K, V> extends AbstractMap<K, V> implements NavigableMap<K, V>, Cloneable, Serializable;public interface NavigableMap<K,V> extends SortedMap<K,V>;
```

`TreeMap`基于一个允许有序映射的树形实现。它保持升序。

`TreeMap`在 Java 中不允许空键(像 Map)。但是，多个空值可以与不同的键相关联。

示例:

```
Example:SortedMap<Integer, String> tm
            = new TreeMap<Integer, String>();tm.put(new Integer(2), "a");
tm.put(new Integer(3), "b");
tm.put(new Integer(5), "c");
tm.put(new Integer(4), "d");
tm.put(new Integer(1), "e");Set s = tm.entrySet();
Iterator i = s.iterator();while (i.hasNext()) {
        Map.Entry m = (Map.Entry)i.next();  
        System.out.println((String)m.getValue());
}//e
//a
//b
//d
//c
```

## 并发哈希表

```
public class ConcurrentHashMap<K, V> extends AbstractMap<K, V> implements ConcurrentMap<K, V>, Serializable
```

`ConcurrenthashMap`与`HashMap`相似，但不同之处在于，如果被多个线程使用，它是可靠的。因此，它是线程安全的，也就是说，多个线程可以对一个对象进行操作，而没有任何复杂性。

在`ConcurrentHashMap`中插入空对象作为键或值是不可能的。

示例:

```
ConcurrentHashMap<String, Integer> days = 
new ConcurrentHashMap<String, Integer>();days.put("monday", Integer.valueOf(1));
days.put("saturday", Integer.valueOf(2));System.out.println(days.get("monday"));
//1
```

## 一组

```
public interface Set<E> extends Collection<E>
```

`Set`接口定义了一个不能包含重复元素的`Collection`。

与列表相比，集合中的元素没有保证的内部顺序。然而，列表中的元素有内部顺序，我们可以使用这个顺序迭代它们。

## 哈希特

```
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, Serializable
```

这个类实现了 Set 接口，并通过哈希结构定义了 Set(不重复元素的组)的概念。

不能保证我们在`HashSet`中插入的对象会以相同的顺序插入。这是因为对象是根据它们的哈希代码插入的。

一个`HashSet`不允许重复的对象，但是允许空元素。

示例:

```
HashSet<String> hset =**new** HashSet();
hset.add("b");    
hset.add("a");//This will not add new element as "a" already exists
hset.add("a");
hset.add("c");Iterator<String> i=hset.iterator();  
while(i.hasNext()) {  
  System.out.println(i.next());  
}
//a
//c
//b
```

## LinkedHashSet

```
public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, Serializable
```

这个类扩展了`HashSet`类，并通过在等式中添加一个双向链表来定义集合概念，以确保元素总是以相同的方式被遍历。

所以`LinkedHashSet`是`HashSet`的排序版本。

示例:

```
LinkedHashSet<String> linkedset
            = new LinkedHashSet<String>();linkedset.add("A");
linkedset.add("B");
linkedset.add("C");System.out.println(linkedset);
[A, B, C]
```

## 分类集合

```
public interface SortedSet<E> extends Set<E>
```

`SortedSet`接口扩展了`Set`接口，并声明了按升序排序的集合的行为。

## 树集

```
public class TreeSet<E> extends AbstractSet<E> implements NavigableSet<E>, Cloneable, Serializable
```

这个类使用一个树形结构，允许元素按照自然顺序或者由一个`Comparator`定义的顺序进行排序。此外，该类有助于比较元素，如大于或小于。

示例:

```
SortedSet<String> ts
    = new TreeSet<String>();ts.add("A");
ts.add("C");
ts.add("B");Iterator<String> i = ts.iterator();
while (i.hasNext()){
  System.out.println(i.next());
}//A
//B
//C
```

## 长队

```
public interface Queue<E> extends Collection<E>
```

`Queue`接口按照 FIFO(先进先出)顺序存储和处理项目。

这是一个有序的对象列表，其用途仅限于按照 FIFO(先入先出)原则在列表末尾插入元素和从`List`开头删除元素。

## 优先级队列

```
public class PriorityQueue<E> extends AbstractQueue<E> implements Serializable
```

该类根据自然顺序对元素进行排序，如通过`Comparable`方法或通过定义`Comparator`对象所指定的。这个类允许按顺序插入和从前面删除。插入是根据优先级完成的，因此优先级最高的元素放在最前面。

最常见的操作是:

*   提供:根据优先权插入一个位置。
*   投票:用于拆包。
*   偷看:在队列中得到第一个。
*   清除:删除队列中的所有元素。
*   size:获取队列中元素的数量。

示例:

```
PriorityQueue<Integer> pQueue 
  =new PriorityQueue<Integer>();pQueue.add(1);
pQueue.add(2);
pQueue.add(3);//Printing the top element of PriorityQueue
System.out.println(pQueue.peek());//1//Printing the top element and removing it.
System.out.println(pQueue.poll());
//1//Printing the top element again
System.out.println(pQueue.peek());
//2
```

## 并发链接队列

```
public class ConcurrentLinkedQueue<E> extends AbstractQueue<E> implements Queue<E>, Serializable
```

这个类实现了队列接口和排序元素 FIFO(先进先出)。

当许多线程共享对一个公共集合的访问时，`ConcurrentLinkedQueue`是合适的，因为当队列为空并返回 null *时，它不会阻塞访问线程。*

该类不允许空元素。

## 阻塞队列

```
public interface BlockingQueue<E> extends Queue<E>
```

这个接口扩展了`Queue`接口。该接口旨在允许任何操作等待，直到它可以成功执行。

例如，如果我们想从空队列中删除一个项目，这种类型的队列允许删除操作等待，直到队列包含要删除的项目。

## **ArrayBlockingQueue**

```
public class ArrayBlockingQueue<E> extends AbstractQueue<E> implements BlockingQueue<E>, Serializable
```

`ArrayBlockingQueue`类实现了`BlockingQueue`接口。

这个阻塞队列是一个固定大小的队列。因此，试图将一个项目放入满队列将会阻止操作。类似地，从空队列中获取元素的尝试也会被阻止。

## LinkedBlockingQueue

```
public class LinkedBlockingQueue<E> extends AbstractQueue<E> implements BlockingQueue<E>, Serializable
```

这个类也实现了`BlockingQueue`接口。

`LinkedBlockingQueue`将元素保持在一个链接的结构中。`LinkedBlockingQueue`按照 FIFO(先入先出)顺序存储元素。

`LinkedBlockingQueue`是阻塞队列。因此，当队列为空时，它会阻塞访问线程。

如果你喜欢这篇文章，考虑通过我的[个人资料](https://kesk.medium.com/membership)订阅 Medium。谢谢大家！

# 参考

[https://docs.oracle.com/en/](https://docs.oracle.com/en/)