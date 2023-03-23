# 用 JavaScript 实现队列

> 原文：<https://betterprogramming.pub/implementing-a-queue-in-javascript-59b332c7ff0d>

## 看看队列数据结构

![](img/2c5b63dd333591f78293bf3f9842946b.png)

图片由 [Levi Jones](https://unsplash.com/@levidjones?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 UnSplash 上拍摄

队列是一个列表，类似于真实世界中的队列。最先进入队列的人是最先从队列中移除的人。

从技术上讲，这个过程叫做先进先出(FIFO)。首先插入的元素将首先从列表中删除

元素总是被添加到列表的末尾，并从列表的前面移除。

`Queue`中的不同操作有:

*   `Enqueue` →向队列中添加一个元素
*   `Dequeue` →从队列中删除一个元素

我们将要实施的操作:

*   在队列末尾插入一个元素
*   从队列中删除前面的元素
*   获取前端元素
*   获取最后一个元素
*   检查队列是否为空
*   打印队列的元素
*   打印队列的长度

让我们创建一个`Queue`类。

```
class Queue {

   constructor(){

     this.data = [];
     this.rear = 0;
     this.size = 10; }}
```

`data` →是我们存储元素的列表

`rear` →用于存储下一个元素的存储位置

`size` →一个队列可以拥有的最大元素数量

# 入队:在队列末尾插入一个元素

在向队列中插入一个元素后，我们需要将`rear`值增加`1`，这样`rear`就指向下一个新元素将被插入的位置。

```
enqueue(element) { this.data[this.rear] = element;
     this.rear = this.rear + 1;}
```

除了上述情况，我们需要检查队列是否已满，以避免溢出。

```
enqueue(element) {

    if(this.rear < this.size ) { this.data[this.rear] = element;
          this.rear = this.rear + 1;
     }}
```

# Length:返回队列的长度

为了获得队列的长度，我们可以使用`rear`属性。

```
 length() {

 **return this.rear;** } 
```

# IsEmpty:检查队列是否为空

如果`rear`指向`0`，那么我们可以说队列是`empty`。因为`rear`指向新元素将被放置的位置，如果`rear`指向 0，则队列中没有元素

```
isEmpty() {

    return this.rear === 0;}
```

# GetFront:获取队列的前端元素

除了获取前面的元素，这将检查队列是否为空。

```
getFront() {

    if(this.isEmpty() === false) { return this.data[0]; }}
```

# GetLast:获取添加到队列中的最后一个元素

我们知道`rear`值指向新的位置，下一个元素将被插入到队列中。因此，为了获得队列中的最后一个元素，我们可以将索引`rear`减少`1`。

```
getLast() {

     if(this.isEmpty() === false) {

          return this.data[ **this.rear - 1** ] ;
     }}
```

# 删除:从队列中删除前面的元素

最先插入的元素是最先删除的元素。因此，我们可以删除前面的元素，并将`rear`值减少`1`，这样后面的元素将指向下一个正确插入的位置。

```
dequeue() {

     if(this.isEmpty() === false) {

          **this.rear = this.rear-1;**
 **return this.data.shift();
     }**}
```

# 打印队列的元素

我们可以打印从`0`索引到`queue`的`rear-1`索引的队列元素。

```
print() {

   for(let i =0; i < this.rear; i++) {
      console.log(this.data[i]);
    }}
```

# 重置队列

删除队列的所有元素，并将`rear`设置为`0`。

```
clear() {
   this.data.length = 0;
   this.rear = 0;}
```

# 结束了

让我们把上面所有的代码结合在一起

感谢阅读。我希望你喜欢这篇文章。