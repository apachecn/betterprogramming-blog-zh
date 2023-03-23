# 面向开发人员的 5 个高级 C 编程概念

> 原文：<https://betterprogramming.pub/5-advanced-c-programming-concepts-for-developers-22a9dd35e361>

## 随着 C 语言的复兴，让我们来看看一些重要的概念

![](img/e2c517a91e4906668e66536e1e683806.png)

图片来源:作者

由于采用 C 语言作为自动驾驶汽车开发的首选语言，C 语言的人气和需求正在复苏。许多业内资深人士通过 C 语言学习编程，但已经很多年没用过了。

今天，我们将通过对开发人员来说最重要的五个高级 C 语言概念来帮助您更新 C 语言知识或使您的学习更上一层楼。

**下面是我们今天要讲的内容:**

*   1.动态存储分配
*   2.用 gdb 调试
*   3.函数指针
*   4.C 中的递归
*   5.C #中的类型转换和 typedef
*   接下来学什么

# 1.动态存储分配

## 定义

在 C #中，内存分配有两种类型:静态和动态。静态分配是两者中最基本的，在执行时分配给堆栈。

一旦分配，静态分配有一个固定的大小。静态分配用于任何全局变量、文件范围变量和带有`static`关键字的变量。

**动态内存分配**是两者中更高级的，在分配后可以改变大小。这个内存存储在堆中。与堆栈不同，堆内存没有大小限制。

动态分配会根据需要分配更多的内存，这意味着分配的内存永远不会超过您的需要。

动态内存分配也会降低性能。缓存的堆数据不像缓存的堆栈数据那样相邻。因此，检索堆数据的效率通常不如检索堆栈数据。

动态内存分配也需要很大的开销。缓存到堆中的变量必须有一个大小相等的关联指针，以便以后定位该变量。

例如，一个 4 字节的变量也有一个关联的 4 字节指针。这意味着堆数据的资源成本大约是栈数据的两倍。

虽然动态内存可以扩展以容纳更多数据，但当数据量减少时，它不会自动释放内存。相反，您必须使用`free()`函数手动释放内存。

如果您忘记释放内存，您将很快发现自己遇到了内存泄漏错误，这可能会降低您的程序速度，甚至导致程序崩溃！

## 使用

动态内存的开销成本最好用于选择变量，如缩放数据结构。最好仍然对大多数变量使用静态分配，以避免降低程序速度。

在以下情况下，您应该使用动态内存:

*   你事先不知道一个结构需要多少内存
*   您想要一个可以无限扩展以匹配输入的数据结构
*   您希望确保永远不会提前过度分配内存(自动向上扩展)
*   您正在使用一个链表或结构对象

## 履行

```
#include <stdio.h>
#include <stdlib.h> 
int main( )
{
    // Declare pointer variables
    int  *i ;
    float  *a ; 
    /* Allocate memory using malloc and store the starting 
    address of allocated memory in pointer variable*/
    i = (int*) malloc (sizeof(int));
    a = (float*) malloc (sizeof(float));
    // Declare employee structure
    struct emp
    {
        char name [20];
        int age;
        float sal;
    };
    // Declare structure pointer
    struct emp *e;
    e = (struct emp *) malloc (sizeof (struct emp));
}
```

在**中，第 6、7 和 20 行**、`i`、`a`和`e`被创建在堆栈上。三个都是指针。它们所指向的内存块是在堆上创建的。

在**第 10–11 行**中，我们使用`malloc( )`库函数来实现动态内存分配。在动态内存分配中，两个决定——分配多少内存和分配到哪里——都是在执行过程中做出的。

应该分配的字节数作为参数传递给`malloc( )`。`malloc( )`分配那么多内存，并返回作为空指针分配的内存块的基址。

使用(目标类型)语法，将由`malloc( )`返回的空指针适当地类型转换(转换)成整数指针、浮点指针或结构 emp 指针。

