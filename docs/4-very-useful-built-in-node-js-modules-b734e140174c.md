# 4 个非常有用的内置 Node.js 模块

> 原文：<https://betterprogramming.pub/4-very-useful-built-in-node-js-modules-b734e140174c>

## 深入了解 perf_hooks、fs、os 和 URL

![](img/851a479535d7e4300e385ee7babb5827.png)

来源: [Unsplash](https://unsplash.com/photos/XTblNijO9IE)

## 第二部分:

[](https://medium.com/javascript-in-plain-english/3-pretty-cool-built-in-node-js-modules-c2c3283276e6) [## 3 个非常酷的内置 Node.js 模块

### 让我们使用 Node.js 读取用户输入，执行终端命令并运行一个 JS 虚拟机！

medium.com](https://medium.com/javascript-in-plain-english/3-pretty-cool-built-in-node-js-modules-c2c3283276e6) 

# 什么是内置模块？

Node.js 有一些所谓的*内置模块*。这意味着，尽管这些模块像我们可以安装的其他模块一样工作，例如，通过 npm，然后包括使用`require`或`import`语法，顾名思义，内置模块已经安装，因此总是可用的。

当然，它们是由 Node.js 背后的开发团队维护的，所以一切都是最新的，我们可以放心地使用这些模块。

不推荐使用的模块特性可以在官方文档中识别出来。

所以，有充分的理由来看看这些模块，并在我们的下一个项目中使用它们。

# 1.性能计时 API (perf_hooks)

在本模块中，我们提供了各种功能来检查 Node.js 应用程序的性能。

该 API 也在现代浏览器中实现，提供非常精确的结果，因此非常适合作为分析您自己的应用程序的首选。

让我们看一个例子，在这个例子中，我们检查了读取一个大文件所需的时间，并将其与读取一个小文件进行了比较。这个大文件包含大约 3000 行 Lorem Ipsum *，*这个小文件只有 20 行 Lorem Ipsum。

如果你运行几次 Node 应用程序，你会发现用`readFileSync`函数读取大文件平均要花更长的时间。

我们的节点应用程序测量阅读持续时间

为此，我们使用`performance.mark`函数，用这些标记包围我们想要检查其执行的实际代码。

因此，我们总是设置一个*开始点*和一个*结束点*，然后通过`performance.measure`函数，我们可以将整个事情存储在性能时间线中，这样我们就可以通过`PerformanceObserver`给出结果。

measure 函数的第一个参数是名称，另外两个是起点和终点的名称。

结果应该是这样的:

当然，时代将取决于你的机器。

# 2.文件系统模块(fs)

我想大家都已经知道了`fs`模块，代表*文件系统，*并允许访问文件系统。顾名思义。

你们大多数人应该从第一次使用 Node.js 时就已经知道了，而且`fs`模块的基本功能在几乎所有的编程语言中都是类似的。

但是有一些不那么为人所知的很酷的功能，我现在就给大家介绍一下。

## **检查是否是文件夹**

```
const fs = require(‘fs’)*// will return false, no directory* fs.statSync(‘./index.html’).isDirectory()
```

## **打印一个目录下的所有文件**

```
fs.readdir(‘./test-dir’, (err, files) => {
  files.forEach(file => {
    console.log(file)
  })
})
```

## **观察目录变化并给出变化类型**

```
fs.watch(‘./test-dir’, (eventType, filename) => {
  if (filename) console.log(filename, eventType)
})
```

## fs 模块的基本功能

**读取文件并打印出文件内容**

```
fs.readFile(‘./note.txt’, ‘utf8’, (err, data) => {
  console.log(data)
})
```

如果您省略了解码的参数，在我们的示例中，“T1”，默认情况下，内容将作为缓冲区给出。

**向文件中添加内容**

```
fs.appendFile(‘./note.txt’, ‘This is new!’, err => {
  if (err) throw err
  console.log(‘file changed’)
})
```

**创建一个新文件，向其中写入内容/清除文件**

```
fs.writeFile(‘./note.txt’, ‘’, err => {
  if (err) throw err
  console.log(‘file cleared’)
})
```

这个函数的第二个参数是应该写入文件的内容。在我们的例子中，我们让它为空，这样如果给定的文件存在，它将完全为空。

如果它不存在，它将被创建，但也将保持为空。因此，这个函数不适合向文件中添加内容，因为它会覆盖以前添加的所有内容。

# 3.操作系统模块(os)

这个模块可以输出 Node.js 当前运行的系统的各种信息。例如，它可以用来监控服务器的性能。

这里有几个有用的函数，我直接转换了它们的输出，这样它们对我们来说更容易理解。

通过将`os.totalmem()`的值除以三次，这给出了 RAM 的确切大小，应该会输出一个非常平滑的数字。在我的 MacBook Pro 上，正好是“16”，因为它有 16g 的内存。

为了确认`os.freemem()`工作正常，你可以打开几个 Chrome 标签，运行脚本，然后再次关闭它们，然后再次运行脚本。你会看到不同之处。

# 4.url 模块(URL)

由于 Node.js 通常用于 web 应用程序的后端，使用 URL 实际上是不可避免的。

这里介绍的内置模块可以通过提供处理 URL 的标准函数来简化我们的工作。

## **将 URL 拆分成所有重要信息，如主机、协议和端口**

```
const url = require("url")url.parse(‘https://medium.com/@louispetrik')
```

结果:

```
Url {
 protocol: ‘https:’,
 slashes: true,
 auth: null,
 host: ‘medium.com’,
 port: null,
 hostname: ‘medium.com’,
 hash: null,
 search: null,
 query: null,
 pathname: ‘/@louispetrik’,
 path: ‘/@louispetrik’,
 href: ‘[https://medium.com/@louispetrik'](https://medium.com/@louispetrik')
}
```

## **将基本网址和精确路径合并成一个正确的网址**

```
url.resolve(‘https://medium.com/', ‘/@louispetrik’)
```

结果:`[https://medium.com/@louispetrik](https://medium.com/@louispetrik)`

## **输出 URL 的搜索参数值**

```
let SearchURL = new URL(‘https://example.org/?product=car')
console.log(SearchURL.searchParams.get(‘product’))
```

结果:`car`

# 结论

今天就到这里，希望你学到了新的有用的东西。感谢阅读！