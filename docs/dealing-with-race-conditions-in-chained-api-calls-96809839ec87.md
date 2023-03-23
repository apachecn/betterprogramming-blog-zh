# 使用 Swift 处理链式 API 调用中的竞争条件

> 原文：<https://betterprogramming.pub/dealing-with-race-conditions-in-chained-api-calls-96809839ec87>

## 使用完成处理程序、递归和信号量示例进行解释

![](img/51ad5965726ae0cae4689a162fd589a5.png)

在本文中，我将分享一些我在 Nano Challenge 2 应用程序 **Colorio 中处理链式 API 调用的经验。**这里我将谈谈我处理它们的方法，以及可以使用的替代方法。

# 关键词

*   完成处理程序
*   递归
*   信号灯

# 让我们从一个基本的 API 调用开始

你有没有想过使用一个 API 并把它们的数据加载到一个表格视图中？通常你会在你的视图控制器中这样做:

从 API 获取的 JSON 数据示例:

```
[
  {
    "id": 1,
    "author": "Gregorius Albert",
    "content": "This is my first tweet"
  },
  {
    "id": 2,
    "author": "Taylor Swift",
    "content": "It's August baby"
  },
  {
    "id": 3,
    "author": "Justin Bieber",
    "content": "What's about the Baby song thing?"
  }
]
```

从 API 获取数据并加载到表视图的代码是:

```
let url = URL(string: Helper.BASE_URL)!
var request = URLRequest(url: url)
request.httpMethod = "GET"

URLSession.shared.dataTask(with: request){ (data, response, error) in

    let json = try! JSONSerialization.jsonObject(with: data!) as! [[String:Any]]

    for result in json {
        let author = result["author"] as! String
        let content = result["content"] as! String
        let tweet = Tweet(author: author, content: content)
        self.tweets.append(tweet)
    }

    DispatchQueue.main.async {
        self.tableView.reloadData()
    }

}.resume()
```

如果 API 可以批量或集合地返回数据，那么这个解决方案是可行的。这就像在 SQL 数据库中做一个`SELECT *`。

# 使用不提供批量数据的公共 API

但是如果您使用的是不提供批量数据的公共 API 呢？有些 API 只允许你根据一个参数获取一个数据。请看下面的例子。

API URL: `[https://www.thecolorapi.com/id?hex=E62028](https://www.thecolorapi.com/id?hex=E62028)`

返回的结果:

```
{
  "hex": {
    "value": "#E62028",
    "clean": "E62028"
  },
  "rgb": {
    "fraction": {
      "r": 0.9019607843137255,
      "g": 0.12549019607843137,
      "b": 0.1568627450980392
    },
    "r": 230,
    "g": 32,
    "b": 40,
    "value": "rgb(230, 32, 40)"
  },
  "name": {
    "value": "Alizarin Crimson",
    "closest_named_hex": "#E32636",
    "exact_match_name": false,
    "distance": 349
  }
}

// Some value from the API have been deleted to shorten this article
```

在这里，我从 URL 中给出的参数`[E62028](https://www.thecolorapi.com/id?hex=E62028)`中获取值。但是我需要一次得到多种颜色的数据。我该怎么做呢？

# 我所尝试的

嗯，很多人可能认为“*只需循环 API 调用“*。从技术上讲，你可以像这样运行 API 调用。让我们尝试循环 5 个十六进制代码，并从数组中的每个十六进制代码获取颜色名称。

```
let hexArr = ["FFFFFF", "000000", "FF0000", "00FF00", "0000FF"]
// White, Black, Red, Green, Blue

for hex in hexArr {
	fetchAPI(hexParam: hex)
}

func fetchAPI(hexParam: String) -> Void {

    let url = URL(string: "https://www.thecolorapi.com/id?hex=\(hexParam)")!
    var request = URLRequest(url: url)
    request.httpMethod = "GET"

    URLSession.shared.dataTask(with: request) { data, response, error in

        let json = try! JSONSerialization.jsonObject(with: data!) as! [String:Any]

        let name = json["name"] as! [String:Any]
        let nameValue = name["value"] as! String

        DispatchQueue.main.async {
            print(nameValue)
        }

    }.resume()

}
```

我们希望得到的结果是:

```
White 
Black 
Red 
Green 
Blue
```

但是，它返回了:

```
Black 
White 
Green 
Red 
Blue
```

我们把所有的颜色都弄对了。但不是按顺序…如果您尝试再次运行该代码，它将以不同的顺序返回。

