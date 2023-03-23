# Python 的 GIL 与带互斥体的 C++

> 原文：<https://betterprogramming.pub/pythons-gil-vs-c-with-mutexes-301b244e42>

## Python 的 GIL 通过一个多线程应用实例进行了解释

![](img/5ab206bb2a904df1039d02a85e5f7105.png)

免费视频上 rawpixel.com 的图片

本文将关注 Python 的全局解释器锁——GIL。我们将讨论它是什么，为什么它在那里，它带来了什么优点/缺点，然后用一个实际的例子结束这篇文章。我们比较了 Python 和 C++中的一个简单多线程应用程序，并展示了 GIL 如何有效地将任何多线程 Python 应用程序转变为单核应用程序。

# 什么是 GIL？

全局解释器锁，缩写为 GIL，是 Python 中的中央解释器锁。执行 Python 字节码的任何一行都需要获得这个锁。这实质上使得任何 Python 程序都是单线程的——也就是说，如果我们有一个受 CPU 限制的程序，旋转多个线程不会使它更快。

# 为什么引入了 GIL？

Python 使用引用计数进行垃圾收集:它计算当前程序执行中对任何对象的引用数，当达到 0 时销毁该对象。我们会在多线程应用中遇到竞争情况，以及错误更新这些引用计数器的危险。一种解决方案是给每一个对象或每一组对象加锁，但这也带来了其他缺点，因此引入了一个单一的中央锁——GIL。

# GIL 的优点和缺点是什么？

首先，与其他垃圾收集方法相比，GIL 易于实现，并且在单线程应用程序中表现非常好。此外，例如，非线程安全的 C 库可以更容易地被集成。所有这些都使得 GIL 非常适合 Python，并帮助这种语言今天获得了流行。

然而，你可能已经从上面的字里行间看出，GIL 不仅有优势，也有劣势。特别是，它本质上把任何 Python 程序变成了单线程程序。这是巨大的，尤其是在当今世界，越来越多的应用被并行化，机器配备了越来越多的计算能力。

