# 用 TinyGo WASM 处理错误

> 原文：<https://betterprogramming.pub/error-handling-with-tinygo-wasm-883533bb8b77>

## 我处理 WASM 错误的方法

![](img/7eb1115cfddcbe76a5caa1483fafd143.png)

迈克尔·盖革在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

“错误处理”是由预测、检测和解决应用程序错误、编程错误或通信错误组成的过程本质上，它是设计程序来解释错误的过程。Go 开发者以不同的方式处理他们的错误。我见过的一种方法是使用函数`log.Fatal`。该函数将错误写入日志，并以错误代码 1 终止程序。

另一种方法是返回错误。这对于库来说很常见，因为它们允许用户实现它来决定如何处理错误。最近我正在用 TinyGo 编写一个 WASM 应用程序，我需要一种有效的方法来处理错误。在这篇文章中，我将详细介绍我实现错误处理的方法。

# TinyGo WASM 设置

首先，我创建了一个空目录。我将我终端的活动目录切换到上述目录。我运行以下命令来复制一个 JavaScript 文件。这个文件是帮助浏览器理解 WASM 二进制文件所必需的:

```
cp $(tinygo env TINYGOROOT)/targets/wasm_exec.js wasm_exec.js
```

我继续将名为`index.html`的文件添加到目录中。该文件的内容将包含加载 WASM 依赖项所需的最少代码。该文件的内容如下:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Go wasm</title>
  </head> <body>  
    <script src="wasm_exec.js"></script>  
  </body>
</html>
```

我将为`index.html`添加一个脚本标签，这个标签将负责加载并运行我的 WASM 二进制文件。脚本标记将包含以下代码:

```
<script>if (!WebAssembly.instantiateStreaming) {
        // polyfill
        WebAssembly.instantiateStreaming = async (resp, importObject) => {
          const source = await (await resp).arrayBuffer();
          return await WebAssembly.instantiate(source, importObject);
        };
      } const go = new Go(); let  inst; WebAssembly.instantiateStreaming(fetch("lib.wasm"), go.importObject).then(
        async (result) => {
          inst = result.instance;
          go.run(inst);
        }
      );</script>
```

脚本标签将下载二进制文件并执行它。产生的实例被分配给变量`inst`。`inst`将房子导出功能。接下来，我将开始编写我的 Go 代码。最后一步是将名为`main.go`的文件添加到新目录中。该文件将包含以下内容:

```
package mainfunc main(){}
```

为了将我的 Go 代码编译成 WASM，我运行了以下命令:

```
tinygo build -o lib.wasm -target wasm ./main.go
```

为了访问`index.html`，我启动了一个文件服务器并提供目录服务。

# 我的基本功能

我将从定义一个我希望 TinyGo 导出到浏览器的函数开始。我将把这个函数添加到我的`main.go`文件中。该函数由以下代码表示:

```
// Must add the following comment for it
// to work.
//export Add
func Add(x, y int) int { return x + y
}
```

让函数中的最后一个注释是`export $function_name`很重要，其中`function_name`是函数的名称。这将把您的函数导出到浏览器。可以使用以下 JavaScript 代码访问该函数:

```
inst.exports.Add
```

现在，为了把一些东西硬塞进去(s/o to [Robert Weber](https://medium.com/u/fd9b4962aa25?source=post_page-----883533bb8b77--------------------------------) ，假设这个函数的 x 参数必须大于 1。如果 x 不是，它应该返回一个错误。该错误还应该与 JavaScript 的错误处理机制兼容，即 try、catch。用 TinyGo WASM 将错误返回给 JavaScript 并不那么简单。

在试用 TinyGo 时，我注意到当我从 Go 函数向 JavaScript 返回一个字符串时，它在浏览器上显示为一个数字。因为字符串有这种行为，所以我没有尝试让函数返回一个错误对象。我的第一个想法是看看`log.Fatal`是否是在浏览器上抛出异常的好方法。

# 确定处理错误的适当方法的过程

我将更新我的函数`Add`来检查 x 是否小于 2。如果是这样，`log.Fatal`就会被援引。下面是产生的`Add`函数:

```
// Must add the following comment for it
// to work.
//export Add
func Add(x, y int) int { if x < 2 {
   log.Fatal("x must be greater than 1.")
 } return x + y
}
```

是时候测试上面的代码是否会抛出异常了。

![](img/bde2b582e22af8b8b091fdd2b3c71fce.png)

代码确实会生成一个异常。然而，例外是援引`log.Fatal`的副产品。我无法捕获错误消息，因为它记录在控制台中。另一种方法是声明一个 JavaScript 函数来抛出异常。理论上，Go 函数和它调用的 JavaScript 函数将在同一个线程上运行。所以抛出一个异常应该会中止执行并返回一个正确的错误消息。

首先，我将在我的 Go 程序中导入一个 JavaScript 函数。我会通过更新`index.html`来做到这一点。我将在初始化常量`Go`后添加以下 JavaScript 代码:

```
go.importObject.env = {
     ...go.importObject.env ,
     'main.throw': (e) => {
          console.log(e)
          throw e
      }
 }