💡这种情况称为竞争情况。

竞争条件是指同时执行多个任务的情况。尽管我们已经在数组的序列中调用了`fetchAPI()`，但是每个 API 调用的完成时间都不相同。

我们知道，每一个叫做`URLSession`的都是异步运行的。因此，如果您需要循环调用有序，您必须操纵 API 调用。

# 操纵 API 调用

这里，`URLSession`将在`URLSession`关闭括号旁边的`.resume()`被调用后异步运行另一个任务。所以理论上，你需要在 URLSession 的右括号前调用`fetchAPI()`。

```
URLSession.shared.dataTask(with: request) { data, response, error in

    let json = try! JSONSerialization.jsonObject(with: data!) as! [String:Any]

    let name = json["name"] as! [String:Any]
    let nameValue = name["value"] as! String

    DispatchQueue.main.async {
        print(nameValue)
    }

    // MARK: The next API call should be here

}.resume()
```

理论上，实现这一点最简单的方法是使用递归。因为我们可以在指定的行中再次调用函数。

# 递归方法

我个人在我的 Nano Challenge 2 应用程序 Colorio 中使用了这种方法。尽管这种方法有点狭隘，因为它是纯逻辑的，并且没有利用像队列和信号量这样的 Swift 特性。性能也可能是一个问题，因为递归中的内存管理并不是最好的。

我是这样实现递归的:

*   设置何时停止递归的基本条件
*   我们可以使用简单的`if-else`或`guard`
*   我们需要添加一个`index`参数来标记何时停止
*   在右括号前递归该函数

💡这里，函数将在 5 次函数调用后停止运行，这是数组内容的计数

```
let hexArr = ["FFFFFF", "000000", "FF0000", "00FF00", "0000FF"]
// White, Black, Red, Green, Blue

// Defining when the recursion needs to stop based on the array count
let arrayCount = hexArr.count

// Calling the function
fetchAPI(index: 0) 

func fetchAPI(index: Int) -> Void {

    // Guarding the function to stop after it reaches the array count
    guard index < arrayCount else { return }

    let url = URL(string: "https://www.thecolorapi.com/id?hex=\(hexArr[index])")!
    var request = URLRequest(url: url)
    request.httpMethod = "GET"

    URLSession.shared.dataTask(with: request) { data, response, error in

        let json = try! JSONSerialization.jsonObject(with: data!) as! [String:Any]

        let name = json["name"] as! [String:Any]
        let nameValue = name["value"] as! String

        DispatchQueue.main.async {
            print(nameValue)
        }

        // Calling the recursion
        fetchAPI(index: index+1)

    }.resume()

}
```

如你所见，结果总是一致的。但是它们会一个接一个地缓慢加载。

```
White 
Black
Red
Green
Blue
```

让我们尝试用另一种方式来实现顺序调用，信号量。

# 信号量方法

