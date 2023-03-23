# Rust 中如何在 JavaScript 和 WASM 之间传递数组

> 原文：<https://betterprogramming.pub/how-to-pass-arrays-between-javascript-and-wasm-in-rust-9f2eeee7f2b6>

## 探索这个友好的界面

![](img/f15cf49542766526ff8831db3dae28b6.png)

照片由[Jakob s by](https://unsplash.com/@jakobsoeby?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

由于 WASM 的内存模型，传递数组(和嵌套数组)可能是相当具有挑战性的，如果你试图做安全的事情，因为 wasm 内存存储其值作为标量类型的数组缓冲区。

然而，幸运的是，当 Javascript 和 WebAssembly 之间交换数据时，我们拥有提供友好界面的`wasm-bindgen`(和`js-sys`)机箱。

我还应该提到，无论何时使用 WebAssembly，如果您将性能作为开发目标之一，就应该避免在 JS 和 WASM 二进制文件之间交换数据。

通常，我们希望 WASM 在其线性内存中处理繁重的计算、查询和数据，并且只返回少量的计算数据。

Wasm 函数以一个`ArrayBuffer`的形式接受并返回标量值。幸运的是`wasm-bindgen`在很多方面都有帮助，但它并不是万能的。

让我们看看如何使用类型传递(或返回)数组。

# 设置

因为我们正在使用 WebAssembly，更确切地说，它是针对一个 web 构建的(它意味着与 js 一起工作)，我们能得到的最好的设置是通过使用`wasm-bindgen`和它的程序宏。

你可以看看[这篇文章](/get-started-with-wasm-in-rust-2347056bab4)更详细更全面地介绍设置和 WASM+Rust，但这里有一个小的回顾:

## 添加所需的依赖项

将这两个依赖项添加到您的`Cargo.toml`配置中(确保您使用的是最新版本):

```
wasm-bindgen = "0.2.82"
js-sys = "0.3.59"
```

## WASM 优化

您还应该在配置中添加以下内容:

```
[profile.release]
opt-level = "z"
overflow-checks = true
debug = 0
strip = "symbols"
debug-assertions = false
panic = "abort"
codegen-units = 1
lto = true[profile.dev]
opt-level = 0
panic = 'abort'
```

这些将在编译代码时添加一些设置，这对代码大小也是有益的(例如，在惊慌收缩代码大小时中止)。

最后，你需要告诉编译器编译将产生一个动态系统库(`"cdylib"`)和统计链接的可执行文件(`"rlib"`内容被插入到可执行文件中)。

```
[lib]
crate-type = ["cdylib", "rlib"]
```

# 接收阵列

正如我通过导入`js_sys`依赖项所暗示的，我们将使用`js_sys::Array`类型。

假设我们有一个 Rust 函数`my_funct`，它接受一个数组作为参数。这是使用`js_sys::Array`类型时的样子:

```
#[wasm_bindgen] 
pub fn my_funct(     
    my_arr: Array,
)
```

这是一个非常简单的构造，几乎不需要我们付出任何努力。然而，努力在于如何对付这种`Array`式的。比如不能有嵌套数组，值类型未知(因为 JS 没有强类型)。

## 嵌套数组

正如我提到的，`Array`类型不支持嵌套数组。那是因为你不能将`Array`的一个元素转换成一个向量。

你有几个选择。您可以传递`[["hey"], ["tdep"]]`，然后将数组中的元素解析成一个字符串，使用:

```
my_arr.get(index).as_string().unwrap()
```

然后用类似`[serde_json](/how-to-work-with-json-in-rust-35ddc964009e)`的库反序列化字符串。

另一个选项是将多维数组作为一维数组传递，然后在 Rust 上将其构建为嵌套数组，这是我更喜欢的选项，因为它允许我在不使用额外依赖项的情况下使用嵌套数组。

如果你知道数组的长度，这是相当容易的。假设你知道你需要一个四个元素的数组，然后用两个数组构造一个嵌套的数组，看起来像这样`[["el1", "el2"], ["el3", "el4"]]`。如果是这种情况，您可以使用以下方法轻松解决问题:

```
[
  [     
  &arr.get(0).as_string().unwrap(),
  &arr.get(1).as_string().unwrap(),
  ],
  [  
  &arr.get(2).as_string().unwrap(),
  &arr.get(3).as_string().unwrap(),
  ]
]
```

但是，您可能正在处理向量，并让 JS 在编译时传递一个未知长度的数组，您仍然必须构建一个嵌套数组，其中每个元素都是包含两个元素的数组。

我决定手动实现这个算法，因为它很容易编写:

上面的代码循环我们的`my_arr`数组，并每两步将一个包含两个元素的新数组推入`my_arr_vec`向量。

这意味着只要我们收到一个长度为偶数的数组，我们的循环就会工作(因为我们想要构建一个内部包含两个元素的数组的向量)。

在这篇简单的文章中，我们已经看到了如何使用`js_sys::Array`类型将数组从 JS 安全地传递到 Rust，这要感谢`wasm_bindgen`和`js_sys`箱子。我还应该提到，如果需要的话，`Array`类型也可以传递回 Javascript。但是，请记住，如果您需要考虑性能，您应该将 js 限制为在 WASM 代码完成必要的计算后，只接收它的简洁输出。

*感谢阅读！*