# 通过 R 学习面向对象编程

> 原文：<https://betterprogramming.pub/learn-object-oriented-programming-through-r-d35a877c22a4>

## 面向对象系统简介

![](img/5a5501b13a2361ccd27c9bb65b23f8e9.png)

卢克·埃利斯-克雷文在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

出于许多原因，r 是一种很棒的编程语言。但是绝大多数 R 用户并不知道 R 给他们带来的所有好处。

面向对象编程是 R 用户经常忽略的好处中的第一个。部分原因是 R 生态系统非常适合快速进入编程，这种适合性导致 R 社区低估了面向对象软件设计。

面向对象编程和过程编程是软件世界中两种广泛使用的编程范例之一。我们不会试图从零开始教授面向对象编程，我们将假设读者对面向对象编程的基本概念有一些熟悉，如类、封装、继承和多态。

与 Python、Java 和 C++等其他面向对象的语言相比，R 有着截然不同的语法，但基本概念保持不变。在 R 中应用面向对象有两个主要的类系统，即 S3 和 S4。我们将详细研究它们。我们开始吧！

# S3 班级

这里我们创建一个简单的线性模型，并根据下面的例子讨论 S3 类的结构:

```
v1 <- rnorm(n = 5 , mean = 100 , sd = 10)
v2 <- v1 + rnorm(n = 5 , mean = 0 , sd = 10)
reg <- lm(v2 ~ v1)
class(reg)## [1] "lm"
```

正如你所看到的，当我们取消一个对象的分类时，它本质上是一个列表。

为了了解为什么`print()`函数在分类和未分类的线性模型上工作不同，我们稍微深入地检查一下`print()`函数。

```
print## function (x, ...) 
## UseMethod("print")
## <bytecode: 0x0000000013082240>
## <environment: namespace:base>
```

为了查看所有与`print()`函数相关的类方法，我们运行下面的代码:

```
methods(print)##   [1] print.acf*                                          
##   [2] print.AES*                                          
##   [3] print.anova*                                        
...                                  
## [133] print.lm*                                           
...                             
## [228] print.xgettext*                                     
## [229] print.xngettext*                                    
## [230] print.xtabs*                                        
## see '?methods' for accessing help and source code
```

截至我现在使用的 R 4.0.2 版本，有 230 个类方法带有`print()`函数。第 133 名是`print.lm*`。但是星号( `\*`)符号意味着`print.lm()`的内容并不能像我们上面做的那样，只需输入不带括号的内容就能直接看到。为此我们使用了`getAnywhere()`。

要查看所有通用函数，只需执行以下操作:

```
methods(  class = 'default')##   [1] add1            aggregate       AIC             all.equal      
....        
## [157] wilcox.test     window          with            xtfrm          
## see '?methods' for accessing help and source code
```

我们已经看到了 S3 类如何使用方法和属性。现在是时候构建我们自己的 S3 类了。第一步很简单，创建一个包含属性元素的列表。然后，我们运行`class()`函数，其中包含一个对象列表，并手动命名该类。到那时，我们将完成整个过程。这可能看起来很奇怪，因为我们首先创建对象，然后创建类，因为在其他著名的面向对象语言中，情况正好相反。

```
student1 <- list(name="Ugurcan", semester = 3 , statStudent = T)
student2 <- list(name="Mark", semester = 7 , statStudent = F)class(student1) <- 'student'
class(student2) <- 'student'
```

这里，我们首先以列表的形式创建了两个实例，然后通过直接命名来创建它们的类:

当我们打印它们时会发生什么？

正如我们提到的，print 是一个通用函数，没有对应的类方法。这就是为什么当我们在这两个实例上调用 print 时，它们会像列表一样被打印出来。让我们为通用函数`print()`创建我们的类方法。

```
print.student <- function(student) {
  cat(student$name , "\n")
  cat("semester" , student$semester , "\n")
  cat("Is he\\she a Statistics student ?" , student$statStudent , "\n")
}
```

下面让我们来看看它是否有效:

太棒了。我们有两个具有适当属性的实例，一个类和一个类方法。

继承是一个重要的话题，它的实现与其他语言有很大的不同。为了创建一个继承类，我们首先再次创建实例，但是不是传递一个字符串作为类名，而是传递一个字符串向量，其中向量的每个元素都分层给出类名。