# 2.用 Gdb 调试

## 定义

Linux 是 C 编程最常用的操作系统。Linux 有一个名为 **gdb** 的调试命令行工具，可以帮助你调试你的程序。一旦安装完毕，你可以使用 gdb 运行你的整个程序，它会指出逻辑和语法错误。

这允许您:

*   执行程序，一次一条语句
*   在程序的任何给定行/函数处停止执行
*   打印中间值
*   了解执行的详细流程

## 使用

gdb 中最重要的命令是断点、`step`、`next`和变量值打印。

**断点**在指定的时间暂停程序的执行，允许你评估程序状态和变量值。该工具用于将较大的代码段分解成较小的代码块。然后，您可以运行这些块来确定 bug 的大致位置。

**步骤**让你一次运行一行代码。该命令执行下一行代码，然后暂停以等待进一步的指令。一旦您将 bug 缩小到一个较小的代码块，而不是一个特定的行，您就可以使用它。当您遍历这个程序块时，您最终会发现是哪一行导致了这个错误。

**下一个**与`step`相似，但是执行下一个功能而不是下一行。这最好与断点一起使用，以调试功能繁重的程序或分支程序。您可以运行每个模块化函数，并确保每个函数都按预期执行。

**变量值打印**用于在断点、`step`或`next`命令后打印变量的当前值。如果没有 gdb，每次程序与变量交互后，您都必须手动将打印命令写入程序。相反，gdb 允许你的程序摆脱杂乱的打印命令，同时仍然允许你通过程序段跟踪变量值。

## 履行

**安装:**

```
apt-get -y install gdb
```

**使用 gdb 编译器编译程序:**

```
gcc main.c -g
```

**进入 gdb 模式:**

```
gdb a.out
```

**参见源代码:**

```
list
```

**创建断点:**

```
gdb) break 6
gdb) run
```

**一步一行:**

```
gdb) step
```

**第一步功能:**

```
gdb) next
```

**检查变量的当前值:**

```
print {variable name}
```

> *按下* `*enter*` *同时在广发租房上一个命令。使用它可以快速遍历代码块，而无需每次都重新键入* `*step*` *或* `*next*` *命令。*

# 3.函数指针

## 定义

函数指针是调用已创建函数的另一种方式。标准的函数调用带有函数名和括号`function1()`。函数指针允许你用函数的内存位置`(*f)()`调用函数。

为此，您必须首先将所需函数的位置存储在一个指针变量中。一旦你这样做了，你就可以在任何需要使用标准函数调用的地方使用函数指针。

## 使用

函数指针允许你**将函数作为参数**传递给其他函数或数据结构。因此，您可以避免使用临时变量，而是简单地将函数的结果直接传递给下一个函数。

函数指针还可以防止不同数据类型的重复代码。例如，通常要为不同的数据类型创建一个快速排序程序，你需要编写特定类型的排序函数，比如`quicksort_integer`或`quicksort_char`。

使用函数指针，您可以创建一个函数，它将指针作为参数，并根据您有比较器的任何字段进行排序。

最后，函数指针允许你创建用函数而不是变量填充的结构。例如，您可以通过将每个函数指针存储为一个元素来创建一个函数数组。

当您想要将一个函数插入到另一个函数中时，或者当您想要在使用数据结构时减轻类型限制时，请使用函数指针。

## 履行

```
# include <stdio.h>
void dbl ( int * ) ;
void tple ( int * ) ;
void qdpl ( int * ) ;
int main( )
{
    int  num = 2, i ;
    void ( *p[ ] )( int * ) = { dbl, tple, qdpl } ;
    for ( i = 0 ; i < 3 ; i++ )
    {
        p[ i ]( &num ) ;
        printf ( "%d\n", num ) ;
    }
    return 0 ;
}void dbl ( int *n )  
{
    *n = *n * *n ;
}void tple ( int *n )  
{
    *n = *n * *n * *n ;
}void qdpl ( int *n )  
{
    *n = *n * *n * *n * *n ;
}
```

