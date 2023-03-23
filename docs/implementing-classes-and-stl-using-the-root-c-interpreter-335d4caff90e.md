# 使用根 C++解释器实现类和 STL

> 原文：<https://betterprogramming.pub/implementing-classes-and-stl-using-the-root-c-interpreter-335d4caff90e>

## 利用根 C++解释器的力量

![](img/4dedb0c07c5adca8bc602ce78a2fed51.png)

由[拉格斯技术人员](https://unsplash.com/@heylagostechie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将演示如何使用 ROOT C++解释器定义和实现类，并演示如何使用标准模板库(STL)中可用的一些数据结构和算法。但是首先，我需要演示如何使用 ROOT 编写独立的程序。

# 根宏

正如我将在后面演示的，在根 shell 中编写类定义是不可行的。但是，您可以使用 shell 编写宏并执行它们。下面是定义根宏的方法。

首先，宏存储在主根目录下的 macros 子目录中。宏以. C 扩展名保存。您可以在宏中编写任何可执行的 C++代码，并且不必担心包含任何库，因为 shell 中包含的库也包含在您的宏中。宏可以从 shell 或命令行执行。

为了演示，下面是一个简单的宏，它显示“Hello，world！”对着屏幕:

```
void hello() {
  std::cout << "Hello, world!" << endl;
}
```

下面是从 shell 中执行宏的样子:

```
root [9] .x hello.C
Hello, world!
root [10]
```

下面是从命令行执行宏的样子:

```
C:\root_v6.24.06\bin>root hello.c------------------------------------------------------------------| Welcome to ROOT 6.24/06                        https://root.cern || (c) 1995-2021, The ROOT Team; conception: R. Brun, F. Rademakers || Built for win32 on Sep 02 2021, 14:20:23                         || From tags/v6-24-06@v6-24-06                                      || With MSVC 19.23.28107.0                                          || Try '.help', '.demo', '.license', '.credits', '.quit'/'.q'       |------------------------------------------------------------------root [0]
Processing hello.c...
Hello, world!
root [1]
```

注意，我必须导航到 bin 目录来执行宏。

第三种选择是加载一个函数，例如，用宏编写的函数，然后在 shell 中执行它。在下面的例子中，我写了一个摄氏到华氏的转换函数，并让它将一组摄氏温度转换成华氏温度。我在一个名为 ctof.c 的宏文件中定义了该函数。下面是该函数的定义以及从宏中加载该函数并在程序中使用它的脚本:

```
double ctof(double cel) {
  return (9.0 / 5.0) * cel + 32.0;
}root [0] .L ctof.c
root [1] for (int c = 0; c <= 100; c+=5) {
root (cont'ed, cancel with .@) [2]std::cout << ctof(c) << endl;
root (cont'ed, cancel with .@) [3]}
32
41
50
59
68
77
86
95
104
...
194
203
212
root [4]
```

现在您已经知道了如何执行宏，让我们利用这些知识来帮助我们更好地使用 ROOT 构建和执行面向对象的程序。

# 在宏中创建类定义

你可以在一个宏中创建一个类定义，然后像我们刚才处理函数一样加载它。下面是一个简化的堆栈类定义(我没有把声明和定义部分分开),它是在一个宏文件标题堆栈中定义的。

```
template <typename T>
class Stack {
private:
  std::vector<T> data;
public:
  Stack() { }
  void push(T element) {
    data.push_back(element);
  }
  void pop() {
    data.pop_back();
  }
  T top() {
    return data.back();
  }
  void clear() {
    data.clear();
  }
  bool isEmpty() {
    if (data.size() == 0) {
      return true;     
    }
    return false;
  }
};
```

让我们将这个定义加载到解释器中并使用它:

```
root [0] .L stack.c
root [1] Stack<string> names;
root [2] names.push("Cynthia");
root [3] names.push("Jonathan");
root [4] std::cout << names.top() << endl;
Jonathan
root [5] names.pop();
root [6] std::cout << names.top() << endl;
Cynthia
root [7] names.push("Danny");
root [8] if (!names.isEmpty()) {
root (cont'ed, cancel with .@) [9]std::cout << names.top() << endl;
root (cont'ed, cancel with .@) [10]}
Danny
root [11] names.clear();
root [12]
```

# 使用标准模板库

ROOT C++解释器允许你使用标准模板库(STL)中的所有数据结构和算法。让我们看看如何使用 STL。

我将从使用随机数生成创建一个简单的整数向量开始，然后使用`sort`函数将向量按升序排列:

```
root [0] std::default_random_engine dre(std::time(0));
root [1] std::uniform_int_distribution<int> ints(1,100);
root [2] std::vector<int> numbers;
root [3] for (int i = 1; i <= 10; i++) {
root (cont'ed, cancel with .@) [4]numbers.push_back(ints(dre));
root (cont'ed, cancel with .@) [5]}
root [6] for (int n : numbers) {
root (cont'ed, cancel with .@) [7]std::cout << n << endl;
root (cont'ed, cancel with .@) [8]}
13
65
75
21
62
80
95
75
26
71
root [9] std::sort(numbers.begin(), numbers.end());
root [10] for (int n : numbers) {
root (cont'ed, cancel with .@) [11]std:: cout << n << endl;
root (cont'ed, cancel with .@) [12]}
13
21
26
62
65
71
75
75
80
95
root [13]
```

让我们试试另一种算法。与其显示整个向量，不如使用`for_each`函数和一个用户定义的函数来显示大于 50 的数字，以确定向量元素是否大于 50:

```
root [17] void print_greater_than_50(int val) {
root (cont'ed, cancel with .@) [18]if (val > 50) { std::cout << val << " "; }
root (cont'ed, cancel with .@) [19]}
root [20] for_each(numbers.begin(), numbers.end(), print_greater_than_50);
62 65 71 75 75 80 95
```

我可以用 lambda 函数来编写上面的代码:

```
root [21] for_each(numbers.begin(), numbers.end(),
[] (int val)   { if (val > 50) std::cout << val << " "; });
62 65 71 75 75 80 95
```

顺序数据很好，但有时您需要本质上更具关联性的数据。`map`数据结构允许您以键/值对的形式存储数据，创建类似电话簿和字典的结构。

下面的例子创建了一个存储一组名字和数字的`map`。在创建了几组数据之后，该示例演示了如何使用 name 键提取一个电话号码。代码如下:

```
root [0] std::map<string, int> contacts = {{"Cynthia", 2112}, {"Jonathan", 2213},
{"Danny", 2001}, {"Mayo", 2313}};
root [1] std::cout << contacts["Cynthia"] << endl;
2112
root [2] std::cout << contacts["Danny"] << endl;
2001
```

下一个片段演示了如何向地图中插入新数据:

```
root [4] contacts.insert({"Raymond", 2412});
root [5] contacts.insert({"April", 2534});
```

现在，让我们遍历列出存储的每个姓名和号码的完整地图:

```
root [17] for (const auto &contact : contacts) {
root (cont'ed, cancel with .@) [18]std::cout << contact.first << ": " << contact.second << endl;
root (cont'ed, cancel with .@) [19]}
April: 2534
Cynthia: 2112
Danny: 2001
Jonathan: 2213
Mayo: 2313
Raymond: 2412
```

如果您不熟悉地图，地图元素存储为一个 pair 结构，它有两个字段，`first`和`second`。`firs` t 字段存储联系人地图中的姓名，而`second`字段存储号码。

最后一个例子，让我们看看如何从列表中删除一个联系人。这涉及到调用`find`函数，该函数将一个键作为其参数，返回一个指向带有该键的 map 元素的迭代器。然后用迭代器作为参数调用`erase`函数。下面是一个例子:

```
root [20] auto removed = contacts.find("Raymond");
root [21] contacts.erase(removed);
root [22] for (const auto &contact : contacts) {
root (cont'ed, cancel with .@) [23]std::cout << contact.first << ": " << contact.second << endl;
root (cont'ed, cancel with .@) [24]}
April: 2534
Cynthia: 2112
Danny: 2001
Jonathan: 2213
Mayo: 2313
```

# 最后的想法

在本文中，我通过演示如何在解释器中创建和使用类，以及如何使用标准模板库的数据结构和算法来执行相当复杂的计算，进一步展示了 ROOT C++解释器的强大功能。

在我的下一篇文章中，我将开始演示如何使用 ROOT 解释器来执行数据分析，这当然是 ROOT 的全部目的。