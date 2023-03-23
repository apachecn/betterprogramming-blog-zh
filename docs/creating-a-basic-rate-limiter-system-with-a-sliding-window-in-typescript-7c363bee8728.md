# 在 TypeScript 中创建带有滑动窗口的基本速率限制器系统

> 原文：<https://betterprogramming.pub/creating-a-basic-rate-limiter-system-with-a-sliding-window-in-typescript-7c363bee8728>

## 学习系统设计概念

![](img/677a2ae33ecf86c3d4c0d8c1b7fc21a7.png)

照片由[萨塔尔希·罗伊](https://unsplash.com/@sappyroy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[速率限制](https://en.wikipedia.org/wiki/Rate_limiting)是系统设计中的基本概念。我们可以用来实现速率限制的算法之一是“滑动窗口”算法。

这是一个非常基本的速率限制系统的例子，它有一个滑动窗口算法，我们用类型脚本制作了它，并逐行解释:

```
class RateLimiter {
    allowedRequests = 0;
    timeFrameSize = 0;
    queue = [];
    constructor(n: number, t: number) {
        this.allowedRequests = n;
        this.timeFrameSize = t;
    } shouldAllow(timestamp: number): boolean {
        const diff = timestamp - this.timeFrameSize;
        while (this.queue[this.queue.length - 1] <= diff ) { 
            this.queue.pop();
        }
        if(this.queue.length < this.allowedRequests){
            this.queue.unshift(timestamp)    
            return true;
        }else{
            return false
        }
    }
}
```

这个类的输入、输出和解释如下:

```
**Input**
[“RateLimiter”, “shouldAllow”, “shouldAllow”, “shouldAllow”, “shouldAllow”, “shouldAllow”]
[[3, 5], [1], [1], [2], [3], [8]]**Output**
[null, true, true, true, false, true]**Explanation**
RateLimiter rateLimiter = new RateLimiter(3, 5);
rateLimiter.shouldAllow(1); // returns True
                            // There are no previous requests, so this request is allowed.
rateLimiter.shouldAllow(1); // returns True
                            // We can allow 3 requests every 5 seconds, so this request is allowed.
                            // Timestamps of allowed requests are [1,1].
rateLimiter.shouldAllow(2); // returns True
                            // Timestamps of allowed requests are [1,1,2].
rateLimiter.shouldAllow(3); // returns False
                            // This request is not allowed because
                            // the time range [1,3] already has 3 allowed requests.
rateLimiter.shouldAllow(8); // returns True
                            // This request is allowed because
                            // the time range [4,8] does not have any allowed requests.
```

现在让我们一步一步来，看看它是如何工作的。

我们上课时有 3 个变量:

```
 allowedRequests = 0;
 timeFrameSize = 0;
 queue = [];
```

`allowedRequests`表示我们在特定的`timeFrameSize`中允许多少个请求(以秒为单位)，这两个值的默认值都是 0。

我们还有一个被初始化为空数组的`queue`，我们将使用它来监控我们当前有多少请求，以及窗口开始被设置的最后时间戳是什么。

在类构造函数中，我们将`allowedRequests`和`timeFrameSize`设置为创建类时发送的值。

现在让我们继续使用`shouldAllow`方法。在这个方法中，我们将首先跳到第 5 行:

```
if(this.queue.length < this.allowedRequests){
          this.queue.unshift(timestamp)    
            return true;
        }else{
            return false
        }
```

这很简单——如果我们不超过允许的请求数:

```
queue.length < allowedRequests
```

我们将把这个请求的时间戳添加到数组的开头:

```
this.queue.unshift(timestamp)
```

并通过返回 true 来允许这个请求。

如果`queue.length === allowedRequests`表示我们不应该在这个时间窗口中允许任何更多的请求。我们返回 false。

现在让我们回到`shouldAllow`方法的第一行:

```
const diff = timestamp — this.timeFrameSize;
while (this.queue[this.queue.length — 1] <= diff ) { 
 this.queue.pop();
}
```

所以首先我们设置一个名为`diff`的常量。

`diff`保存最后一个允许请求的时间戳，减去我们系统的`timeFrameSize`(我们在几秒钟内允许了多少个请求)。

当最后一项(我们添加到队列中的第一项)的时间戳等于或小于 diff 时，我们将它从队列中删除，并为下一个到来的请求增加空间。这就是窗口向前“滑动”的原因。

让我们以上面的例子来更清楚地理解它—输入看起来像这样:

```
[[3, 5], [1], [1], [2], [3], [8]]
```

我们允许每 5 秒钟 3 个请求(开始时的[3，5])。
在前 3 次中，队列长度是<或者等于 3(`timeFrameSize`)，所以我们允许请求并把它们添加到队列中。

现在，我们的队列如下所示:

```
[ 2, 1, 1 ]
```

这解释了为什么我们得到了如下结果:

```
[null, true, true, true, false, true]
```

第一个空值是当我们`init`类并得到`void`时，之后的 3 个子序列`true`值是允许的前 3 个请求。对于第 4 个，队列长度已经是 3，所以不是从 3 开始的<(`timeFrameSize`)。这就是为什么第四个请求为假。

在第 5 个请求中，队列中的最后一项是 1，而`diff`是 3，因此第一次:

```
this.queue[this.queue.length — 1] <= diff
```

现在，我们将删除所有小于 3 的请求(本场景中队列中的所有项目)，并且队列是空的，因此在下一个请求中…

```
queue.length < allowedRequests
```

…在这种情况下，我们允许请求并将其添加到队列中——现在队列看起来像这样`[8]`,我们允许另外 2 个请求到来。
通过这个动作，我们将窗口从时间戳 1“滑动”到时间戳 8，并从 8 开始计数 3 个请求。

# **资源**

*   L [eetcode 挑战](https://leetcode.com/explore/featured/card/system-design/689/system-design-basics/4408/)
*   L [eetcode TS 解决方案](https://leetcode.com/explore/featured/card/system-design/689/system-design-basics/4408/discuss/2302472/Typescript-solution-using-queue-with-comments.)
*   [窗口滑动技术](https://www.geeksforgeeks.org/window-sliding-technique/)
*   [速率限制/ API 节流的精彩解释](https://www.youtube.com/watch?v=9CIjoWPwAhU)