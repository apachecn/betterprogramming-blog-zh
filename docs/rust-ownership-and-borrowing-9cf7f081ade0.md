# 铁锈:所有权和借款

> 原文：<https://betterprogramming.pub/rust-ownership-and-borrowing-9cf7f081ade0>

## 它们是什么，我们如何使用它们？

![](img/2d7f9b492bf9b93ecb9eb0d99d59fc8b.png)

照片由[艾米·赫斯基](https://unsplash.com/@amyhirschi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/own?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我正在学习 [Rust](https://www.rust-lang.org/) 并做了一些关于`Ownership`和`Borrowing`概念的笔记。

请注意，在“与 Rust 借用检查器战斗”的同时，我仍在努力掌握这些概念…

# 生锈的所有权

所有权=值与变量的绑定/关联。规则是:

*   一次只能有一个主人。
*   如果绑定被“释放”，则所有权“消失”，数据被“释放”。

```
let p1 = Person::new(); //1
p2 = p1; //2let p1 = Person::new();
do_something(p1); //out of scope
```

在 2 号线上，`p1`将被释放。它不在范围内，因为它已经将所有权转移给了`p2`。将`p1`传递给另一个函数也有同样的效果。

```
let p1 = Person::new();
p2 = p1;
println!("person {#:?}", p2)
println!("person {#:?}", p1) //compile error
```

这就是 Rust 不允许您再次使用该值的原因。它在编译时使用借位检查器强制实现这一点。

## `**Drop**`

如果需要自定义清理逻辑，可以从`Drop`特征实现`drop`方法。当该值超出范围时，Rust 会自动调用它

一旦一个变量超出了作用域，也就是说，它的所有权被放弃了，Rust 编译器就不允许你再使用它，因为它需要被“释放”。这也叫`Moving`，即`p1`的所有权移到了`p2`。

所有权仅适用于堆数据，*不适用于*堆栈数据，因为它(堆栈数据)是“复制的”，而不是“移动的”。

## `**Copy**`

特性`Copy`允许复制一个值。所有的原始类型(如`i32`、`bool`等)。)隐式实现`Copy`。

自定义类型(如`struct` s)可以实现`Copy`，如果其所有组件也实现`Copy`。要实现，您可以使用`#[derive(Copy)]`或使用显式实现

# 转让所有权的方式

让编译器开心。

*   使用`clone()`方法。
*   从函数返回。
*   传递引用。
*   传递可变引用。

## **使用克隆()**

可以把它想象成一个类似于`deep copy`的操作，它在堆上创建一个单独的数据副本，并把新的所有者指向它，而旧的值保持不变(或不动)。

一些类型默认实现了`Clone`特征(例如`String`，但是您可以使用`#[derive(Clone)]`将您的定制类型标记为可克隆的。

```
let p1 = Person::new();
let p2 = p1.clone();
do_something(p2);
println!("{:?}, p1); //works ok
```

## **从功能**返回

```
let p1 = Person::new();
do_something(p1);
...fn do_something(p: Person) -> Person {
//logic
}...println!("p = {}",p1); //continue using p1 since ownership has been passed back
```

但这太奇怪了。你被迫返回一些东西，只是因为你想保持变量在范围内

## **传递参考**

```
let p1 = Person::new();
do_something(&p1);
println!("p = {}",p1); //continue using p1 since ownership has been was never passed on
...fn do_something(p: &Person) {
//logic
}...
```

`p1`的值没有*不动*——这叫`Borrowing`。`do_something`从未获得所有权，因为我们所做的只是传递一个对`p1`的引用——请注意`&`。

## **可变引用**

如果您想让`do_something`逻辑更新`Person`引用的名称，您需要传递一个可变引用。

```
let mut p1 = Person::new();
do_something(&mut p1);
println!("mutated info {}",p2)
...fn do_something(p: &mut Person, new_name: String) {
   p.name = String::from(new_name);
}
...
```

变化:

*   变量`p1`被声明为`mut`(可变的)。
*   将`&mut`传递给`do_something`而不仅仅是`&`——表示可变引用。
*   `do_something`签名更新为`&mut` —表示可变引用。

# 结论

为了更深入，你可以阅读《Rust 编程语言》 一书中的以下章节。

*   [第四章](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html) —所有权和借款
*   [第十章](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html) —生命周期
*   [第十五章](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html) —智能指针