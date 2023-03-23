# Rust 可执行文件的清理

> 原文：<https://betterprogramming.pub/sanitization-of-rust-executables-dac2744340c>

## 简要介绍在没有事先清理的情况下发布 Rust 可执行文件的危险

![](img/8f627280a67b3386bee915a1c82a6f80.png)

照片由 [Kelvin Han](https://unsplash.com/@kelvinhan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你有没有停下来想一想当你生成一个可执行文件时会发生什么？许多系统路径都存储在里面，包括使用过的库，这些库可以提供关于构建可执行文件的系统的信息。不仅包括路线，还包括关于可能发生的错误的信息或控制台要打印的结构名称。

# 调试信息

在这种情况下，我将展示当您在 Rust 结构中实现 Debug 特性时会发生什么。

注意:所有的编译都使用了`--release`标志。

```
struct PrivacyExample1Struct {
    pub name : String,
    pub content : String,
    pub version : u64
}
#[derive(Debug)]
struct PrivacyExample2Struct {
    pub name : String,
    pub content : String,
    pub version : u64
}
fn main() {
    let obj = PrivacyExample1Struct {
        name : "001".to_string(),
        content : "content1".to_string(),
        version : 123
    };
    println!("PrivacyExample1: {}",obj.name);
    let obj = PrivacyExample2Struct {
        name : "002".to_string(),
        content : "content2".to_string(),
        version : 234
    };
    println!("PrivacyExample2: {:?}",obj);
}
```

无需执行，只需提取文件的字符串:

```
strings -n 12 target/release/privacy.exe  | grep Privacy
001content1PrivacyExample1: 
002content2PrivacyExample2:
PrivacyExample2Structname
```

因此，对于第二个结构体，即实现 Debug 特征的结构体，我们可以看到存储在文件中的结构体的名称。

现在，如果我们用另一个结构来改进我们的例子:

```
#[derive(Debug)]
struct PrivacyExample2_1Struct {
    pub name : String,
    pub content : String,
    pub version : u64
}
let obj = PrivacyExample2_1Struct {
    name : "002.1".to_string(),
    content : "content2.1".to_string(),
    version : 234
};
println!("PrivacyExample2.1: {:?}",obj.name);
```

我们正在使用`PrivacyExample2_1Struct`但没有打印它。所以提取字符串会给我们:

```
strings -n 12 target/release/privacy.exe  | grep Privacy
001content1PrivacyExample1: 
002content2PrivacyExample2:
002.1content2.1PrivacyExample2.1:
PrivacyExample2Structname
```

我们可以看到，编译器知道我们没有使用`PrivacyExample2_1Struct`的调试特性，所以它从编译后的代码中删除了它。

## 主机路由

正如我们提到的，编译后的代码中存储了与编译所在的主机相关的路由:

```
strings -n 12 target/release/privacy.exe | grep ':\\' 
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\legacy.rs8
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\v0.rs
.llvm.C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\lib.rs
Z:\privacy\target\release\deps\privacy.pdb
```

在这种情况下，没有对我的用户`secsamdev`的引用，因为我们没有使用库(存储在`C:\\Users\\secsamdev\\.cargo`)。唯一需要注意的是“Z:\”共享文件夹。

## 序列化信息

我们现在将使用 Serde 库，因此我们现在将在代码中包含与主机相关的路由。

```
[dependencies]
serde_json = "1.0"
serde = { version = "1.0", features = ["derive"] }
```

现在该结构必须实现*序列化*和*反序列化*特征:

```
#[derive(Serialize, Deserialize, Debug)]
struct PrivacyExample3Struct {
    pub name : String,
    pub content : String,
    pub version : u64
}
let obj = PrivacyExample3Struct {
    name : "002".to_string(),
    content : "content2".to_string(),
    version : 234
};
let j = serde_json::to_string(&obj).unwrap();println!("PrivacyExample3: {}", j);
```

和以前一样，*字符串*:

```
strings -n 12 target/release/privacy.exe | grep 'Privacy'
001contenido1PrivacyExample1: 
002contenido2PrivacyExample2: 
002.1contenido2.1PrivacyExample2.1: 
PrivacyExample3: 
PrivacyExample2Structname
```

和寻找系统路线相同:

```
strings -n 12 target/release/privacy.exe | grep ':\\'            
internal error: entered unreachable codeC:\Users\secsamdev\.cargo\registry\src\github.com-1ecc6299db9ec823\serde_json-1.0.81\src\ser.rs
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\legacy.rs
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\v0.rs
.llvm.C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\lib.rs
Z:\privacy\target\release\deps\privacy.pdb
```

要从代码中删除这一行，我们必须编辑“config.toml”。

[](https://github.com/rust-lang/rust/issues/40552) [## 默认情况下，为绝对路径启用- remap-path-prefix 问题#40552 rust-lang/rust

### 根据#40374，一部分隐私信息被驻留在编译后的二进制文件中的路径字符串泄露。至…

github.com](https://github.com/rust-lang/rust/issues/40552) 

```
[build]
rustflags = ["--remap-path-prefix=C:\\Users\\secsamdev\\.cargo=cargo", "--remap-path-prefix=C:\\Users\\secsamdev=home", "--remap-path-prefix=C:\\Users\\secsamdev\\.rustup=rustup", "--remap-path-prefix=Z:\\privacy=workspace"]
```

重新编译代码将为我们提供:

```
strings -n 12 target/release/privacy.exe | grep 'cargo'
internal error: entered unreachable codehome\.cargo\registry\src\github.com-1ecc6299db9ec823\serde_json-1.0.81\src\ser.rs
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\legacy.rsx
C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\v0.rs
.llvm.C:\Users\runneradmin\.cargo\registry\src\github.com-1ecc6299db9ec823\rustc-demangle-0.1.21\src\lib.rs
```

考虑到仍然会有对库的引用:

```
strings -n 12 target/release/privacy.exe | grep '\\src'
AccessErrorcannot access a Thread Local Storage value during or after destructionlibrary\std\src\thread\local.rs
use of std::thread::current() is not possible after the thread's local data has been destroyedlibrary\std\src\thread\mod.rs
library\std\src\io\buffered\linewritershim.rs
library\std\src\io\stdio.rs
stdoutlibrary\std\src\io\mod.rs
/rustc/7737e0b5c4103216d6fd8cf941b7ab9bdbaace7c\library\core\src\char\methods.rsP
:.assertion failed: self.is_char_boundary(new_len)/rustc/7737e0b5c4103216d6fd8cf941b7ab9bdbaace7c\library\alloc\src\string.rs
library\std\src\panic.rs
library\std\src\path.rs
...
```

## 反序列化信息

我们已经检查了序列化发生了什么，现在是反序列化:

```
let obj2 : PrivacyExample3Struct = serde_json::from_str(&j).unwrap();
```

`strings`命令将为我们提供:

```
strings -n 12 target/release/privacy.exe | grep 'Privacy'
struct PrivacyExample3Struct with 3 elements
001contenido1PrivacyExample1: 
002contenido2PrivacyExample2: 
002.1contenido2.1PrivacyExample2.1: 
PrivacyExample3: 
PrivacyExample2Structname
struct PrivacyExample3Structcalled `Result::unwrap()` on an `Err` value
```

注意，我们可以看到“struct privacy example 3 struct with 3 elements ”,它提供了关于我们的代码如何在内部工作的信息。

# 结论

正如我们所看到的，许多引用隐藏在可执行文件中:我们编译代码的机器，我们使用的库，它甚至可以给出我们的代码如何工作的线索。

出于这个原因，有必要时刻牢记我们正在使用的工具，并深入了解围绕生锈的所有工具的来龙去脉。