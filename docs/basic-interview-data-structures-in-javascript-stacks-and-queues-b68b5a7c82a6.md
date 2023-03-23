# JavaScript 中的基本访问数据结构:堆栈和队列

> 原文：<https://betterprogramming.pub/basic-interview-data-structures-in-javascript-stacks-and-queues-b68b5a7c82a6>

## 后进先出与先进先出

![](img/781295b107e80488482d43bb4a7ef3e3.png)

欢迎阅读我的后续文章“[JavaScript](https://medium.com/@baum.johannes/basic-interview-data-structures-in-javascript-509003c751fe)中的基本面试数据结构”，在这篇文章中，我描述了数组、数组列表/向量、哈希映射和哈希集的 JavaScript 实现，供编写面试代码时使用。本文主要关注堆栈和队列。

堆栈和队列是面试题中常用的，使用它们可以非常高效地解决某些问题。依赖堆栈和队列的两个非常著名的算法是*深度优先搜索*和*宽度优先搜索*，两者都用于遍历一个图。

# 大量

栈是一种所谓的*后进先出(lifo)* 数据结构，工作起来就像一堆盘子。你可以很容易地把盘子放在盘子堆的顶部，但是如果你想从底部拿一个盘子，你首先必须把它上面的所有盘子都拿走。

正如在我的上一篇文章中一样，我将给出以下操作的运行时复杂度:

*   **插入**(推):O(1)
*   **随机存取**(存取任意元素):O(n)*
*   **先访问** (peek —访问最近添加的元素):O(1)
*   **查找**(查找特定元素):O(n)
*   **随机删除**(删除任意元素):O(n)
*   **删除第一个** (pop —删除最近添加的元素):O(1)

**注:**如果使用 JavaScript 数组作为堆栈，随机访问可以在 O(1)中完成。

栈的重要操作有`insert (push)`、`remove first (pop)`和`access first (peek)`。这些是栈的有效操作。如果你发现自己经常使用不同的操作，那么栈可能不是你想要的数据结构。

## JavaScript 中的堆栈

JavaScript 自带动态数组。这些可以非常容易地用作堆栈:

```
const stack = [];// insert elements (push)
stack.push('one'); 
stack.push('two'); console.log(stack); // ['one', 'two']// access first (peek)
console.log(stack[stack.length-1]); // "two"// remove first (pop)
stack.pop();console.log(stack); // ['one']
```

注意，因为我们使用的是 JS 数组，所以我们可以访问 O(1) 中的任何元素，而不仅仅是最后一个。

# 行列

队列是一种所谓的*先进先出(fifo)* 数据结构，其工作方式类似于电影院前面的队列。第一个到达电影院的人将是第一个得到票的人。另一方面，最后到达的人也将是最后收到票的人。

正如在我的上一篇文章中，我将为您提供以下操作的运行时复杂性的[:](https://medium.freecodecamp.org/all-you-need-to-know-about-big-o-notation-to-crack-your-next-coding-interview-9d575e7eec4)

*   **插入**(入队):O(1)
*   **随机存取**(存取任意元素):O(n)
*   **访问最后一个**(访问最近最少添加的元素):O(1)
*   **查找**(查找特定元素):O(n)
*   **随机删除**(删除任意元素):O(n)
*   **删除最后一个**(出列—删除最近最少添加的元素):O(1)

队列的基本和有效操作是插入(入队)、最后移除(出队)和最后访问(查看)。类似于栈，如果不需要上面提到的操作，就不应该使用这种数据结构。

## JavaScript 中的队列

您可以用 JavaScript 数组实现一个队列，如下所示:

```
const queue = [];// insert elements (enqueue)
queue.push('one'); 
queue.push('two');console.log(queue); // ['one', 'two']// access last
console.log(queue[0]); // "one"// remove last (dequeue)
queue.shift();console.log(queue); // ['two']
```

然而，这种解决方案有一个缺点:`shift` 在最坏的情况下需要 O(n) 因为所有的数组元素都必须复制一个位置。

有几种第三方解决方案可以有效地实现队列。然而，你不能在面试中使用它们。你应该告诉你的面试官你知道 JavaScript 中的这个限制，并澄清你是否可以简单地使用数组作为队列，假装`shift` 只需要 O(1) *。如果这不是一个选项，你可以使用链表有效地实现一个队列(以及一个堆栈)。*

然而，您也可以从头开始实现队列。为此，您需要以下数据结构:

```
const Node = data => ({ data, next: null, prev: null });
```

正如你所看到的，这看起来类似于一个双向链表(参见[我之前关于链表的文章](https://medium.com/@baum.johannes/basic-interview-data-structures-in-javascript-linked-lists-983e169db2e5))。

我们将其嵌入到队列工厂函数中:

```
const Queue = () => {
  const Node = data => ({ data, next: null, prev: null }); let head = null;
  let tail = null; ... return {
    enqueue,
    dequeue,
    peek,
  };
};
```

我们需要这个工厂函数，以便有一个包含队列头部和尾部的闭包。

## 插入(排队)

要向队列中插入一个新元素，只需将它作为列表式数据结构的新头部:

```
const enqueue = data => {
  if (head == null) {
    head = Node(data);
    tail = head;
    return head;
  } const newNode = Node(data);
  newNode.next = head;
  head.prev = newNode;
  head = newNode;
  return head;
};
```

## **移除最后一个**(出列)

从队列中删除最近添加的元素可以通过返回并删除列表的尾部来完成:

```
const dequeue = () => {
  if (tail === null) return null; const tailData = tail.data;
  if (tail.prev === null) {
    tail = null;
    head = null;
    return tailData;
  } tail.prev.next = null;
  tail = tail.prev;
  return tailData;
};
```

这相当简单，但是我们必须小心空指针。

## **访问最后一个(peek)**

要访问队列中最近使用的元素，我们只需返回列表的尾部。我们只需要考虑尾部为空的情况:

```
const peek = () => tail ? tail.data : null;
```

# 结论

堆栈和队列是编码面试中非常常见和有用的数据结构。每当您需要大量访问和丢弃最近添加/最少添加的元素时，您应该考虑使用堆栈/队列。

像 Java 这样的许多语言在它们的标准库中都包含了链表实现。因此，在 Java 中可以使用链表作为堆栈和队列。出于这个原因，你应该问你的面试官，为了简单起见，你是否可以只使用 JavaScript 数组作为队列，而忽略 shift 操作的运行时间。在大多数情况下，只要面试是语言不可知的，你的面试官会允许的。

我希望这能帮助你找到下一份工作。祝你面试好运！

# 参考

*   [JavaScript 中的基本面试数据结构](https://medium.com/@baum.johannes/basic-interview-data-structures-in-javascript-509003c751fe)
*   [你所需要知道的关于“大 O 符号”的所有知识将帮助你破解下一次编码面试](https://medium.freecodecamp.org/all-you-need-to-know-about-big-o-notation-to-crack-your-next-coding-interview-9d575e7eec4)
*   [JavaScript 中的基本面试数据结构—链表](https://medium.com/@baum.johannes/basic-interview-data-structures-in-javascript-linked-lists-983e169db2e5)