至于为什么 GIL 没有被删除，我不想评论更多，除了它有它的缺点和优点，如上所述。如果你感兴趣，更多信息请点击[这里](https://realpython.com/python-gil/)。

# 行动中的 GIL

现在让我们从理论上实际测试一下上面描述的东西。为此，我们实现了一个简单的多线程程序，共同计数到 100K。我们在 Python 和 C++中分别做了一次——显示了这样的 C++程序在使用更多线程时变得更快，因为使用了多个内核，而 Python 不是这样。

这是 Python 代码:

```
import threading

MAX_COUNT = 50000

counter = 0
lock = threading.Lock()

# Function which iteratively increases the global variable counter up to max_count.
def count_to(max_count):
    global counter
    for _ in range(max_count):
        lock.acquire()
        counter += 1
        lock.release()

# Initialize two counter threads.
t1 = threading.Thread(target=count_to, args=(MAX_COUNT,))
t2 = threading.Thread(target=count_to, args=(MAX_COUNT,))
# Start them.
t1.start()
t2.start()
# Join them, i.e. wait for them to finish.
t1.join()
t2.join()

print(f"Counting done, reached: {counter}")
```

我们定义一个函数`count_to(N)`，它迭代地增加一个全局变量计数器到`N`，同时保持一个锁。注意，我们必须对 counter 使用 global 关键字，而不是对 lock 使用，是因为后者是可变的(比较[这篇文章](https://medium.com/@hrmnmichaels/variable-scopes-in-python-f32c7c640715))。然后，我们用参数 50K 启动两个线程运行这个函数。因此，总的来说，我们期望`counter`最终等于 100K，事实就是如此。

注意，即使对于 Python 来说，在前面介绍了 GIL 之后，我们仍然需要锁定:GIL 只保证在每个时间步长只有一个 Python 线程被执行——但是不能保证上下文切换何时发生。特别是，增量操作符在 Python 中不是原子的——因此，如果我们运气不好，解释器可能会决定在这个操作中切换上下文，产生错误的最终结果。

公平地说，对于这个简单的例子，即使有许多线程，并且计数到相对较高的数字时，我也不能强制这种行为。然而，不手动锁定相互代码和资源是危险的，也是错误的。很容易想象更复杂的场景，其中错误的行为实际上显示。我们可以通过人为分割和减慢执行来强制执行，例如:

```
tmp = counter
tmp += 1
time.sleep(0.000001)
counter = tmp
```

相应的 C++代码如下所示:

```
#include <iostream>
#include <mutex>
#include <thread>

constexpr int kMaxCount = 50000;

int counter = 0;
std::mutex counter_mutex;

// Function which iteratively increases the global variable counter up to
// max_count.
void countTo(int max_count) {
  for (int i = 0; i < max_count; i++) {
    {
      std::lock_guard<std::mutex> guard(counter_mutex);
      counter++;
    }
  }
}

int main() {
  // Start two threads counting to kMaxCount.
  std::thread t1(countTo, kMaxCount);
  std::thread t2(countTo, kMaxCount);
  // Join these threads (wait for them to finish).
  t1.join();
  t2.join();
  std::cout << "Counting done, reached: " << counter;
  return 0;
}
```

# GIL 导致的经济放缓

为了结束这篇文章，让我们更好地理解 GIL 的缺点，也就是我们上面的陈述:它将每个 Python 程序变成了单线程程序。

为了更好地理解这一点，我们修改了上面介绍的计数示例，不再使用全局变量，而是让每个函数自己计数:这模拟了一些 CPU 密集型、可并行化的应用程序——也就是说，我们不需要锁定对象。让我们从 C++开始:

```
#include <chrono>
#include <iostream>
#include <mutex>
#include <thread>
#include <vector>

constexpr int kMaxCount = 50000000;
// Number of threads to employ.
constexpr int kNumThreads = 1;

// Mock some CPU-heavy operation, i.e. simply count up to max_count.
void countTo(int max_count) {
  int counter = 0;
  for (int i = 0; i < max_count; i++) {
    counter++;
  }
}

int main() {
  auto t_start = std::chrono::high_resolution_clock::now();
  // Create kNumThreads, start them and move them to a vector.
  std::vector<std::thread> threads;
  for (int i = 0; i < kNumThreads; i++) {
    std::thread t(countTo, kMaxCount / kNumThreads);
    threads.push_back(std::move(t));
  }
  // Join all threads.
  for (auto& t : threads) {
    t.join();
  }
  auto t_end = std::chrono::high_resolution_clock::now();
  double elapsed_time_ms =
      std::chrono::duration<double, std::milli>(t_end - t_start).count();
  std::cout << "Execution done, time elapased [ms[: " << elapsed_time_ms;
  return 0;
}
```

上面的代码运行需要大约 90 毫秒(在我的旧笔记本电脑上)。但是，当我们将线程数量增加到 8 和 32 时，所需的时间分别减少到 24 和 18 毫秒，即没有线性加速，并且回报递减，但计算机仍然可以有效地并行运行线程，特别是使用更多(所有可用)内核。

让我们将它与 Python 进行比较:

```
import threading
import time

MAX_COUNT = 50000000
NUM_THREADS = 1
MS_PER_S = 1000

# Mock some CPU-heavy operation, i.e. simply count up to max_count.
def count_to(max_count):
    counter = 0
    for _ in range(int(max_count)):
        counter += 1

t_start = time.time()

# Create NUM_THREADS threads, append to list and start them.
threads = []
for _ in range(NUM_THREADS):
    t = threading.Thread(target=count_to, args=(MAX_COUNT / NUM_THREADS,))
    threads.append(t)
    t.start()

# Join all threads.
for t in threads:
    t.join()

print(f"Execution done, time elapsed [ms]: {(time.time() - t_start) * MS_PER_S}")
```

这需要大约 1200 毫秒来运行。将线程数增加到 8 和 32 会产生大约 1500 和大约 1400 毫秒→执行时间不会随着线程数的增加而减少。在这个例子中，GIL 是有效的，它使得线程的数量变得无关紧要！因为所有线程都需要等待 GIL 锁，所以拥有多个内核没有任何优势。

谢谢收听。我希望你喜欢这次短途旅行。请放弃下面的任何问题或评论！

**注意**:在以前的版本中，我错误地声称 Python 中的增量操作符是原子的，因此我们不需要锁定。这是错误的。我为此道歉！