```
student3 <- list(name="Lauren", semester = 6 , statStudent = F , dorm = T)
class(student3) <- c("dormStudents" , "student")
print(student3)## Lauren 
## semester 6 
## Is he\she a Statistics student ? FALSE
```

我们已经为已经存在的通用函数创建了类方法。当您处理大型项目时，您可能还需要创建通用函数。这是我们 S3 的方式。

通过`UseMethod()`创建调用自身的函数。

```
advance <- function(x , ...) {
  UseMethod(generic = "advance" )
}
```

就是这样。我们有一个通用函数。现在我们为这个通用函数编写一个类方法。

```
advance.student <- function(x) {
  x$semester = (x$semester +1)
  return(x)
}
```

我们把`student1`提前了一个学期。

```
student1 <- advance(student1)
student1## Ugurcan 
## semester 4 
## Is he\she a Statistics student ? TRUE
```

# S4 班级

S4 类是 r 中另一种类型的面向对象系统。S3 和 S4 之间的差异应该从语言的历史发展角度来解释。

r 受到另一种语言 S 的启发，后者是在 20 世纪 70 年代由贝尔实验室开发的。S3 的类系统来自于 S 语言。但随着时间的推移，随着新的面向对象语言的崛起，S3 类开始显得过时，因为它们缺乏一定程度的健壮性和安全性，而其他语言在当时也是如此。

作为对此的回应，S4 班级诞生于防止拼写错误和误认的需要，而 S3 班级很容易出现这种情况。

我们用函数`setClass()`定义了一个 S4 类。

```
setClass(Class = "student" , representation = list(
                                              name = "character",
                                              semester = "numeric" ,
                                              statStudent = "logical"
)
         )
```

与 S3 不同，这里我们首先创建了类，然后创建了实例。每个实例都是用`new()`函数创建的。

请注意，S4 类的属性称为插槽。访问它们的一种方法是使用`@`符号，而不是像我们在 S3 课上做的那样使用`$`。访问 S4 属性的第二种方法是使用`slot()`函数。在下面的示例中，您可以看到这两种方式:

```
student1@semester## [1] 3slot(object = student1 , name = "name")## [1] "Uğurcan"
```

如果我们犯了任何拼写错误或其他编程错误，S4 类会阻止我们这样做。另一方面，S3 类只是美化了的列表。

为了在 S4 类上实现一个类方法，我们使用了`setMethod()`函数。我们将为通用函数`show()`创建一个类方法，它是 S3 的`print()`的 S4 等价物。正如我们可能猜测的那样，当我们在控制台上键入一个对象的名称时`show()`就会起作用。事实上，我们只要使用`show()`就会得到相同的输出。

这就是我们如何使用`setmethod()`来创建 S4 类方法:

```
setMethod("show" , "student" , function(object) {
  cat(object@name , "\n")
  cat("semester" , object@semester , "\n")
  cat("Is he\\she a Statistics student ?" , object@statStudent , "\n")
})
```

让我们看看它是否有效。

```
show(student1)## Uğurcan 
## semester 3 
## Is he\she a Statistics student ? TRUE
```

为了继承 S4 中的另一个类，我们将超类的名称传递给`setClass()`函数中的`contains`参数。

```
setClass(Class = "dormStudents"  ,
         representation = list(dorm = "logical" ) ,
         contains = "student" )
```

让我们为子类创建一个新实例:

在 S4 系统中创建通用函数类似于 S3。我们创建一个通过`standardGeneric()`调用自身的函数。

```
advance <- function(object , ...){
  standardGeneric("advance")
}
```

现在，我们为这个通用函数创建一个类方法。

```
advance.student <- setMethod(f = "advance" ,
                             signature = "student" ,
                             definition = function(object) {
                               object@semester <- (object@semester + 1)
                               return(object)
                             })
```

这很有效。

```
student1 <- advance(student1)
student1## Uğurcan 
## semester 4 
## Is he\she a Statistics student ? TRUE
```

我们可以使用 S3 语法创建一个 S4 通用函数。注意我们只把`$`改成了`@`。

r 是会上瘾的。如果您只是通过编写简单的脚本开始 R 编程，这并不奇怪。随着项目的增长，您将需要面向对象设计的武器来对抗混乱和非结构化的代码。或者，谁知道呢，也许你会开始你自己的 R 库。到那时，我希望这篇文章能对你有所帮助。