在我们继续代码实现之前，这里有一些关于信号量的理论，来自 [Roy Kronenfeld 的文章](https://medium.com/@roykronenfeld/semaphores-in-swift-e296ea80f860)以帮助您快速了解:

> 信号量由一个线程队列和一个计数器值(Int 类型)组成。
> 
> 信号量使用线程队列来跟踪 FIFO 顺序中等待的线程(*一旦共享资源可用，第一个进入队列的线程将是第一个访问共享资源的线程*)。
> 
> 信号量使用计数器值来决定线程是否应该访问共享资源。当我们调用 signal()或 wait()函数时，计数器值会发生变化。
> 
> **那么，什么时候应该调用 wait()和 signal()函数呢？**
> 
> ——来源:[罗伊·克罗南菲尔德](https://medium.com/@roykronenfeld/semaphores-in-swift-e296ea80f860)

# 代码实现

下面是我们初始化信号量所需的步骤:

*   `let semaphore = DispatchSemaphore(value: 1)`
*   根据队列数量为 value 参数赋值。在这里，因为我们希望按顺序执行，所以需要逐个对数组进行排队。因此将参数赋值为 1。`DispatchSemaphore(value: 1)`
*   将 API 调用封装到一个函数中。这里命名为`fetchAPI(hexParam: String)`
*   创建一个调用函数的循环
*   在调用`fetchAPI(hexParam: String)`之前，分配`semaphore.wait()`初始化队列
*   分配`semaphore.signal()`继续`.resume()`之前的队列

```
let hexArr = ["FFFFFF", "000000", "FF0000", "00FF00", "0000FF"]
// White, Black, Red, Green, Blue

// Assign the semaphore variable
let semaphore = DispatchSemaphore(value: 1)

for hex in hexArr {
    semaphore.wait() // Initiate the queue
    fetchAPI(hexParam: hex)
}

func fetchAPI(hexParam: String) -> Void {

    let url = URL(string: "https://www.thecolorapi.com/id?hex=\(hexParam)")!
    var request = URLRequest(url: url)
    request.httpMethod = "GET"

    URLSession.shared.dataTask(with: request) { data, response, error in

        let json = try! JSONSerialization.jsonObject(with: data!) as! [String:Any]

        let name = json["name"] as! [String:Any]
        let nameValue = name["value"] as! String

        DispatchQueue.main.async {
            print(nameValue)
        }

        semaphore.signal() // Continue the queue

    }.resume()

}
```

# 完成处理程序

如果我们想在一个 API 调用中重用这个函数呢？我们怎样才能在函数中删除`semaphore.signal()`，同时还能像以前一样在链接 API 调用时传递信号呢？

对于这种用法，我们可以使用完成处理程序。

💡完成处理程序允许您将某些函数或代码行嵌入到函数中的任何位置，而不仅仅是在函数调用的末尾。

在处理异步任务时，我们需要在参数中使用`@escaping`来等待异步任务完成，然后运行我们的完成处理程序。如果我们不使用`@escaping`,处理程序将立即运行，而不会在运行处理程序之前等待异步任务完成。

因为我们将`finished()`处理程序放在一个异步的 URLSession 闭包内，没有`@escaping`关键字，Xcode 将返回一个错误:

```
expression failed to parse:
error: MyPlayground.playground:21:47: error: escaping closure captures non-escaping parameter 'finished'
    URLSession.shared.dataTask(with: request) { data, response, error in
                                              ^

MyPlayground.playground:15:47: note: parameter 'finished' is implicitly non-escaping
func fetchAPIUsingSemaphore(hexParam: String, finished: () -> Void) -> Void {
                                              ^

MyPlayground.playground:32:9: note: captured here
        finished()
        ^
```

因此，下面是实现示例:

```
myFunction() {
    // Code to inject to the function
}

func myFunction(finished: @escaping() -> Void) -> Void {

    URLSession.shared.dataTask(with: request) { data, response, error in

        // Any process handling the data

        finished() // Put where you want any code injected to the function

    }.resume()

}
```

这里，我们想在`fetchAPI`函数恢复之前注入`semaphore.signal()`。

因此，我们可以创建一个闭包，并将信号放在闭包内，当代码到达`finished().`时，闭包就会运行

对于单个 API 调用，只需调用函数并给它一个空闭包。

```
let hexArr = ["FFFFFF", "000000", "FF0000", "00FF00", "0000FF"]
// White, Black, Red, Green, Blue

let semaphore = DispatchSemaphore(value: 1)

// Looping and chaining the API Call
for hex in hexArr {
		semaphore.wait()
    fetchAPI(hexParam: hex) {
				// Injecting semaphore.signal to the function
        semaphore.signal()
    }
}

// Single API Call. Just give an empty closure
fetchAPI(hexParam: "FAFAFA"){}

func fetchAPI(hexParam: String, finished: @escaping() -> Void) -> Void {

    let url = URL(string: "https://www.thecolorapi.com/id?hex=\(hexParam)")!
    var request = URLRequest(url: url)
    request.httpMethod = "GET"

    URLSession.shared.dataTask(with: request) { data, response, error in

        let json = try! JSONSerialization.jsonObject(with: data!) as! [String:Any]

        let name = json["name"] as! [String:Any]
        let nameValue = name["value"] as! String

        DispatchQueue.main.async {
            print(nameValue)
        }

        finished()

    }.resume()

}
```

瞧，竞争条件已经处理好了，您的 API 请求将按顺序获取。

感谢阅读。

Colorio 是一个调色板生成器，为你的完美色彩日提供日常建议。我们建议从你的基础上选择完美的色调和巧妙的组合，增加个人风格的感觉。然后，您可以选择您需要的产品——您的底座，或者我们的精选产品。

*这个应用程序主要由两个 API 提供支持，你可以在这里找到应用程序的源代码—*[*GitHub—gal-Bert/Colorio*](https://github.com/gal-bert/Colorio)*。*

*原载于*[*http://github.com*](https://gist.github.com/gal-bert/474cadda5664bcbc41622fd84c9c252d)*。*