在这个程序中，我们定义了三个函数。`dble`、`tple`和`qdpl`函数接收一个 int 的地址，分别将 int 值加倍/三倍/四倍，并且不返回任何值。

在 main 函数中，我们将这些函数的地址存储在一个数组中，然后通过遍历一个数组来逐个调用它们。

在**第 8 行**中，我们创建了一个函数指针数组，并将`dble`、`tple`和`qdpl`的地址存储在一个数组中。

在**第 11 行**中，我们通过遍历一个数组，根据地址调用`dbl`、`tple`和`qdpl`函数。

# 4.C 中的递归

## 定义

递归是指函数包含对自身的调用。递归程序通常包含在每次递归迭代中重复的递归调用之上的命令和操作。

在 C 中，可以递归调用用户函数和`main()`。在许多情况下，递归可以取代传统的循环。像循环一样，如果没有退出条件，递归程序可以无限循环。

典型的递归调用要么来自`if`段，要么来自`else`段。

对于`if`类型的递归程序，递归调用保存在`if`段。程序将继续迭代，直到`if`变为`false`。然后，程序将前进到包含返回语句的`else`段。

例如:

```
int example()
{
  if (n != 0){ 
    // do something
    // something more
    example(..); //recursive call
  }
  else 
  {
    return 1;
  }
}
```

在`else`类型程序中，递归调用与影响程序条件的操作一起保存在`else`段中。任何`else`类型的程序都会迭代，直到`if`语句变成`true`。

这些程序类型之间的区别在于，我们是从起始状态*远离*还是从*向*目标状态前进。

例如:

```
int example ()
{
  if (n == 0){
      return 1; 
  }
  else 
  {
    // do something
    // something more
    example(..); //recursive call
  }
}
```

## 使用

递归用于重新定位问题，以关注期望的条件，而不是实现它的迭代次数。

对于循环，您必须对程序将要进行的迭代次数进行编程。这意味着你必须知道一个给定的输入需要多少次迭代，或者创建一个子组件来决定迭代的次数。

然而，您最终关心的是实现某个目标，迭代是您实现目标的工具，而不是相反。

递归允许你设计基于目标思维的程序。递归程序重复多少次来实现它们的目标，无论是偏离一个起始状态还是到达一个目标状态。

你不应该对所有事情都使用递归，因为它比迭代循环解决方案使用更多的资源。一般来说，递归最适合用于以下解决方案:

*   可以用许多更小的子问题的答案来解决
*   需要多次重复相同的步骤
*   追求某些程序状态。

## 履行

这个 C 程序寻找一个整数参数的阶乘。这是一个`else`类型的递归程序。

```
#include <stdio.h>
int refact ( int ) ;int main( )
{
    int  num, fact ;
    num = 4; fact = refact ( num ) ;
    printf ( "Factorial value = %d\n", fact ) ; return 0 ;
}int  refact ( int  n )
{
    int  p ; if ( n == 0 )   
        return ( 1 ) ; 
    else
        p = n * refact ( n - 1 ) ; //recursive call return ( p ) ;
}
```

# 5.C #中的类型转换和 Typedef

## 定义

类型转换是 C #中一种特殊的操作，它将一种数据类型转换成另一种数据类型。类型转换可以隐式或显式完成。

隐式类型转换是编译器自动转换数据的地方。隐式类型转换更容易实现，但在转换过程中经常会丢失信息。

```
// implicit typecasting
# include <stdio.h>
int main( )
{
    // Initialize variables of type int
    int  a = 5, b = 2, c ;
    // Declare variable of type int
    float  d ;
    // Assign value to c
    c = a / b ;
    printf ( "%d\n", c ) ;
    // Store result of integer division in variable of float type
    d = a / b ;
    printf ( "%f\n", d ) ;
}
```

