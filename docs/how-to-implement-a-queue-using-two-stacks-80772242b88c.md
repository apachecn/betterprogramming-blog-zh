# 如何使用两个堆栈实现队列

> 原文：<https://betterprogramming.pub/how-to-implement-a-queue-using-two-stacks-80772242b88c>

## 一个经典编码面试问题的 Python 解决方案

![](img/4c191c5c76f060726dc939197c389986.png)

[女同胞](https://unsplash.com/@cowomen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/interview?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

我之前发表了一篇博客([# push elements into stack1
# this is the enqueue function (entering the queue)# check if stack1 is empty
# if it isn't, pop the last element and push it into stack2
# repeat until stack1 is empty
# this is the dequeue function (leaving the queue)# once stack1 is empty, check if stack2 is empty
# if it isn't, pop the last element and return it
# repeat until stack2 is empty](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)

[Python 代码:](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)

```
class Queue_Two_Stacks():

    def __init__(self):
        self.stack_1 = []
        self.stack_2 = []

    def enqueue(self, item):
        self.stack_1.append(item)

    def dequeue(self): if len(self.stack_2) == 0: # If stack_1 is empty, raise an error
            if len(self.stack_1) == 0:
                raise IndexError("Can't dequeue from empty queue!")

        # while stack_1 is not empty, 
        # move items from stack_1 to stack_2, reversing order
            while len(self.stack_1) > 0:
                last_stack_1_item = self.stack_1.pop()
                self.stack_2.append(last_stack_1_item) # return the last item in stack_2, which is the first
        # item that entered stack_1 (FIFO!) return self.stack_2.pop()
```

[](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)

# [时间复杂度/大 O](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)

[所以我们有一个完整的解决方案！但是运行时呢？最简单的方法是考虑每个元素的时间成本。就单个项目的成本而言，最坏的情况是它先入队，然后再出队。在这种情况下，物品进入`stack_1`(成本为一次推送)，然后移动到`stack_2`(成本为一次弹出和一次推送)。稍后，该物品从`stack_2`中移除并被退回(花费一次 pop)。这四个 pushes 和 pop 中的每一个都是`O(1)`，这意味着访问堆栈中的单个元素不会根据数据集的大小而改变。我们只需要抓取最后一个元素，不需要遍历数组。所以我们的每项总成本是`O(1)` *，*，而我们整个算法的运行时间是`O(n)`。随着我们添加更多的元素，算法将需要更长的时间来运行，因为运行时间与数据集中的元素数量成正比。](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)

[](https://medium.com/@krbarounis/a-primer-on-stacks-and-queues-f40dda17604d# create two stacks: stack1, stack2</span><span id=)