```

如您所见，定义的 JavaScript 函数将在调用时抛出错误。为了让它完全工作，我必须在我的`main.go`文件中定义一个无主体函数。为此，我将在文件中添加以下代码:

```
func throw(e string)
```

这将允许我的 Go 程序访问并执行该函数。我将更新函数`Add`来使用这个函数抛出一个错误。结果函数如下:

```
// Must add the following comment for it
// to work.
//export Add
func Add(x, y int) int { if x < 2 { err := "X must be greater than 1!" 
   throw(err)
 } return x + y
}
```

下面是实际运行的代码:

![](img/14e510b8bc6fcec1bcd62739ef79e435.png)

如您所见，从函数`Add`中调用`throw`正在工作。但是，整数作为参数被传递。同样，这是因为 TinyGo 只与浏览器进行数字通信。传递的数字是一个内存地址。准备好，伙计们，我要开始挖掘记忆了。

# 访问微小的内存

首先，我需要更新我的 Go 代码。我需要一个函数来获取一个字符串，并将其声明为一个`byte`数组。一旦断言，我需要将第一个`byte`的地址以及字符串的长度传递给我的 JavaScript 函数。执行此操作的 Go 函数定义如下:

```
func throwError(e string, l int){
 firstByte := &(([]byte)(e)[0])
 throw(firstByte,l)
}
```

第一行将断言字符串为字节，然后返回一个指向`byte`数组第一个索引的指针。我将更新 throw 以接受另一个参数。该参数将被称为`l`。它是错误字符串的长度。下面是函数`throw`的更新定义，在 Go 端:

```
func throw(e *byte, l int)
```

我会更新`Add`用`throwError`代替`throw`。这是函数更改后的样子:

```
// Must add the following comment for it
// to work.
//export Add
func Add(x, y int) int { if x < 2 {
   err := "X must be greater than 1!" 
   throwError(err, len(err))
 } return x + y
}
```

我将继续更新`index.html`中函数的 JavaScript 定义。函数 throw 的新 JavaScript 定义如下:

```
go.importObject.env = {
    ...go.importObject.env ,
     'main.throw': (e, length) => {
          console.log(e)
          throw getString(e, length)  
     },
}
```

`getString`将是负责从内存中获取字符串并将其解码为 UTF-8 字符串的函数。以下代码将执行此操作:

```
function getString(addr, length){
      const memory = inst.exports.memory; const extractedBuffer = new Uint8Array(memory.buffer, addr, length); const str = new TextDecoder("utf8").decode(extractedBuffer);

      return str
 }
```

常量`memory`代表我的程序的内存。`extractedBuffer`将是从程序的内存缓冲区中提取的字节子数组。`addr`将指定要提取的字节的起始地址。`length`将决定在起始地址后提取多少字节，这应与错误消息的长度相匹配。我将使用 TextDecoder 将字节转换为字符串并返回结果。以下是这种结合的结果:

![](img/6d604c4e6fa054a1d4833eb4916296d6.png)

最后，我的程序抛出一个字符串，我可以将它显示为一条错误消息。

# 结论

TinyGo 有一个令人印象深刻的 WASM 实现。我无法理解在 Go 和浏览器之间共享功能是多么容易。唯一的限制是它只能用数字和浏览器交流。这可以通过访问内存来纠正，如上面的例子所示。

从 Go 函数中抛出异常将导致该函数的执行暂停。它还将防止静默错误，并消除检查返回的函数数据是否有任何额外错误的需要。最后，我觉得 WASM 库的错误处理应该在一定程度上兼容浏览器。兼容，因为其他 JavaScript 组件可以可靠地处理库的错误。

# 附加链接

[](https://github.com/tinygo-org/tinygo) [## GitHub - tinygo-org/tinygo:小地方用的 go 编译器。微控制器、网络组装…

### TinyGo 是一个 Go 编译器，用于微控制器、WebAssembly (Wasm)和命令行等小地方

github.com](https://github.com/tinygo-org/tinygo) [](https://www.techopedia.com/definition/16626/error-handling) [## 什么是错误处理？-来自 Techopedia 的定义

### 错误处理是指软件应用程序中出现错误时的响应和恢复过程…

www.techopedia.com](https://www.techopedia.com/definition/16626/error-handling) [](https://www.wasm.builders/k33g_org/an-essay-on-the-bi-directional-exchange-of-strings-between-the-wasm-module-with-tinygo-and-nodejs-with-wasi-support-3i9h) [## 一篇关于 Wasm 模块(带有 TinyGo)和…之间双向交换字符串的文章

### 免责声明:我更喜欢 JavaScript 和 Java，我以前没有使用过指针...用 tinygo 标记…

www.wasm.builders](https://www.wasm.builders/k33g_org/an-essay-on-the-bi-directional-exchange-of-strings-between-the-wasm-module-with-tinygo-and-nodejs-with-wasi-support-3i9h)  [## medium _ examples/tinygo-http at main cheikh shift/medium _ examples

### 中型文章的代码示例。在 GitHub 上创建一个帐户，为 cheikhshift/medium_examples 开发做贡献。

github.com](https://github.com/cheikhshift/medium_examples/tree/main/tinygo-http)  [## 数据类型

### TinyGo 对某些数据类型使用了与标准 Go 不同的表示。字符串被编码为{ptr，len}…

tinygo.org](https://tinygo.org/docs/concepts/compiler-internals/datatypes/)