这里，`d`应该有`2.500000`的值，但实际上是`2.000000`。十进制值在自动转换过程中被截断，因为`a`和`b`都是整数，因此从它们的除法运算中返回一个整数值。

**显式类型转换**解决了这个问题。使用这种类型转换形式，您可以手动转换数据，因此可以选择更早的转换点。

```
// explicit typecasting
# include <stdio.h>
int main( )
{
    // Initialize variables of type int
    int  a = 5, b = 2, c ;
    // Declare variable of type int
    float  d ;
    // Assign value to c
    c = a / b ;
    printf ( "%d\n", c ) ;
    // Store result of float division in variable of float type
    d = ( float ) a / b ;
    printf ( "%f\n", d ) ;
}
```

在**第 12 行**、`d = ( float ) a / b`上，在执行除法运算之前，a 被强制定型为浮点型*。因此，操作会保留操作的所有十进制值。如您所见，`d`的值就是显式转换时的`2.500000`。*

> *只从不太具体的数据类型转换到更具体的数据类型，以避免数据丢失。例如，如果您从* `*int*` *→* `*double*` *转换，您将保留所有信息，但是如果您从* `*double*` *→* `*int*` *转换，您将丢失信息。*

## 使用

您可以使用类型转换来创建**可重用代码**。如果将较小的数据转换为较大的数据，则可以将相同的值用作多个函数的参数，即使它们调用不同的数据类型。

您还可以从第一个函数序列中获取输出，并将其转换为新的类型，以满足它遇到的下一个函数的需求。在 c #中使用严格数据类型时，类型转换允许您有更大的自由度。

您还可以使用`typedef`关键字进行类型转换，为现有的数据类型或用户定义的结构赋予新的名称。这并没有取消原始名称的赋值，只是增加了另一种引用相同类型的方式。

例如:

```
#include<stdio.h>int main() {
    // Declare variables
    unsigned long int i, j;
    // Give new name to unsigned long int
    typedef unsigned long int ULI;
    // Declare variables of type ULI
    ULI k, l;
}
```

这里我们将名称`ULI`指定给`unsigned long int`类型。然后，我们可以声明类型为`ULI`或`unsigned long int`的变量，并获得相同的效果。

这有助于提高可读性，因为您可以为数据类型指定名称，以缩短声明或更准确地表示它们将保存什么值(`string` → `color`)。

## 履行

**隐式类型转换:**

```
// implicit typecasting
# include <stdio.h>
int main( )
{
    // Initialize variables of type int
    int  a = 5, b = 2, c ;
    // Declare variable of type int
    float  d ;
    // Assign value to c
    c = a / b ;
    printf ( "%d\n", c ) ;
    // Store result of integer division in variable of float type
    d = a / b ;
    printf ( "%f\n", d ) ;
}
```

**显式类型转换:**

```
# include <stdio.h>
int main( )
{
    // Initialize variables of type int
    int  a = 5, b = 2, c ;
    // Declare variable of type int
    float  d ;
    // Assign value to c
    c = a / b ;
    printf ( "%d\n", c ) ;
    // Store result of float division in variable of float type
    d = ( float ) a / b ;
    printf ( "%f\n", d ) ;
}
```

**用户结构的 Typedef:**

```
#include<stdio.h>int main() {
    // Declare structure
    struct a
    {
        char *p;
    };
    // Give new name to struct a
    typedef struct a FILE;
    FILE *fs , *ft;
}
```

# 接下来学什么

祝贺您完成了高级 C 编程的第一步！我们讨论了提高 C 编程技能的五个最重要的概念。

然而，这些仅仅是这种长期发展的语言所能提供的表面。一些值得研究的下一个主题是:

*   创建库
*   使用 Linux/Unix 的 c 语言
*   位和按位运算符
*   可变参数列表
*   结构

快乐学习！