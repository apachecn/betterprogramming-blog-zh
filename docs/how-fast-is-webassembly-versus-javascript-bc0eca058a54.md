# WebAssembly 相对于 JavaScript 有多快？

> 原文：<https://betterprogramming.pub/how-fast-is-webassembly-versus-javascript-bc0eca058a54>

## 进行一些简单的基准测试产生了一些令人惊讶的结果

![](img/b105467578f03816ce582fa254d1de19.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Veri Ivanova](https://unsplash.com/@veri_ivanova?utm_source=medium&utm_medium=referral) 拍摄的照片

几天前，我想尝试建立一个完全由 WebAssembly 驱动的网站。在我看来，今天最简单的方法是使用[微软的 Blazor](https://dotnet.microsoft.com/en-us/apps/aspnet/web-apps/blazor) 。本质上，您可以在以前使用 JavaScript 的地方编写 C#代码，Blazor 会将 C#编译成 WebAssembly，以允许它在浏览器中运行。

然而，我有点惊讶地发现，当我测试它与用 JavaScript 编写的应用程序的速度时，它明显慢了。因此，我决定对 WebAssembly 进行基准测试，看看它与 JavaScript 在现实场景中的表现如何，比如从 JavaScript 调用 WebAssembly 模块。

# 什么是 WebAssembly？

在我们深入基准测试之前，你们中的一些人可能不熟悉 WebAssembly，所以我将快速介绍一下它是什么。如果您已经熟悉，请随意跳到下一部分。

WebAssembly 是一种低级汇编语言，可以在任何主流浏览器中运行。它被设计得很快，比 JavaScript 快得多，以便处理要求更高性能的现代 web 用例。与在运行时使用实时编译器的 JavaScript 不同，WebAssembly 在构建时编译，并由浏览器直接执行，与 Java 在构建时编译的方式相同。

工程师通常不会编写 WebAssembly，相反，你可以像今天一样用你喜欢的编程语言编写代码，然后编译成 WebAssembly。

几个主要的语言已经得到支持，包括 C#、C/C++、Rust 和 TypeScript——许多其他语言[目前正在为生产做准备。](https://github.com/appcypher/awesome-wasm-langs)

WebAssembly 当前不能访问 DOM，因此它必须依赖于调用 JavaScript 来呈现 WebAssembly 模块的任何输出。

# WebAssembly 比 JavaScript 快吗？

将 C#编译成 WebAssembly 实际上只有 Blazor 才有可能，所以对于基准测试来说不是很有用。相反，我将使用 TypeScript(技术上来说， [AssemblyScript](https://www.assemblyscript.org/) )，它可以很容易地编译成 WebAssembly 模块。

## 基准 1:立方数

我尝试的三个基准中的第一个是对数字进行立方运算。这些功能本身非常简单:

```
// AssemblyScript
export function cube(n: i32): i32 {
  return n * n * n;
}// JavaScript
function cube(n) {
    return n * n * n
}
```

然后我使用 [BenchmarkJS](https://benchmarkjs.com/) 建立了一个简单的基准:

```
suite.add('JavaScript', function() {
    cube(1000)
})
.add('WebAssembly', function() {
    wasm_functions.cube(1000)
})
.on('cycle', function(event) {
    ***console***.log(***String***(event.target));
})
.on('complete', function() {
    ***console***.log('Fastest is ' + this.filter('fastest').map('name'));
})
.run();
```

最后，运行基准测试:

```
Running tests for cube
JavaScript x 886,730,026 ops/sec ±1.41% (86 runs sampled)
WebAssembly x 168,566,364 ops/sec ±0.84% (95 runs sampled)Fastest is JavaScript
```

正如你所看到的，它一点也不接近——**JavaScript 快了五倍多。**

## 基准 2:排序数组

在现代计算中，给一个数字立方形非常容易，即使对于 JavaScript 也是如此。因此，我决定尝试一些稍微复杂一点的东西:对数组进行排序。同样，方法很简单:

```
// AssemblyScript
export function sort_array(arr: Array<number>): void
{
  arr.sort()
}// JavaScript
function sort_array(arr)
{
    arr.sort()
}
```

本质上，两者都将使用内置数组排序，结果如下:

```
Running tests for sorting an array
JavaScript x 1,186 ops/sec ±0.57% (94 runs sampled)
WebAssembly x 402 ops/sec ±0.91% (85 runs sampled)Fastest is JavaScript
```

这一次更接近了，但是 JavaScript 仍然比 T4 快了三倍。

## 基准 3:阶乘

对于最后一个基准测试，我决定尝试稍微复杂一点的东西——计算给定数字的阶乘。阶乘是将一个给定的数和它之前的所有正数相乘得到的数。例如，4 的阶乘是 24，因为你乘以 1x2x3x4。

下面是每个实现的代码:

```
// AssemblyScript
export function factorial(num: i32): i32 {

  if (num === 0) return 1
  if (num === 1) return 1;

  let fact = num;

  fact = fact * factorial(num-1);
  return fact;
}// JavaScript
function factorial(num){

    if (num === 0) return 1
    if (num === 1) return 1;

    let fact = num;

    fact = fact * factorial(num-1);
    return fact;
}
```

两者都利用了递归——有一种更有效的方法，但是我想通过使用递归来使它更费力一些。结果如下:

```
Running tests for factorials
JavaScript x 118,328 ops/sec ±1.23% (93 runs sampled)
WebAssembly x 218,274 ops/sec ±1.07% (88 runs sampled)Fastest is WebAssembly
```

最后，WebAssembly 速度更快— **几乎是 JavaScript 的两倍，事实上是**。一个有趣的观察是，WebAssembly 只会随着阶乘的增加而变得更快。

上面的基准是阶乘 6，但是如果我使用阶乘 3 重新运行基准，JavaScript 轻松获胜。

如果你对我如何运行基准测试的代码感兴趣，你可以在这里查看 repo——它非常粗糙，但确实有效。

# 为什么 WebAssembly 在某些情况下会比较慢？

基准测试之后，我很好奇为什么 WebAssembly 在很多操作上比 JavaScript 慢那么多。在谷歌上搜索了一番后，我看到了一篇有趣的文章，给出了一些很好的见解:

> 根据 WebAssembly 模块的大小，创建它的实例的过程通常需要几秒钟，通常是几十秒钟。
> - PSPDFKIT

这里说的是 C++代码，但同样适用于我们的基准测试。同样，我们每次调用函数，都要在 WebAssembly 实例中加载到内存中(类似于 JavaScript，有一个 WebAssembly VM 运行在执行 WebAssembly 模块的浏览器中)，这就增加了很多开销。

你可以做一些优化，在文章中有概述，但是仍然会有大约 300 毫秒的开销。因此，对于大多数操作来说，WebAssembly 不会比原生 JavaScript 更快——绝对不要把所有网站的 JavaScript 都转换成 WebAssembly！

然而，这并不是 WebAssembly 的真正目的。它的目标是在需要更高性能的网络上实现更丰富的体验，例如机器学习、虚拟现实或游戏。

```
**Want to Connect?**I run a free newsletter providing fortnightly technical book recommendations, including my key takeaways from the books. Interested? [Sign up here!](https://subscribe.technicalbookclub.com/?utm_source=medium&utm_medium=article&utm_campaign=webassemblybenchmark)
```